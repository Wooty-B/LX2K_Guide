# Installing FreeBSD 14.1+

   1. Download the FreeBSD image (disk1 or dvd1) from the repository here:
         ```
         Download Portal: https://download.freebsd.org/releases/arm64/aarch64/ISO-IMAGES/
         ```
   2. Flash the ISO to a USB drive
         ```
         dd if=~/Downloads/FreeBSD-*version*-RELEASE-arm64-aarch64-disc1.iso of=/dev/sdX
         ```
         
         Alternatively, you can use Gnome Disks, balenaEtcher, or an imaging tool of your choice.
         
   3. Restart and boot from the USB drive that was created. (See: BIOS Navigation)
   
   4. Press [ENTER] or wait for installer to start.

   5. Continue through the installer, making sure to select your USB NIC from the network adaptors section.
         
   6. Once finished, reboot and login to the new FreeBSD install. Run the following to setup sudo:

         ```
         su
         pw groupmod video -m *username* || pw groupmod wheel -m *username*
         pkg install sudo (say 'Y' when asking to install package management tool)
         visudo           
         ```
         Scroll to end of 'visudo', highlight the '#' symbol and press [x] on line: # %wheel ALL=(ALL:ALL) ALL
         This will remove the '#' symbol. Then press [ESC]+[:]+[w]+[q]+[!]+[ENTER] to save and exit 'visudo'.

   7. Optionally, you may now install a Desktop Environment. This will install XFCE: 

         Install X11 backend and configure video mode:
         ```
         sudo pkg install xorg
         vi /boot/loader.conf
         ```
         In 'vi', press [i] to enter interactive typing mode, then input the following:
         ```
         kern.vty=vt
         ```
         Then press [ESC]+[:]+[w]+[q]+[!]+[ENTER] to save and exit 'loader.conf'.
         
         Now we will add our video driver to 'rc.conf':
         ```
         vi /etc/rc.conf
         ```
         Add the following line to the file, supplementing for the video card/output you desire:
         ```
         kld_list="amdgpu"      (Other flags are: radeonkms; nvidia; nvidia-modeset)
         ```
         Save and exit the file. We can now start installing XFCE and set it to launch with the command 'startx':
         ```
         sudo pkg install xfce
         echo "exec /usr/local/bin/startxfce4 --with-ck-launch" > ~/.xinitrc
         startx
         ```

   8. You should now be running the XFCE desktop on FreeBSD!