WIP

# Installing Fedora

   1. Download Fedora ISO from the Fedora ARM page. (https://arm.fedoraproject.org/)

   2. Flash the iso with dd, BalenaEtcher, etc. to your USB drive.

   3. Insert the USB drive into a free USB slot on the LX2K and make sure a standard USB keyboard is plugged in.

NOTE (3): As mentioned previously, gaming keyboards may not register until Linux boots.

   4. Power on the LX2K and hit ESC when prompted to do so.

   5. Navigate to Boot Manager > Boot Options > (USB Device ID) > Boot > AARCH64.EFI; Press Enter.

   6. Press "e" at the GRUB boot menu and edit the Linux Defaults line to include the following:
   
          arm-smmu.disable_bypass=0 amdgpu.pcie_gen_cap=0x4 amdgpu.noretry=0

   7. Run through the Fedora installer, it prompts just as you would on an x86 machine, installing to storage of your choosing.

   8. Once completed, reboot the machine and boot into your new Fedora install.

   9. You should now be logged in and sitting at the terminal. Make sure Ethernet is active and set up "/etc/resolv.conf" if needed. (i.e. for me: nameserver 8.8.8.8)

  10. First open "/etc/default/grub" with a text editor and make sure GRUB_CMDLINE_LINUX_DEFAULT has these values:
  
            GRUB_CMDLINE_LINUX_DEFAULT="arm-smmu.disable_bypass=0 amdgpu.pcie_gen_cap=0x4 amdgpu.noretry=0"

  11. Now run the following:
  
            sudo dnf update && sudo dnf upgrade && dnf install linux-firmware

  12. Once completed, do a full system reboot/poweroff and then boot back into your new system.
