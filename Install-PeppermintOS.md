# Installing PeppermintOS

   1. Download Debian netinstall ISO
         ```
         PeppermintOS [Debian] aarch64 current: https://sourceforge.net/projects/peppermintos/files/isos/XFCE/PeppermintOS-Debian_ARM_64.iso/download
         ```
         ```
         PeppermintOS [Devuan] aarch64 current: https://sourceforge.net/projects/peppermintos/files/isos/XFCE/PeppermintOS-devuan_arm_xfce.iso/download
         ```
   2. Flash the ISO to a USB drive
         ```
         dd if=~/Downloads/PeppermintOS-*version*.iso of=/dev/sdX
         ```
         
         Alternatively, you can use Gnome Disks, balenaEtcher, or an imaging tool of your choice.
         
   3. Restart and boot from the USB drive that was created. (See: BIOS Navigation)
   
   4. When the installer loads, press [E] key on the first option labelled "PeppermintOS *version* - Live". Add the following to the line starting with 'linux':
         ```
         amdgpu.pcie_gen_cap=0x4 iommu.passthrough=1
         ```

         Then press [CTRL]+[X] of [F10] to continue installer.

   5. The Live environment should now be loaded, and you may have heavy graphical artifacting depending on GPU used.

         Open the "Install PeppermintOS" icon on the desktop to continue installing.

   6. Reboot. When bootloader/GRUB appears, press [E] and repeat Step 4.

   7. Once logged in, complete the following steps to prevent crashing [AMD GPU's]
         ```
         nano /etc/default/grub
         ```
         ```
         GRUB_CMDLINE_LINUX_DEFAULT="amdgpu.pcie_gen_cap=0x4 amdgpu.noretry=0"
         ```
         ```
         sudo update-grub
         ```
   8. Install the following to enable 'sddm' login manager with 'Cinnamon DE'. (least artifacting on AMD GPU's)
         ```
         sudo apt install task-cinnamon-desktop sddm
         ```
         
   9. Reboot and log into your new install of PeppermintOS!


   NOTE: If you are using an AMD GPU, you will probably need to apply the Mesa patch on under "Post-Install Recommendations" on the main page.