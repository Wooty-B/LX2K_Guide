# Installing Debian

   1. Download Debian netinstall ISO
         ```
         Debian aarch64 current: https://cdimage.debian.org/debian-cd/current/arm64/iso-cd/
         ```
   2. Flash the ISO to a USB drive
         ```
         dd if=~/Downloads/debian-*version*-arm64-netinst.iso of=/dev/sdX
         ```
         
         Alternatively, you can use Gnome Disks, balenaEtcher, or an imaging tool of your choice.
         
   3. Insert and boot from the USB drive
         ```
         [ESC at Boot] > Boot Maintenance Manager > Boot From File > (USB Device ID) > EFI > AARCH64.EFI; [ENTER]
         ```
   4. Install Debian and restart when prompted
         
   5. Once logged in, complete the following steps to prevent crashing [AMD GPU's]
         ```
         nano /etc/default/grub
         ```
         ```
         GRUB_CMDLINE_LINUX_DEFAULT="amdgpu.pcie_gen_cap=0x4 amdgpu.noretry=0"
         ```
         ```
         sudo update-grub
         ```
   6. Install the following to enable GPU hardware acceleration [All GPU's]
         ```
         sudo apt install firmware-linux
         ```
         
   NOTE: If you are using an AMD GPU, you will probably need to apply the Mesa patch on under "Post-Install Recommendations" on the main page.
