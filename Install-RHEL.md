# Installing Red Hat Enterprise Linux 9.4+

   1. Sign up for a RHEL Account to download the image:
         ```
         Developer Download Portal: https://developers.redhat.com/products/rhel/download
         ```
   2. Flash the ISO to a USB drive
         ```
         dd if=~/Downloads/rhel-*version*-aarch64-dvd.iso of=/dev/sdX
         ```
         
         Alternatively, you can use Gnome Disks, balenaEtcher, or an imaging tool of your choice.
         
   3. Restart and boot from the USB drive that was created. (See: BIOS Navigation)
   
   4. Click 'Install Red Hat Enterprise Linux', and on the setup screen make sure to register your device.

         If you are testing, use the following registration settings:
         ```
         Role: Any
         SLA: Self-Support
         Usage: Development/Test
         ```
         
   5. Continue installing and restart when prompted. You will need to manually restart if the text '[terminated]' is displayed.
         
   6. You should now be booted into the Red Hat Enterprise Linux desktop!