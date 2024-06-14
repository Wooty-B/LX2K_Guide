# Installing Armbian
  
   1. Download the Armbian aarch64 efi image from thelink:
		
        https://www.armbian.com/uefi-arm64/

   2. Write the downloaded image tarball to the drive of your choice:
        ```
        xzcat ~/Downloads/Armbian_*version*_Uefi-arm64_*release*_current_*kernel*_minimal.img.xz | sudo dd of=/dev/sdX status=progress
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
   7. Set the hostname
		```
		nano /etc/hostname
		```
   8. Install latest updates
        ```
        apt install -y linux-headers-current-arm64
        apt update && apt upgrade
        ```
   9. Configure grub
		```
		grub-install --target=arm64-efi --efi-directory=/boot --bootloader-id=Armbian-EFI
		grub-mkconfig -o /boot/grub/grub.cfg
		```
  10. Configure Grub Linux defaults
		```
		nano /etc/default/grub
		```
		```
		GRUB_CMDLINE_LINUX_DEFAULT="quiet arm-smmu.disable_bypass=1 amdgpu.pcie_gen_cap=0x4"
	  	```
        ```
        update-grub
        apt install --reinstall linux-image-current-arm64
        ```
        For some reason, Armbian/GRUB fails to add a boot entry upon 'update-grub' unless you reinstall kernel. Even then it adds the wrong entry, and you will need to rename "vmlinuz-*version*" to "Image" in /boot/grub/grub.cfg
        ```
        nano /boot/grub/grub.cfg
        [CTRL]+[W]
        vmlinuz
        [ENTER]
        ```
        Rename the '/vmlinuz' entry to just say '/Image'

  11. Reboot LX2K and boot into your new Arbian install; follow prompts to change root password and setup a user account

        After adding a user, run the following:
        ```
        usermod -aG sudo *username*
        exit
        ```

  12. Login and install a desktop environment (GNOME recommended)
        ```
        sudo apt update && sudo apt install armbian-config
        sudo apt install -y tasksel
        sudo tasksel
        sudo reboot
        ```
 
  13. Upon reboot you should be at a graphical Armbian login!