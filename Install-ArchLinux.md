  # Installing Arch Linux ARM
   
  1. Install bsdtar & wget
      
          Debian/Ubuntu: sudo apt install libarchive-tools wget
          Fedora: sudo dnf install bsdtar wget
          Void: sudo xbps-install bsdtar wget
          Arch: sudo pacman -Sy bsdtar wget

  
  2. Mount the target drive
  
          sudo mkdir /mnt/chroot
          sudo mount /dev/sdX1 /mnt/chroot
          sudo mkdir -p /mnt/chroot/boot
          sudo mount /dev/sdX2 /mnt/chroot/boot
          
  3. Download and extract the tarball to the mounted drive as root [Do not use sudo]
  
          su -
          wget http://os.archlinuxarm.org/os/ArchLinuxARM-aarch64-latest.tar.gz
          bsdtar -xpf ArchLinuxARM-aarch64-latest.tar.gz -C /mnt/chroot
          exit
      
  4. Mount pseudo filesystems
  
          cd /mnt/chroot
          sudo mount -t proc /proc proc
          sudo mount --make-rslave --rbind /sys sys
          sudo mount --make-rslave --rbind /dev dev
          sudo mount --make-rslave --rbind /run run

  5. Chroot into Arch Linux install
  
          sudo chroot /mnt/chroot /bin/bash
	  
  6. Set new root password
 
          passwd root
	  
  7. Edit resolv.conf
  
          rm /etc/resolv.conf
          nano /etc/resolv.conf
	  
          "nameserver 8.8.8.8"
      
  8. Initiate Pacman and needed packages 
  
          pacman-key --init
          pacman-key --populate archlinuxarm
          pacman -Sy base-devel wget python3 bc pahole
      
  9. Download and extract the kernel [Modify to your needs]
  
          mkdir /root/kernelbuild && cd /root/kernelbuild
          wget https://cdn.kernel.org/pub/linux/kernel/v6.x/linux-6.0.10.tar.xz
          tar -xvf linux-6.0.10.tar.xz
          exit
      
  10. Copy host linux running kernel config
    
          cp /boot/config-<version> /mnt/chroot/root/kernelbuild/linux-6.0.10/.config
          sudo chroot /mnt/chroot /bin/bash
          cd /root/kernelbuild/linux-6.0.10
      
  11. Modify .config file [Modify the following lines...]  [Use CTRL+W to search for values, CTRL+W again to find the next instance]
      
          nano .config
      
          CONFIG_LOCALVERSION="-1-ARCH"
          #CONFIG_MODULE_SIG_KEY=""
          #CONFIG_SYSTEM_TRUSTED_KEYS=""
          #CONFIG_CRYPTO_AEGIS128
      
  12. Run the build
 
          make -j$(nproc) && make modules -j$(nproc) && make modules_install
      
  13. Copy "bzImage/Image" to boot directory
 
          cp arch/arm64/boot/Image /boot/vmlinuz-linux6
      
  14. Generate initramfs
      
          pacman -Sy cpio mkinitcpio-nfs-utils mkinitcpio-archiso
          mkinitcpio -k 6.0.10-1-ARCH -g /boot/initramfs-linux6.img
      
  15. Install Grub
 
          pacman -Sy grub efibootmgr
          mkdir /boot/efi
          grub-install --target=arm64-efi --efi-directory=/boot/efi --bootloader-id=ARCH
          grub-mkconfig -o /boot/grub/grub.cfg
      
  16. Configure Grub Linux defaults
          
          nano /etc/default/grub
	
	        GRUB_CMDLINE_LINUX_DEFAULT="nomodeset loglevel=4 arm-smmu.disable_bypass=0 amdgpu.pcie_gen_cap=0x4 amdgpu.noretry=1"
 
  17. Set root permissions
 
          chmod 755 /
          chmod 755 /bin
          chmod 755 /lib
      
  18. Reboot LX2K and boot into your new install using the BIOS
 
  19. Log into your new install with root/*password*
      
  20. Update system
 
          pacman -Syyu
      
  21. Install Gnome
 
          pacman -Sy gnome-desktop
          sytemctl enable gdm
 
 NOTES:
 
 1. If Terminal doesnt launch under Gnome, install Terminator, it works fine. Currently troubleshooting.
 2. Graphical issues abound. Currently troubleshooting.
