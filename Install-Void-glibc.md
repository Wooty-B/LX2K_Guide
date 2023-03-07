  # Installing Void Linux (glibc)
  
1. Locate the disk you will be using
	```
	sudo fdisk -l
	```
2. Select the drive we will be using for Void (Where X is your drive letter)
	```
	sudo fdisk /dev/sdX
	```
3. Type the following letters and numbers to partition the drive
	```
	Command: 	  n
	Partition number: 1
	First sector: 	  [ENTER]
	Last sector: 	  +512M
	Remove signature: Y
	Command: 	  n
	Partition number: 2
	First sector: 	  [ENTER]
	Last sector: 	  [ENTER]
	Remove signature: Y
	Command: 	  w
	Exit:		  q
	```
4. Format the new partitions
	```
	sudo mkfs.vfat /dev/sdX1
	sudo mkfs.ext4 /dev/sdX2
	```
5. Mount partitions
	```
	sudo mount /dev/sdX2 /mnt
	sudo mkdir /mnt/boot
	sudo mount /dev/sdX1 /mnt/boot
	```
6. Download the Void Tarball
	```
	wget https://repo-default.voidlinux.org/live/current/void-aarch64-ROOTFS-20221001.tar.xz
	```
7. Extract the tarball to the root of sdX2
	```
	sudo tar xvf void-aarch64-ROOTFS-20221001.tar.xz -C /mnt
	```
8. Mount host filesystems to prepare chroot
	```
	sudo mount --rbind /sys /mnt/sys && sudo mount --make-rslave /mnt/sys
	sudo mount --rbind /dev /mnt/dev && sudo mount --make-rslave /mnt/dev
	sudo mount --rbind /proc /mnt/proc && sudo mount --make-rslave /mnt/proc
	```
9. Copy Host DNS settings to chroot
	```
	sudo cp /etc/resolv.conf /mnt/etc/resolv.conf
	```
10. Chroot into new Void root
	```
	PS1="(void chroot) # " sudo chroot /mnt /bin/bash
	```
11. Install the base system
	```
	xbps-install -Su xbps
	xbps-install -u
	xbps-install base-system
	xbps-remove base-voidstrap
	```
12. Install a text editor, unless you prefer vi
	```
	xbps-install nano
	```
13. Set hostname
	```
	nano /etc/hostname
	```
14. Uncomment and set locale options in rc.conf
	```
	nano /etc/rc.conf
 	```
	```
	HARDWARECLOCK=”UTC”
	TIMEZONE=”US/Central”
	KEYMAP=”en”
	```
15. Uncomment lines in libc-locale
	```
	nano /etc/default/libc-locales
	```
	```
	en_US.UTF-8 UTF-8
	```
16. Re-generate locale files
	```
	xbps-reconfigure -f glibc-locales
	```
17. Change root password
	```
	passwd
	```
18. Add user, replacing *username* where necessary
	```
	useradd -m -G audio,video,wheel,storage,kvm,users *username*
	passwd *username*
	visudo
	```
	Scroll down and press “x” when on the # to remove it
	```
	# %wheel ALL=(ALL:ALL) ALL
	```
	Then press [ESC], then [:], [w], [q], [!]
	
19. Get UUID of sdX1 and sdX2
	```
	blkid
	```
20.	Configure /etc/fstab, using the UUID’s found in the previous command
	```
	nano /etc/fstab
	```
	```
	UUID=XXX-XXX		/boot	vfat	rw,relatime,errors=remount-ro  	0 2
	UUID=YYY-YYY-YYY-YYY	/	ext4	rw,relatime			0 1
	tmpfs			/tmp	tmpfs	rw,relatime			0 0
	```
21. Install Grub bootloader
	```
	xbps-install grub-arm64-efi
	grub-install --target=arm64-efi --efi-directory=/boot --bootloader-id=”Void”
	xbps-reconfigure -fa
	```
22. Configure Grub command line arguments
	```
	nano /etc/default/grub
	```
	```
	GRUB_CMDLINE_LINUX_DEFAULT="modprobe.blacklist=amdgpu loglevel=4 arm-smmu.disable_bypass=0 amdgpu.pcie_gen_cap=0x4 amdgpu.noretry=1"
	```
23. Install the Kernel [Kernel numbers will vary, replace where needed]
	```
	xbps-query --regex -Rs '^linux[0-9.]+-[0-9._]+'
	xbps-install linux6.0-6.0.8_1 linux6.0-headers-6.0.8_1
	```
24. Configure initramfs
	```
	xbps-install dracut
	dracut --host-only --kver 6.0.8_1
	update-grub
	```
25. Reboot into your Void install

26. Find your Ethernet/Wireless interface
	```
	ip addr
	```
27. Enable the network adapter using the network interface found above, and ip scheme of your network; Y is the subnet in slash notation (ex. /24 for 255.255.255.0)
	```
	sudo ip link set dev *net_interface* up
	sudo ip addr add X.X.X.X/Y brd + dev *net_interface*
	sudo ip route add default via X.X.X.Z
	```
28. Install linux firmware for AMD GPU
	```
	sudo xbps-install linux-firmware-amd 
	```
29. Install Gnome (Optional)
	```
	sudo xbps-install gnome gdm xorg mesa-dri mesa-vaapi mesa-vdpau
	sudo ln -s /etc/sv/gdm /var/service
	sudo ln -s /etc/sv/NetworkManager /var/service
	sudo ln -s /etc/sv/dbus /var/service
	```
	
30. Enable amdgpu driver on startup (place at end of file)
	```
	sudo nano /etc/rc.local
 	```
 	```
 	sudo modprobe amdgpu
 	exit 0
	```

31. Install and enable socklog (System logging)
	```
	xbps-install socklog-void
	usermod -a -G socklog *username*
	sudo ln -s /etc/sv/socklog-unix /var/service
	sudo ln -s /etc/sv/nanoklogd /var/service
	```
