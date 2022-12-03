# Installing Ubuntu

   1. Download Ubuntu Server aarch64
         ```
         Ubuntu Server for ARM: https://cdimage.debian.org/debian-cd/current/arm64/iso-cd/
         ```
   2. Flash the ISO to a USB drive
         ```
         dd if=~/Downloads/ubuntu-*version*-live-server-arm64.iso of=/dev/sdX
         ```
         
         Alternatively, you can use Gnome Disks, balenaEtcher, or an imaging tool of your choice.
         
   3. Insert and boot from the USB drive
         ```
         [ESC at Boot] > Boot Maintenance Manager > Boot From File > (USB Device ID) > EFI > AARCH64.EFI; [ENTER]
         ```
   4. Press [e] at "Install Ubuntu" and type the following at the end of the ```linux``` line
         ```
         iommu.passthrough=1 amdgpu.pcie_gen_cap=0x4
         ```        
   5. Install Ubuntu by following on-screen prompts; Installer may run sluggish, can skip updates at end
         
   6. Remove "cdrom" from sources.list [Replace "jammy" with your Ubuntu version]
         ```
         nano /etc/apt/sources.list
         ```
         Comment out:
         ```
         # deb [check-date=no] file:///cdrom jammy main restricted
         ```
         ```
         sudo apt update && sudo apt upgrade
         ```
         
   7. Run the following to install a graphical desktop automatically [Gnome, KDE, XFCE, etc]
         ```
         sudo apt install tasksel
         sudo tasksel
         ```
   8. Complete the following steps to prevent crashing [AMD GPU's]
         ```
         nano /etc/default/grub
         ```
         ```
         GRUB_CMDLINE_LINUX_DEFAULT="iommu.passthrough=1 amdgpu.pcie_gen_cap=0x4 amdgpu.noretry=0"
         ```
         ```
         sudo update-grub
         ```
   9. Install the following to enable GPU hardware acceleration [All GPU's]
         ```
         sudo apt install firmware-linux
         ```
         
   NOTE: If you are using an AMD GPU, you will probably need to apply the Mesa patch on under "Post-Install Recommendations" on the main page.
