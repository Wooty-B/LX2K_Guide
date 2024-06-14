# Installing Manjaro ARM
  
   1. Download the Manjaro ARM 'GENERIC EFI' image (recommend the GNOME version) from the link below:
		
        https://manjaro.org/download/

        NOTE: At time of writing, the GNOME image is broken on their site, you can download the prior release or a nightly build from here: https://github.com/manjaro-arm/generic-efi-images/releases/tag/20240610

   2. Write the downloaded image tarball to the drive of your choice:
        ```
        xzcat ~/Downloads/Manjaro-ARM-*type*-generic-*version*.img.xz | sudo dd of=/dev/sdX status=progress
      ```      
   3. Use the following to extend the last partition:
		```
        sudo fdisk /dev/sdX
        ```
        ```
		Command:		    d
		Partition number:	2
		Command:		    n
		Partition number:	[ENTER]
		First sector:		[ENTER]
		Last sector:		[ENTER]
		Remove signature:	N
		Save changes:		wq
		```
        ```
        sudo e2fsck -f /dev/sdX2
        sudo resize2fs /dev/sdX2
        ```
   4. Mount the target drive
		```
		sudo mkdir /mnt/chroot
		sudo mount /dev/sdX2 /mnt/chroot
		sudo mount /dev/sdX1 /mnt/chroot/boot
		```
   5. Mount pseudo filesystems
		```
		cd /mnt/chroot
		sudo mount -t proc /proc proc
		sudo mount --make-rslave --rbind /sys sys
		sudo mount --make-rslave --rbind /dev dev
		sudo mount --make-rslave --rbind /run run
		```
   6. Chroot into Manjaro install
		```
		sudo chroot /mnt/chroot /bin/bash
		```
   7. Set new root password
		```
		passwd
		```
   8. Edit resolv.conf
		```
		nano /etc/resolv.conf
		```
		```
		nameserver 8.8.8.8
		```
   9. Set the hostname
		```
		nano /etc/hostname
		```
  10. Initiate Pacman and needed packages 
		```
		pacman-key --init
		pacman-key --populate archlinuxarm manjaro-arm
		pacman -Sy base-devel wget python3 bc pahole rsync
		```
  11. Download the latest linux kernel image
        ```
        pacman -Sy linux linux-headers
        ```
  12. Install Grub
		```
		pacman -Sy grub efibootmgr
		grub-install --target=arm64-efi --efi-directory=/boot --bootloader-id=Manjaro-ARM
		grub-mkconfig -o /boot/grub/grub.cfg
		```
  13. Configure Grub Linux defaults
		```
		nano /etc/default/grub
		```
		```
		GRUB_CMDLINE_LINUX_DEFAULT="loglevel=4 arm-smmu.disable_bypass=1 amdgpu.pcie_gen_cap=0x4"
	  	```
        ```
        update-grub-menu
        ```
  14. Set root permissions
		```
		chmod 755 /
		chmod 755 /bin
		chmod 755 /lib
		```
  15. Create a user account
		```
		pacman -S sudo vi
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
  16. Bypass signature check; I was only able to update once changing this value.
        ```
        nano /etc/pacman.conf
        ```
        ```
        SigLevel = Never
        ```
  17. There is a conflist with 'konsoler' out-of-the-box, we need to back this up as it should be redownloaded
        ```
        sudo mv /etc/xdg/konsolerc /etc/xdg/konsolerc.bak
        pacman -Syyu
        ```
  18. Check fstab was generated correctly
        ```
        blkid
        ```
        Make note of the 'PART-UUID=' number for your two partitions. If different, run the command below and update the PART-UUID.
        ```
        nano /etc/fstab
        ```
  19. Reboot LX2K and boot into your new install using the BIOS
 
  20. You should now be in your new Manjaro install!

  
Must ask Manjaro forum for assistance, boots but not into a graphical user interface, have to switch to another tty
  
  NOTE: Hangs and flashes on login on GNOME image... Try KDE image again using the updated guide above!!!!

  Then, regardless whether it works, work on Armbian guide then look for a forum post for someone successfully running Manjaro on LX2K.