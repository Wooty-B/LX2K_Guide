1. Computer Won’t Boot:
    Be sure that you have the right image: [LX2K UEFI Images](https://images.solid-run.com/LX2k/lx2160a_uefi) Pick the image matching your RAM speed.

2. GPU:
    Make sure you are using an AMD Graphics Core Next (GCN1-5) or newer GPU, or nVidia 1K series and up w/ Linux Kernel 5.18+. Older GPU's aren't supported.

3. No Power LED:
    Make sure that connector PLED+ is connected to pin 2 and that connector PLED- is put on pin 5. See the bottom of this picture:
    https://marcin.juszkiewicz.com.pl/files/2021/02/honeycomb-layout.webp 4

4. Computer Powers Off Randomly/Under Thermal Load:
set “thermal.crt=-1” in /etc/default/grub in the GUB_CMDLINE_LINUX_DEFAULT like this: GRUB_CMDLINE_LINUX_DEFAULT=“loglevel=4 thermal.crt=-1”

5. GPU Power Issues (AMD):
    Set the value “amdgpu.pcie_gen_cap=0x4” for GRUB_CMDLINE_LINUX_DEFAULT in /etc/default/grub

6. No Keyboard on Login:
    Make sure that the keyboard is plugged into the lower/bottom USB-port, and that the mouse is plugged into the upper USB-port.

7. USB Headset/Sound Card: Run the following from the terminal: 
    “touch /etc/modprobe.d/alsa.conf” "echo “options snd slots=snd-usb-audio,snd-hda-codec-hdmi” >> /etc/modprobe.d/alsa.conf

8. I/O Error on /dev/sda:
    Make sure your SATA cable is rated for SATAIII speeds.

9. Void Linux Boots to Emergency Shell:
    Make sure that you have the newest tarball version of Void Linux (Ex. /live/20210316/))

10. Void Linux Has No WiFi: Run the following from the terminal: 
    “wpa_passphrase SSID password /etc/wpa_supplicant/wpa_supplicant.conf” “wpa_supplicant -B -i wlan0 -c /etc/wpa_supplicant/wpa_supplicant.conf” “ln -s /etc/sv/wpa_supplicant /var/service/” “ln -s /etc/sv/dhcpcd /var/service/”

11. Void Linux "operation not permitted" during xbps-install: Run the following from the terminal: 
    “export SSL_NO_VERIFY_PEER=1”

12. Void Linux Sync Time to Hardware Clock: Run the following from the terminal: 
    “xbps-install ntp openntpd” “ntpdate pool.ntp.org” “openntpd -s” “hwclock -wl” “echo “openntpd-s; hwclock -wl” >> ~/.xinitrc”

13. Void Linux Ethernet:
    Make sure you are using a generic 5.14+ Linux Kernel or else Ethernet may not work.

14. Void Linux VSync: Run the following from the terminal: 
    “xbps-install picom” “echo “picom --backend-glx --vsync” >> ~/.xinitrc”

15. Void Linux ntpd OpenRC:
    "-s" has to be taken out of NTPD_OPTS in /etc/conf.d/ntpd. "doas ntpd -s" is set in .xinitrc and "permit nopass user cmd ntpd" is set in /etc/doas.conf.

16. Void Linux "momount" issue:
    The first 2 letters get output to the beginning of a command, Ex.: Trying to tab-autocomplete the command "mount" -> mou -> (TAB Key) -> momou. The letters "mo" do     not interfere with the input command. Starship contains a foreign prompt-symbol ("❯").
    ~/.config/starship.toml fixes this behaviour by configuring the prompt to have normal system-known symbols.

17. Sound crackling/popping/distortion: Run the following from the terminal: 
    "cd /usr/src/linux/ && doas make menuconfig" Device Drivers > Sound Card Support > ALSA: Pre-allocated buffer size for HD-audio driver = 0"   (CONFIG_SND_HDA_PREALLOC_SIZE=0).

18. udev Incorrect Time Halts i2C Bus:
    CONFIG_FW_LOADER_USER_HELPER must be disabled in the Kernel

19. PWM Fans Increase to Max:
    Set i2c-imx either as M or Y in the Kernel config. 

20. Audio Over DisplayPort With ALSA:
    "cat /proc/asound/cards" -> Card #; "cat /proc/asound/devices" -> Device #
    Edit one of the following files, ~/.asoundrc or /etc/asound.conf:

    defaults.pcm.!card 2
    defaults.pcm.!device 8

21. Android Emulator; Waydroid for Debian 11+/Ubuntu 21.10+:
    Follow the Guide on https://docs.waydro.id/usage/install-on-desktops.

    Edit the file /etc/gbinder.conf:

    [General]
    ApiLevel = 29
