  # Installing Gentoo (OpenRC)
  
  ## Under construction

1. Locate target Disk
    ```
	  sudo fdisk -l
	  ```
2. Format target Disk (Where X is your drive letter)
	  ```
	  sudo fdisk /dev/sdX
	  ```
    Type the following letters and numbers to partition the drive
	  ```
	  Command:          n
	  Partition number: 1
	  First sector:     [ENTER]
	  Last sector:      +512M
	  Remove signature: Y
	  Command:          n
	  Partition number: 2
	  First sector:     [ENTER]
	  Last sector:      [ENTER]
	  Remove signature: Y
	  Command:          w
	  ```
3. Format the new partitions
	  ```
	  sudo mkfs.vfat /dev/sdX1
	  sudo mkfs.ext4 /dev/sdX2
	  ```
4. Mount partitions
	  ```
	  sudo mount /dev/sdX2 /mnt
	  sudo mkdir /mnt/boot
	  sudo mount /dev/sdX1 /mnt/boot
	  cd /mnt
	  ```
5. Install xz package if not already installed
	
6. Download and extract Gentoo tarball  (Guide covers OpenRC)
	  
	  Current stage3 tarballs here: http://distfiles.gentoo.org/releases/arm64/autobuilds/
	  ```
	  wget http://distfiles.gentoo.org/releases/arm64/autobuilds/current-stage3-arm64-desktop-openrc/stage3-arm64-desktop-openrc-*date*.tar.xz
	  sudo tar xvf stage3-arm64-desktop-openrc-*date*.tar.xz -C /mnt
	  ```
7. Mount pseudo filesystems
	  ```
	  sudo mount --rbind /sys /mnt/sys && sudo mount --make-rslave /mnt/sys
	  sudo mount --rbind /dev /mnt/dev && sudo mount --make-rslave /mnt/dev
	  sudo mount -t proc /proc /mnt/proc
	  sudo mount --rbind /tmp /mnt/tmp
	  ```
8. Copy DNS server settings from host
	  ```
    sudo cp /etc/resolv.conf /mnt/etc
    ```
9. Chroot into Gentoo mount
	  ```
	  PS1="(gentoo chroot) # " sudo chroot /mnt /bin/bash
	  ```
10. Configure Gentoo
	  ```
	  mkdir /var/db/repos/gentoo
	  env-update && . /etc/profile
    emerge-webrsync
    ```
11. Set root password
	  ```
	  passwd
	  ```
12. Create user account and setup sudo
	  ```
	  useradd -m -G users,wheel,audio,video -s /bin/bash *username*
	  passwd *username*
    emerge --ask --verbose app-admin/sudo
    visudo
    ```
    Uncomment the following line:
    ```
    # %wheel ALL=(ALL:ALL) ALL
	  ```
13. Enable Networking
	  ```
	  ln -s /etc/init.d/net.{lo,eth0}
	  rc-update add net.eth0 default
	  ```
14. Set Hostname
	  ```
	  nano /etc/conf.d/hostname
	  nano /etc/hosts
    ```
    Add your hostname after ‘localhost’; example below:
    ```
    127.0.0.1	localhost	*hostname*
    ```
15. Configure DHCP
	  ```
	  emerge --ask net-misc/dhcpcd
	  rc-update add dhcpcd default
	  nano /etc/conf.d/net
	  ```
	  ```
	  config_eth0=”dhcp”
	  ```	
	  Optionally, start the service now:
	  ```
	  rc-service dhcpcd start
	  ```
16. Configure Portage makefile
	  ```
	  nano /etc/portage/make.conf
	  ```
	  ```
    LLVM_TARGETS="ARM AMDGPU AArch64 BPF"
    L10N="en"
    LC_MESSAGES=C
    CPU_FLAGS_ARM64="neon"
    VIDEO_CARDS="amdgpu radeonsi"
    COMMON_FLAGS="-march=native -O2 -pipe"
    FCFLAGS="${COMMON_FLAGS}"
    FFLAGS="${COMMON_FLAGS}"
    CFLAGS="${COMMON_FLAGS}"
    CXXFLAGS="${COMMON_FLAGS}"
    CHOST="aarch64-unknown-linux-gnu"
    FEATURES=“buildpkg parallel-fetch parallel-install”
    USE="${USE} X wayland vulkan elogind dbus opengl gnome gtk grub -systemd  -tracker"
    EMERGE_DEFAULT_OPTS="–ask --jobs 12 --keep-going=y --autounmask=y --autounmask-continue"
    MAKEOPTS="-j12"
    ACCEPT_KEYWORDS="~arm64"
    ACCEPT_LICENSE="*"
    PORTAGE_NICENESS="16"
    GENTOO_MIRRORS=https://mirror.leaseweb.com/gentoo/
    GRUB_PLATFORMS=”efi-64”
    ```
17. Configure repositories
	  ```
	  mkdir /etc/portage/repos.conf
	  nano /etc/portage/repos.conf/gentoo.conf
	  ```
	  ```
	  [Default]
	  main-repo = gentoo
	  
	  [gentoo]
	  location = /usr/portage
	  sync-type = rsync
	  sync-url = rsync://namerica.gentoo.org/gentoo-portage
	  auto-sync = yes
	  ```
18. Cofigure Portage, system profile, and install packages
	  ```
	  emerge --ask --oneshot sys-apps/portage
	  emerge --sync
	  eselect profile list
	  ```
	  select the profile of your choice, example:
	  ```
	  eselect profile set 5
	  ```
19. Install all packages from step 17 (USE=) [May take a long time]
	  ```
	  emerge --ask --verbose --update --deep --newuse @world
	  ```
20. Get UUID of sdX1 and sdX2
	  ```
	  blkid
	  ```
21. Configure /etc/fstab, using the UUID’s found in the previous command
	  ```
	  nano /etc/fstab
	  ```
	  ```
	  UUID=XXX-XXX		/boot	vfat	rw,relatime,errors=remount-ro  	0 2
	  UUID=YYY-YYY-YYY-YYY	/	ext4	rw,relatime			0 1
	  tmpfs			/tmp	tmpfs	rw,relatime			0 0
	  ```
22. Configure kernel system type
	  ```
	  emerge --ask sys-kernel/installkernel-gentoo
	  ```
23. Installing the Kernel
	
	  Pre-built kernel binary:
	  ```
	  emerge --ask sys-kernel/gentoo-kernel-bin
	  emerge --depclean
	  ```
	  Source-built kernel:
	  ```
	  emerge --ask sys-kernel/gentoo-kernel
	  emerge --depclean
	  ````	
24. Loading modules
	
    Either find the modules you need to load here:
    ```
	  find /lib/modules/<kernel version>/ -type f -iname '*.o' -or -iname '*.ko' | less
	  ```
	  Or alternatively, check loaded host modules for reference:
	  ```
	  exit
	  lsmod
	  PS1="(gentoo chroot) # " sudo chroot /mnt /bin/bash
	  ```
	  Then, run the following and add kernel modules to the conf file:
	  ```
	  mkdir -p /etc/modules-load.d
	  nano -w /etc/modules-load.d/network.conf
	  ```
25. Set Keymap
	  ```
	  nano /etc/conf.d/keymaps
	  ```
	  Change country code (Where XX is code; Example: us)
	  ```
	  keymap=XX
	  ```
26. Install and enable System Logger (sysklogd)
	  ```
	  emerge --ask app-admin/sysklogd
	  rc-update add sysklogd default
	  ```
27. Set Time Syncronization
	  ```
	  emerge --ask net-misc/chrony
	  rc-update add chrony default
	  ```
28. Install Filesystem tools
	  ```
	  emerge --ask sys-fs/e2fsprogs sys-fs/dosfstools
	  ```
29. Install and configure GRUB
	  ```
	  emerge --ask --verbose sys-boot/grub
	  grub-install --target=arm64-efi --efi-directory=/boot --bootloader-id=Gentoo
	  grub-mkconfig -o /boot/grub/grub.cfg
	  ```
30. Reboot and enjoy!

    Optional: Installing GNOME (Can be done before reboot)
	
	  Update system:
    ```
	  emerge --sync
	  emerge --deep --with-bdeps=y --changed-use --update --ask --verbose @world
	  ```
	  Set and update GNOME profile:
	  ```
	  eselect profile set “default/linux/arm64/17.0/desktop/gnome”
	  eselect profile show
	  emerge --ask --deep --changed-use --update --verbose @world
	  ```
	  Install and configure X11:
	  ```
	  echo -e “media-libs/mesa xa” >> /etc/portage/package.use/mesa
	  emerge --ask --verbose --oneshot x11-base/xorg-server x11-base/xorg-drivers
	  usermod -a -G plugdev *username*
	  ```
	  Install GNOME:
	  ```
	  emerge --ask  --verbose --keep-going gnome-base/gnome gnome-base/gdm
	  ```
	  Change and configure Display Manager:
	  ```
	  nano /etc/conf.d/display-manager
	  ```
	  ```
	  CHECKVT=7
	  DISPLAYMANAGER=”gdm”
	  ```
	  ```
	  emerge --ask --noreplace gui-libs/display-manager-init
	  ```
	  Update OpenRC:
	  ```
    	  emerge --ask --verbose app-admin/openrc-settingsd 
	  rc-update add dbus default
	  rc-update add display-manager default
	  rc-update add openrc-settingsd default
	  rc-update add elogind boot
	  ```
	  Update GRUB with AMDGPU patch:
	  ```
	  nano /etc/default/grub
	  ```
	  ```
	  GRUB_CMDLINE_LINUX_DEFAULT="amdgpu.pcie_gen_cap=0x4"
	  ```
	  ```
	  grub-mkconfig -o /boot/grub/grub.cfg
	  ```
	  Reboot!
