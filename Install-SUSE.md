# Installing openSUSE

   1. Download openSUSE Tumbleweed or Leap aarch64 ISO
         ```
         openSUSE Website: https://www.opensuse.org/
         ```
   2. Flash the ISO to a USB drive
         ```
         dd if=~/Downloads/openSUSE-*edition*-*type*-aarch64-Current.iso of=/dev/sdX
         ```
         
         Alternatively, you can use Gnome Disks, balenaEtcher, or an imaging tool of your choice.
         
   3. Insert and boot from the USB drive
         ```
         [ESC at Boot] > Boot Maintenance Manager > Boot From File > (USB Device ID) > EFI > AARCH64.EFI; [ENTER]
         ```
   4. Press "e" at Install Fedora GRUB menu and add the following to the ```linux``` line:
         ```
         amdgpu.pcie_gen_cap=0x4
         ```
   5. Install openSUSE and restart when prompted [Ignore any graphical issues during installer]
         
   6. Once logged in, complete the following steps to prevent crashing [AMD GPU's]
         ```
         nano /etc/default/grub
         ```
         ```
         GRUB_CMDLINE_LINUX_DEFAULT="amdgpu.pcie_gen_cap=0x4 amdgpu.noretry=0"
         ```
         ```
         sudo grub2-mkconfig -o /boot/grub2/grub.cfg
         ```
   7. Reboot, and the install is now complete!

   NOTE: If you are using an AMD GPU, you will probably need to apply the Mesa patch on under "Post-Install Recommendations" on the main page.
