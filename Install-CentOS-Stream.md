# Installing CentOS Stream 9+

   1. Download the CentOS Stream image (not CentOS Linux 7-2009) from here:
         ```
         Download Portal: https://centos.org/download/
         ```
   2. Flash the ISO to a USB drive
         ```
         dd if=~/Downloads/CentOS-Stream-*version*-latest-aarch64-dvd1.iso of=/dev/sdX
         ```
         
         Alternatively, you can use Gnome Disks, balenaEtcher, or an imaging tool of your choice.
         
   3. Restart and boot from the USB drive that was created. (See: BIOS Navigation)
   
   4. Click 'Install Cent OS Stream', and follow installation prompts.

   5. Continue installing and restart when prompted. You will need to manually restart if the text '[terminated]' is displayed.
         
   6. You should now be booted into the CentOS Stream desktop!