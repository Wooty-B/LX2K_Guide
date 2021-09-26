NOTE: For simplicity I will be focusing/referencing Ubuntu 21.04, however this can be easily applied to Debian 10 & 11 installs. Ubuntu was chosen for newer kernel, package repositories, and beginner friendliness.

   1. Download the Ubuntu 21.04 from the Ubuntu Server for ARM page.

NOTE (1): (20.04 and 20.10 will work; but may need kernel patches/update for less buggy experience on 20.04 and lower.

   2. Flash the iso with dd, BalenaEtcher, etc. to your USB drive.

   3. Insert the USB drive into a free USB slot on the LX2K and make sure a standard USB keyboard is plugged in.

NOTE (3): As mentioned previously, gaming keyboards may not register until Linux boots.

   4. Power on the LX2K and hit ESC when prompted to do so.

   5. Navigate to Boot Manager > Boot Options > (USB Device ID) > Boot > AARCH64.EFI; Press Enter.

NOTE (5): There are multiple ways to access bootable media, and is covered at the end of this guide.

   6. Click Install Ubuntu Server at the GRUB boot menu to start the installer.

   7. Run through the Ubuntu installer prompts just as you would on an x86 machine, installing to attached storage of your choosing.

   8. Once completed, reboot the machine and boot into your new Ubuntu install.

NOTE (8a): You may need to power off device completely, wait a few seconds, and boot again. NOTE: XMP Profile may not be working as I haven't played with it, I know in the past it was buggy but I may attempt someday.

NOTE (8b): If using this guide to install other distro's, note you may need Micro USB to access Linux in console mode if it doesn't bring up any framebuffer device; common AMD GPU quirk on ARM.

   9. You should now be logged in and sitting at the terminal. Make sure Ethernet is active and set up "/etc/resolv.conf" if needed. (i.e. for me: nameserver 8.8.8.8)

  10. First open "/etc/default/grub" with a text editor and make sure GRUB_CMDLINE_LINUX_DEFAULT has these values: GRUB_CMDLINE_LINUX_DEFAULT="amdgpu.pcie_gen_cap=0x4 amdgpu.noretry=0"

NOTE (10): The former fixes issues with crashing on boot, the latter prevents crashing in GL apps like Minecraft

  11. Now run the following: "apt update && apt upgrade && apt install linux-firmware tasksel"

NOTE (11a): "linux-firmware" installs the correct firmware for amdgpu and should not be skipped.

NOTE (11b): If you are installing Debian, set up your user with "adduser", then "usermod -a -G username sudo". Logout and back in.

  12. Run "sudo tasksel" and select Ubuntu Desktop, and any additional packages as well.

  13. Once completed, do a full system reboot/poweroff and then boot back into your new system.

  14. You should now be logged into your new aarch64 Ubuntu install with full GPU acceleration! You rock!
