NOTE: THIS IS A WIP. Information here is being updated and currently only contains Debian based instructions. Instructions currently DO NOT cover virt-manager.

   1. Run the following commands:
   
            "sudo apt update && sudo apt upgrade"
            "sudo apt install qemu-kvm libvirt-daemon-system libvirt-clients bridge-utils"
    
   2. Create and navigate to directory for downloading Windows ISO:
   
            "mkdir -p ~/Downloads/iso/windows_10"
            "cd ~/Downloads/iso/windows_10"
    
   3. Visit "https://uupdump.net/" and search for "Windows arm64" using the field at the top.
    
   4. Click on any Windows 10 Insider Preview image. (all should be arm64)
    
   5. Choose your language then click Next.
    
   6. Check your desired Windows 10 Editions then click Next. (I check both, but filesize may be larger)
    
   7. Leave Download and Convert to ISO checked, and then click "Create download package".
    
   8. Once downloaded, extract the zip into the directory we created earlier.
    
   9. Run the following in the root of the extracted files:
   
            "chmod +x uup_download_linux.sh"
            "sudo apt install aria2 cabextract wimtools genisoimage"
            "./uup_download_linux.sh"
    
   10. The download may take a while, once completed, you will have an image file located in your current directory:
   
            "<VERSION>_MULTI_ARM64_<REGION>.ISO"
    
   11. Create and navigate to directory where your VM will run:
   
            "mkdir -p ~/.local/share/qemu/vm/windows_10"
            "cd ~/.local/share/qemu/vm/windows_10"
   
   12. Copy and rename Windows 10 ISO into your VM directory:
   
            "cp ~/Downloads/iso/windows_10/<VERSION>_MULTI_ARM64_<REGION>.ISO ~/.local/share/qemu/vm/windows_10/install.iso"
    
   13. Download official QEMU ARM64 UEFI BIOS or use my 1080p modified one:
   
        Official: "wget http://snapshots.linaro.org/components/kernel/leg-virt-tianocore-edk2-upstream/latest/QEMU-AARCH64/RELEASE_GCC5/QEMU_EFI.fd"
   
        Wooty 1080p Framebuffer: <Link>
    
   14. Download VirtIO Drivers:
            
            "wget https://fedorapeople.org/groups/virt/virtio-win/direct-downloads/archive-virtio/virtio-win-0.1.204-1/virtio-win-0.1.204.iso" 
    
   15. Create a 30GB+ disk image:
   
            "qemu-img create -f qcow2 system.img 40G"
    
   16. Create a script in your current directory named "run.sh":
  
            "nano run.sh"
   
          qemu-system-aarch64 \
          -cpu host \
          -enable-kvm \
          -M virt-2.12 \
          -smp 4 \
          -m 4G \
          -bios QEMU_EFI.fd \
          -device ramfb \
          -device nec-usb-xhci \
          -device usb-kbd \
          -device usb-mouse \
          -device usb-tablet \
          -device virtio-blk,drive=system \
          -drive if=none,id=system,format=raw,file=system.img \
          -device usb-storage,drive=drivers \
          -drive if=none,id=drivers,media=cdrom,file=virtio-win-0.1.204.iso \
    
   17. Run the following to make the script executable:
   
            "chmod +x run.sh"
    
   18. Run the script to launch your VM:
   
            "./run.sh"
    
   19. Press Space whenever prompted to launch the Windows 10 Installer.
    
   20. Start the installer as usually, skipt the key activation, and choose custom.
    
   21. When at the disk install screen, choose "Load Drivers" and load the following directory:
   
            "virtio-win-0.1.204>viostor-w10>ARM64"
    
   22. Install the RedHat VirtIO driver, it will bring you back to the Disk Select screen.
    
   23. Chose "Load Drivers" again and load the following directory:
   
            "virtio-win-0.1.204>NetKVM>w10>ARM64"
    
   24. Install the RedHat VirtIO driver, it will bring you back to the Disk Select screen.
    
   25. Partition your newly found disk and start installing Windows.
    
   26. Once the VM reboots, it should continue by itself into the Windows setup as usual.
    
   27. You can now remove the following lines from your "run.sh" script"
   
        -device usb-storage,drive=drivers \
        -drive if=none,id=drivers,media=cdrom,file=virtio-win-0.1.204.iso \
    
   B. Set up Linux Share directory
    
   1. Install Samba using the following command:
   
            "sudo apt install samba"
    
   2. Add the following to your "run.sh" script:
   
        -net user,smb=/directory/to/ntfs/folder \
        -net nic,model=virtio \
    
   3. Start your VM using the "run.sh" script and log in to Windows.
    
   4. Open "This PC" from the File Explorer and click "Map Network Drive" from the "Computer" tab.
    
   5. Select a drive letter of your choice and use the following as the folder:
   
            "\\10.0.2.4\qemu"
    
   6. Name your drive and click Finish.
    
   7. You should now have bi-directional access to the folder you chose in Step 2.
