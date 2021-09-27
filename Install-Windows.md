1. Visit "https://uupdump.net/" and search for "Windows 21286 arm64" using the field at the top.


2. Click on the Windows 10 Insider Preview 21286 image.

3. Choose your language then click Next.

4. Check your desired Windows 10 Editions then click Next. (I check both, but filesize may be larger)

5. Leave Download and Convert to ISO checked, and then click "Create download package".

6. Once downloaded, extract the zip:
    
        unzip 21286.1000_arm64_en-us_multi_04a8f3ca_convert -d win10_arm64_iso

7. Enter the directory, install the prerequisites, and run the downloader:

        cd win10_arm64_iso
        chmod +x uup_download_linux.sh
        sudo apt install aria2 cabextract wimtools genisoimage
        ./uup_download_linux.sh

8. Use balenaEtcher or dd to write the output iso noted below to a USB device:

        <VERSION>_MULTI_ARM64_<REGION>.ISO

9. Once the iso file is written to a USB drive, reboot the LX2K, and run it from the BIOS.

10. Start the installer as usual, skip the key activation, and choose custom.

11. If you have any SATA drives attached, they should be detected by the installer. Choose one of your preference and continue the install.

12. Once the installe reboots, you may need to select the SATA drive to continue on to Windows 10.

13. Setup Windows 10 normally, and boot into your now Windows 10 system!



NOTES:

1. There are no Windows ARM64 AMD GPU drivers.

2. The framebuffer supports at least my 3440x1440 monitor at native resolution.

3. If you have a DisplayLink Dock, you can use it for multimonitor support and 2D acceleration. 2D and light 3D Steam games load, and games like AoE II.
   Use the link below to get the latest drivers for your device:
   
        https://www.synaptics.com/products/displaylink-graphics/downloads/windows
