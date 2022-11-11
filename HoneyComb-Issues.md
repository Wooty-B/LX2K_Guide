1. Computer Won’t Boot:

    - Be sure that you have the right image: [LX2K UEFI Images](https://images.solid-run.com/LX2k/lx2160a_uefi) Pick the image matching your RAM speed.

2. GPU:

    - Make sure you are using an AMD Graphics Core Next (GCN1-5) or newer GPU, or nVidia 1K series and up w/ Linux Kernel 5.18+. Older GPU's aren't supported.

3. No Power LED:

    - Make sure that connector PLED+ is connected to pin 2 and that connector PLED- is put on pin 5.
    - See the bottom of this picture: https://marcin.juszkiewicz.com.pl/files/2021/02/honeycomb-layout.webp 4

4. Computer Powers Off Randomly/Under Thermal Load:

    - Add the following to /etc/default/grub: 
        GRUB_CMDLINE_LINUX_DEFAULT=“loglevel=4 thermal.crt=-1”
    - Then run: sudo update-grub

5. GPU Power Issues (AMD):

    - Add the following to /etc/default/grub:
        GRUB_CMDLINE_LINUX_DEFAULT=“amdgpu.pcie_gen_cap=0x4”
    - Then run: sudo update-grub

6. No Keyboard on Login:

    - Make sure that the keyboard is plugged into the lower/bottom USB-port, and that the mouse is plugged into the upper USB-port.

7. USB Headset/Sound Card:

    - Run the following ONLY if you've exhausted troubleshooting:
        echo “options snd slots=snd-usb-audio,snd-hda-codec-hdmi” >> sudo tee /etc/modprobe.d/alsa.conf

8. udev Incorrect Time Halts i2C Bus:

    - CONFIG_FW_LOADER_USER_HELPER must be disabled in the Kernel (Useful for Void Linux)

9. Increase PWM Fans to Max:

    - When compiling a custom kernel, set i2c-imx either as M or Y in the Kernel config.

10. Audio Over DisplayPort With ALSA:

    - Check the two locations below to obtain the CardID and DeviceID: 
        /proc/asound/cards
        /proc/asound/devices
    - Edit the file '~/.asoundrc' and input the following:
        defaults.pcm.!card *CardID*
        defaults.pcm.!device *DeviceID*

11. Computer won't boot external media:

    - Download a U-Boot image for your configuration here: https://images.solid-run.com/LX2k/lx2160a_build
    - Burn the downloaded image file to a MicroSD card and insert into your
    - Connect a Micro-USB cable from the Console port on your LX2K to a USB port on a second computer.
    - Use Putty, KiTTY or a TTY Client of your choice, and connect using the following settings:
        Serial (COM#)
        Speed: 115200
        Data Bits: 8
        Stop Bits: 1
        Parity: NONE
        Flow Control: XON/XOFF
    - Power on your LX2K and press ENTER during the boot countdown.
    - Type the following commands to clear the onboard SPI Flash:
        sf probe
        sf erase 0 0x4000000
    - Reboot, and you should be able to boot external media again.
    
