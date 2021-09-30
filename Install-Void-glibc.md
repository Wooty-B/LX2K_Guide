  # Installing Void Linux (glibc)

  1. Create disk partitions: (Modify X for your drive letter)
	
	sudo mkfs.vfat /dev/sdX1
	sudo mkfs.ext4 /dev/sdX2
	
  2. Mount newly created partitions:
	
	sudo mount /dev/sdX2 /mnt/
	mkdir -p /mnt/boot/efi/
	sudo mount /dev/sdX1 /mnt/boot/efi/
	
  3. Download the aarch64 glibc rootfs tarball:
	
	wget https://alpha.de.repo.voidlinux.org/live/current/void-aarch64-ROOTFS-20210316.tar.xz
	
  4. Extract tarball to root of sdX2:
	
	tar xvf void-aarch64-ROOTFS-20210316.tar.xz -C /mnt
	
  5. Mount filesystems for chroot:
	
	sudo mount --rbind /sys /mnt/sys && mount --make-rslave /mnt/sys
	sudo mount --rbind /dev /mnt/dev && mount --make-rslave /mnt/dev
	sudo mount --rbind /proc /mnt/proc && mount --make-rslave /mnt/proc
	
  6. Copy DNS Settings from host:
	
	sudo cp /etc/resolv.conf /mnt/etc/
	
  7. Chroot into Void root:
	
	PS1='(chroot) # ' chroot /mnt/ /bin/bash
	
  8. Install base system:
	
	xbps-install -Su xbps
	xbps-install -u
	xbps-install base-system
	xbps-remove base-voidstrap

  9. Install text editor and video driver packages:
	
	xbps-install nano linux-firmware-amd mesa-dri vulkan-loader mesa-vulkan-radeon mesa-vaapi mesa-vdpau
	
  10. Set hostname:
	
	nano /etc/hostname
	
  11. Set rc.conf locale options:
	
	nano /etc/rc.conf
	
	Example:
	HARDWARECLOCK="UTC"
	TIMEZONE="US/Central"
	KEYMAP="en"
	
  12. Uncomment lines in libc-locale:
	
	nano /etc/default/libc-locales
	
	Example:
	en_US.UTF-8 UTF-8
	
  13. Generate locale files:
	
	xbps-reconfigure -f glibc-locales
	
  14. Change root password:
	
	passwd
	
  15. Get UUID of drives:
	
	blkid
	
  16. Configure fstab file:
	
	nano /etc/fstab
	
	Example:
	UUID=XXX-XXX	/boot/efi	vfat	rw,relatime,errors=remount-ro	0 2
	UUID=YYY-YYY	/		ext4	rw,relatime			0 1
	tmpfs		/tmp		tmpfs	rw,relatime			0 0
	
  17. Install Grub:
	
	xbps-install grub-arm64-efi
	grub-install --target=arm64-efi --efi-directory=/boot/efi --bootloader-id="Void"
	xbps-reconfigure -fa

  18. Configure Grub Linux defaults:
	
	nano /etc/default/grub
	
	GRUB_CMDLINE_LINUX_DEFAULT="nomodeset loglevel=4 arm-smmu.disable_bypass=0 amdgpu.pcie_gen_cap=0x4 amdgpu.noretry=1"
	
  19. Install Kernel: (Query will show you all available kernels, modify to your needs)
	
	xbps-query --regex -Rs '^linux[0-9.]+-[0-9._]+'
	xbps-install linux5.14-5.14.8_1 linux5.14-headers-5.14.8_1
	
  20. Configure initramfs: (Replace kernel version with one  from /lib/modules)
	
	xbps-install dracut
	dracut --host-only --kver 5.14.8_1
	update-grub
