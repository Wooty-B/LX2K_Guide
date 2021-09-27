  # Installing Arch Linux ARM
  
  1. Download the Arch Linux ARM tarball:
  
          wget http://os.archlinuxarm.org/os/ArchLinuxARM-aarch64-latest.tar.gz
      
  2. Install Archive Tools:
      
          sudo apt install libarchive-tools
  
  3. Mount the target drive:
  
          sudo mkdir /mnt/chroot
          sudo mount /dev/sdXy /mnt/chroot
          sudo mount /dev/sdXz /mnt/chroot/boot/efi
          cd /mnt/chroot/
          sudo mount -t proc /proc proc
          sudo mount --make-rslave --rbind /sys sys
          sudo mount --make-rslave --rbind /dev dev
          sudo mount --make-rslave --rbind /run run
  
  4. Extract the tarball to the mounted drive:
  
          bsdtar -xpf ArchLinuxARM-aarch64-latest.tar.gz -C /mnt/chroot
      
  5. Chroot into Arch Linux install:
  
          sudo chroot /mnt/chroot /bin/bash
      
  6. Initiate Pacman:
  
          pacman-key --init
          pacman-key --populate archlinuxarm
      
  7. Download and extract the kernel:  [Non-SolidRun kernel; Need to Update] [Modify to your needs]
  
          mkdir /home/alarm/build/kernel && cd /home/alarm/build/kernel
          wget https://cdn.kernel.org/pub/linux/kernel/v5.x/linux-5.14.6.tar.xz
          tar -xvf linux-5.14.6.tar.xz
          cd linux-5.14.6.tar.xz
      
  8. Copy host linux running kernel config:
    
          exit
          cp /boot/config-<version> /mnt/chroot/home/alarm/build/kernel/linux-5.14.6/.config   [Select the one reflecting current kernel (uname -a)]
          sudo chroot /mnt/chroot /bin/bash
          cd /home/alarm/build/kernel
      
  9. Modify .config file:   [Modify the following lines...]   [Use CTRL+W to search for values, CTRL+W again to find the next instance]
      
          nano .config
      
          CONFIG_LOCALVERSION="-1-ARCH"
          #CONFIG_MODULE_SIG_KEY=""
          #CONFIG_SYSTEM_TRUSTED_KEYS=""
          #CONFIG_CRYPTO_AEGIS128
      
 10. Run the build:
 
          make -j12 && make modules -j12 && make modules_install
      
 11. Copy "bzImage/Image" to boot directory:
 
          cp arch/arm64/boot/Image /boot/vmlinuz-linux514
      
 12. Generate initramfs:
      
          pacman -Sy cpio mkinitcpio-nfs-util mkinitcpio-archiso
          mkinitcpio -k 5.14.6-1-ARCH -g /boot/initramfs-linux514.img
      
 13. Install Grub:
 
          grub-install --target=arm64-efi --efi-directory=/boot/efi --bootloader-id=GRUB
          grub-mkconfig -o /boot/efi/EFI/grub/grub.cfg
      
 14. Set root permissions:
 
          chmod 755 /
          chmod 755 /bin
          chmod 755 /lib
      
 15. Reboot LX2K and boot intoyour new install using the BIOS.
 16. Log into your new install with root/root.
 17. Set new root password:
 
          passwd root
 
 18. Edit resolv.conf:
 
          nano /etc/resolv.conf
      
          nameserver 8.8.8.8
      
 19. Update system:
 
          pacman -Syyu
      
 20. Install Gnome:   [At time of writing, still working on Mesa GNOME driver patch]
 
          pacman -Sy gnome-desktop
          sytemctl enable gdm
 
 NOTES:
 
 1. If Terminal doesnt launch under Gnome, install Terminator, it works fine. Currently troubleshooting.
