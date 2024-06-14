# Installing NetBSD 10+

   1. Download the NetBSD image (evbarm-aarch64 ISO under 'Release Info') from the repository here:
         ```
         Download Portal: https://wiki.netbsd.org/ports/evbarm/
         ```
   2. Flash the ISO to a USB drive
         ```
         dd if=~/Downloads/FreeBSD-*version*-RELEASE-arm64-aarch64-disc1.iso of=/dev/sdX
         ```
         
         Alternatively, you can use Gnome Disks, balenaEtcher, or an imaging tool of your choice.
         
   3. Restart and boot from the USB drive that was created. (See: BIOS Navigation)
   
   4. Press [1] or wait for installer to start.

   5. Click through first options, installing NetBSD to your drive of choice.
   
         NOTE: If you get scrolling output on install screen, you may need to unplug extra USB devices.

         WARNING: NetBSD detects the onboard SD Card, you can accidentally overwrite the EDK II/U-Boot loader here.

   6. When asked what distribution you would like, click "Custom Installation" and make sure the following sets are selected if you want an easy DE setup:
         ```
         X11 sets
            a. X11 base and clients
            c. X11 configuration
            d. X11 fonts
            e. X11 servers
         Source and debug sets
            b. Base sources
            d. GNU sources
            e. X11 sources
         ````
   7. Once your distribution sets are selected, continue on and when asked where you'd like to install from, select the following:
         ```
         b: HTTP
         ```
         Enter your hostname and domain name then select:
         ```
         x: Get Distribution
         ```
   8. When the download finishes, enter a root password, then complete the last two options:
         ```
         k: Enable xdm
         o: Add user
         ```
   9. Due to nature of NetBSD, you will need to boot directly from the device path or add a boot entry for NetBSD. Then you may boot into NetBSD.

  10. To login, type your username, press [ENTER], then type your password and press [ENTER]. You should now be in a very minimal X window environment.

  11. Once logged in, we will need to set up our package sources, as well as install and configure sudo. Run the following:
         ```
         export PKG_PATH="http://ftp.netbsd.org/pub/pkgsrc/packages/NetBSD/aarch64/10.0/All/"
         su
         echo $PKG_PATH
         pkg_add -v pkgin
         exit
         ```
  12. Now we need to setup sudo and add user to 'wheel' group.
         ```
         su
         pkg_add -v sudo
         visudo
         user mod -G wheel *username*
         exit
         ```
  13. We can now start installing XFCE and configuring our system to boot into our DE.
         ```
         su
         pkgin install xfce4 xfce4-extras
         cp /usr/pkg/share/examples/rc.d/dbus /etc/rc.d  
         echo 'dbus=YES' >> /etc/rc.conf
         /etc/rc.d/dbus start
         touch .xsession
         echo 'exec ck-launch-session xfce4-session' > .xsession
         echo 'DisplayManager*authName: MIT-MAGIC-COOKIE-1' >> /etc/X11/xdm/xdm-config
         echo 'xdm=YES' >> /etc/rc.conf 
         ```
  14. Reboot. You should now be able to log in to your graphical desktop!

         Lastly, run the following to setup basic home folder directories:
         ```
         pkgin install xdg-user-dirs
         xdg-user-dirs-update
         ```
         
         You can now choose to install additional packages if you'd like:
         ```
         pkgin install vlc zip unzip unrar sudo vim gnumeric abiword papirus-icon-theme epdfview p7zip neofetch wget firefox mpv cmus xcalc xterm
         ```

    ## Special thanks to Edgar Rodolfo and his guide: https://bitsymasbitsya.blogspot.com/2021/12/netbsd-92-and-xfce-with-xdm-display.html
  