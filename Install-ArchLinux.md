  # Installing Arch Linux ARM
  
  1. Locate the disk you will be using
		```
		sudo fdisk -l
		```
  2. Select the drive we will be using for Arch (Where X is your drive letter)
		```
		sudo fdisk /dev/sdX
		```
  3. Type the following letters and numbers to partition the drive
		```
		Command:		n
		Partition number:	1
		First sector:		[ENTER]
		Last sector:		+512M
		Remove signature:	Y
		Command:		n
		Partition number:	[ENTER]
		First sector:		[ENTER]
		Last sector:		[ENTER]
		Remove signature:	Y
		Save changes:		w
		Exit:			q
		```
  4. Format the new partitions
		```
		sudo mkfs.vfat /dev/sdX1
		sudo mkfs.ext4 /dev/sdX2
		```  
  5. Install bsdtar & wget
		
		Debian/Ubuntu:
		```
		sudo apt install libarchive-tools wget
		```
		Fedora:
		```
		sudo dnf install bsdtar wget
		```
		Void:
		```
		sudo xbps-install bsdtar wget
		```
		Arch:
		```
		sudo pacman -Sy bsdtar wget
		```
		openSUSE:
		```
		sudo zypper install wget bsdtar
		```
  6. Mount the target drive
		```
		sudo mkdir /mnt/chroot
		sudo mount /dev/sdX2 /mnt/chroot
		sudo mkdir -p /mnt/chroot/boot
		sudo mount /dev/sdX1 /mnt/chroot/boot
		```
  7. Download and extract the tarball to the mounted drive as root [Do not use sudo]
		```
		su -
		wget http://os.archlinuxarm.org/os/ArchLinuxARM-aarch64-latest.tar.gz
		bsdtar -xpf ArchLinuxARM-aarch64-latest.tar.gz -C /mnt/chroot
		exit
		```
  8. Mount pseudo filesystems
		```
		cd /mnt/chroot
		sudo mount -t proc /proc proc
		sudo mount --make-rslave --rbind /sys sys
		sudo mount --make-rslave --rbind /dev dev
		sudo mount --make-rslave --rbind /run run
		```
  9. Chroot into Arch Linux install
		```
		PS1="(arch chroot) # " sudo chroot /mnt/chroot /bin/bash
		```
  10. Set new root password
		```
		passwd
		```
  11. Edit resolv.conf
		```
		nano /etc/resolv.conf
		```
		```
		"nameserver 8.8.8.8"
		```
  12. Set the hostname
		```
		nano /etc/hostname
		```
  13. Initiate Pacman and needed packages 
		```
		pacman-key --init
		pacman-key --populate archlinuxarm
		pacman -Sy base-devel wget python3 bc pahole rsync
		```
  14. Download and extract the kernel [Modify to your needs]
		```
		mkdir /root/kernelbuild && cd /root/kernelbuild
		wget https://cdn.kernel.org/pub/linux/kernel/v6.x/linux-6.2.2.tar.xz
		tar -xvf linux-6.2.2.tar.xz
		exit
		```
  15. Copy host linux running kernel config
		```
		sudo cp /boot/config-<version> /mnt/chroot/root/kernelbuild/linux-6.2.2/.config
		PS1="(arch chroot) # " sudo chroot /mnt/chroot /bin/bash
		cd /root/kernelbuild/linux-6.2.2
		```
  16. Modify .config file [Modify the following lines...]  [Use CTRL+W to search for values, CTRL+W again to find the next instance]
		```
		nano .config
		```
		```
		#CONFIG_MODULE_SIG_KEY=""
		#CONFIG_SYSTEM_TRUSTED_KEYS=""
		#CONFIG_CRYPTO_AEGIS128
		```
  17. Run the build
		```
		make -j$(nproc) && make modules -j$(nproc) && make modules_install && make headers_install
		```
  18. Copy "bzImage/Image" to boot directory
		```
		cp arch/arm64/boot/Image /boot/vmlinuz-linux6
		```
  19. Generate initramfs
		```
		pacman -Sy cpio mkinitcpio-nfs-utils mkinitcpio-archiso
		mkinitcpio -k 6.2.2 -g /boot/initramfs-linux6.img
		```
  20. Install Grub
		```
		pacman -Sy grub efibootmgr
		mkdir /boot/efi
		grub-install --target=arm64-efi --efi-directory=/boot/efi --bootloader-id=ARCH
		grub-mkconfig -o /boot/grub/grub.cfg
		```
  21. Configure Grub Linux defaults
		```
		nano /etc/default/grub
		```
		```
		GRUB_CMDLINE_LINUX_DEFAULT="loglevel=4 arm-smmu.disable_bypass=1 amdgpu.pcie_gen_cap=0x4 amdgpu.noretry=1"
	  	```
  22. Set root permissions
		```
		chmod 755 /
		chmod 755 /bin
		chmod 755 /lib
		```
  23. Reboot LX2K and boot into your new install using the BIOS
 
  24. Log into your new install with root/*password*
      
  25. Disable console syslog messages [Only if your console is flooded]
		```
		dmesg -n 1
		clear
		```
  26. Update system
		```
		pacman -Syyu
		```
  27. Install Gnome (I had issues installing gnome base package due to missing qemu dependecies)
		```
		pacman -Sy gnome-desktop gdm baobab cheese eog epiphany evince file-roller gdm gnome-backgrounds gnome-books gnome-calculator gnome-calendar gnome-characters gnome-clocks gnome-color-manager gnome-console gnome-contacts gnome-control-center gnome-disk-utility gnome-font-viewer gnome-getting-started-docs gnome-keyring gnome-logs gnome-maps gnome-menus gnome-music gnome-photos gnome-remote-desktop gnome-session gnome-settings-daemon gnome-shell gnome-shell-extensions gnome-software gnome-system-monitor gnome-text-editor gnome-themes-standard gnome-user-docs gnome-user-share gnome-video-effects gnome-weather grilo-plugins gvfs gvfs-afc gvfs-goa gvfs-google gvfs-gphoto2 gvfs-mtp gvfs-nfs gvfs-smb malcontent mutter nautilus orca rygel simple-scan
		```
		```
		sytemctl enable gdm
		```
  28. Create a uservisudo
		```
		pacman -Sy sudo
		visudo
		```
		Scroll down and press “x” when on the # to remove it
		```
		# %wheel ALL=(ALL:ALL) ALL
		```
		Then press [ESC], then [:], [w], [q], [!]
		```
		useradd -m -G audio,video,wheel,storage,kvm,users *username*
		passwd *username*
		```
  29. Final reboot!
