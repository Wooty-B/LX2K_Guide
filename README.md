# Using HoneyComb LX2K as a Desktop
A guide for fully setting up the SolidRun LX2K with novice users in mind.

## Table of Contents:

A. Specs

B. Introduction

C. Important Notes

D. Prerequisites

E. Console Connection

F. Configuring LX2K

G. Installing an Operating System

H. Post-Install Recommendations

I. BIOS Navigation

J. Overclocking & Timings

## A. Specs

**CPU** - NXP Layerscape LX2160A 16-core ARM Cortex-A72; 2Ghz (Overclockable to 2.5Ghz+ all-core)

**RAM** - 2x DDR4 SODIMM slots @ up to 3200Mhz; ECC Compatable; Up to 64GB supported

**Storage** - 1x PCIe Gen. 3 x4 m.2; 4x SATA III ports; 1x 64GB Onboard eMMC; 1x SPI Flash, 1x UHS-1 MicroSD Slot

**Network** - 1x Gigabit Ethernet; 4x SFP+ 10Gb (40Gb support)

**PCIe** - 1x PCIe Gen. 3 x8 half-slot (open-ended, x16 card support)

**USB** - 2x USB3 ports; 1x USB3 header (2 ports); 1x USB2 header (2 ports)

**Interface** - 1x micro USB-OTG Console port; 1x micro USB-OTG Management port; 1x JTAG header

**Form Factor** - Mini ITX

## B. Introduction

The SolidRun HoneyComb LX2K (ClearFog) is a MiniITX form-factor ARM64 development board used for enterprise networking, security, autimotive, server applications, and as a developer workstation. Over the last year there has been much development to bring a stable desktop experience with AMD GPU support and it has finally reached a point where it is now viable as an ARM64 Desktop. Over the last few months the bugs have settled down, so I trashed my cobbled together Debian install and started fresh with Ubuntu 21.04. 
	
Using a Radeon WX 4100, many applications such as Blender, RBDOOM-3, Portal 2 and console emulators run laps around the Raspberry Pi 4, RockPro64, and Odroid XU-4. Many games run at 3440x1440 @ 60fps as long as you aren't going ham on the render options, Minecraft running close to 60fps @ 12-16 chunks, and OpenMW running @ 60fps with everything turned to max. And with Box86, there are many applications that run smoothly with Box86.
	
The guide below is a WIP, and I greatly appreciate any comments regarding how I can improve this page, and will try to answer any questions thrown my way.


## C. Important Notes

 - Onboard Gigabit Ethernet does not work out-of-the-box, must apply kernel patches or use USB Ethernet adaptor.
 - nVidia GPU's only support framebuffer at time of writing (no hardware acceleration)
 - SolidRun images can be downloaded as lx2160a_uefi (UEFI BIOS) -OR- lx2160a_build (U-Boot BIOS (like Rasperry Pi and SBC's))
 - The EDKII UEFI BIOS will sometimes hang at splash under certain reboot conditions; simply wait a few seconds and boot again
 - If you experience frequent crashing, no wake from sleep, etc., make sure GRUB Linux Defaults are set as well as disabling sleep/hybernate services.
 - At time of writing there appears to be minor window boarder and text artifacting on occasion; shouldn't affect overall experience
 - Try using a generic/stock kernel of 5.10 or higer; alternatively you can install an older kernel with SolidRun patches from the SolidRun GitHub page. [https://github.com/SolidRun/linux-stable]
 - Booting with some RGB Gaming keyboards may not register during POST and will not respond to input. Once booted into Linux drivers will pick up any non-standard keyboards. I.e.: You may need a 2nd generic USB keyboard to highlight BIOS options.
 - Use Wayland over X11 if at all possible, X has much more bugs with GPU acceleration and Wayland has the ability to launch X windows anyways.
 
 
## D. Prerequisites

You will need the following items
 - 512MB+ MicroSD card
 - 2GB+ USB Thumb Drive
 - USB Ethernet Adaptor (try sticking with generic, realtek based chips if possible)
 - Build computer/SBC with an SD slot or adaptor
 - (recommended) Micro USB cable
 - (recommended) Radeon GCN 5th Gen or lower
 - (recommended) Standard USB Keyboard (i.e.: A cheap usb keyboard)


## E. Console Connection

 - Serial (COM#)
 - Speed: 115200
 - Data Bits: 8
 - Stop Bits: 1
 - Parity: NONE
 - Flow Control: XON/XOFF

NOTE: Make sure to connect Micro USB cable to the Console port and not the MGMT (congratulations...) port.


## F. Configuring LX2K

1. Assuming your board has USB Ethernet adaptor, RAM, Storage, PSU and GPU (optional) installed, head here on your build computer [https://images.solid-run.com/LX2k] to download the Tianocore EDKII UEFI firmware.

2. On the images page, click the link "lx2160a_uefi" and select the appropriate image file.

*NOTE (2a): There are three parts of the filename to focus on: "2000" = CPU Speed, "xx00" = RAM Speed, "sd/flexspi" = Target Install. All have the same CPU speed, so make sure you grab the file with correct RAM speed and "sd" for MicroSD card.*

*NOTE (2b): flexspi_nor can be used to install to internal SPI Flash. [Haven't tested]*

*NOTE (2c): This image can be built from scratch and is explained at the bottom of this guide.*

3. Flash the img.xz file to your uSD card using dd or BalenaEtcher.

4. Insert the uSD into the back of the LX2K.

*NOTE (4): At this point you may begin using the board, you may now also connect over console via Micro USB. Using the Micro USB cable is useful for controlling BIOS over serial, troubleshooting boot issues, and installing some OS' that only work in console mode.*


## G. Installing an Operating System

1. [Debian/Ubuntu](Install-Ubuntu)

2. [Windows 10/QEMU](Install-Windows-Qemu)
	
	
## H. Post-Install Recommendations

Possbile Crash on Sleep/Suspend
	
- If the system crashes constantly on sleep/suspend event, use this command to disable:
	 	"sudo systemctl mask sleep.target suspend.target hibernate.target hybrid-sleep.target"
	 	
Install Newer Mesa, Improve GPU Performance/Issues
	
- Over the course of this boards life its recieved various Mesa patches to help fix quirks related to GPU/PCIe issues. Ubuntu and most systems Mesa packages are a little out of date for stability reasons. I use Oibaf's repository which contains up to date aarch64 builds, and can be added by doing the following:
	 	"sudo add-apt-repository ppa:oibaf/graphics-drivers && sudo apt update"
	 
Install Pi-Apps (Minecraft Java, Box86 & Box64 setup)
	
- Pi-Apps contains a few games such as full Minecraft Java, and some quality of life apps like Box86/64. I recommend this route to save time manually installing and figuring out the quirks. Pi-Apps can be installed from Github or by using:
 
	"wget -qO- https://raw.githubusercontent.com/Botspot/pi-apps/master/install | bash"

Enable x86_32 and x86_64 Compatability
	
- As mentioned above, Pi-Apps can automatically set up Box64 and Box86 (with auto armhf chroot). This means just enabling these will allow you to execute many x86 based linux programs, including Wine. A few things to note:
	 	1. Running Wine should work for many passive apps such as Notepad++ or DosBox, but because it's translating AND emulating don't expect any miracles.
	 	2. I recommend installing just Box86, then installing Box64 if needed. I have run into issues where, for example, launching AssaultCube with Box64 installed hangs when trying to connect to GL. Uninstalling Box64 let AssaultCube load 32bit libraries under Box86, booting the game properly.
	 	3. If you have any x86 applications scripted to run natively on aarch64. Box86 may try and take over, possibly breaking things.
	 	
Running Steam
	 
- YES! Steam launches and runs Source titles!!! I followed a few guides to get this running over the weekend and it involves setting up a 32-bit armhf chroot and installing Box86 there; multilib Box86 is apparently not the way to go at time of writing. Steam has to be launched with "steam -no-browser" due to it being in a pure 32-bit environment, which is why I hope a multilib solution arises that can handle vulkan lib wrapping. All Valve titles run, however games like HITMAN won't due to it needing 64bit libs. Again, with Box64 here under multilib we could work around these issues, maybe one day...

	
## I. BIOS Navigation

	NOTE: Upon powering up, press ESC at the splash to enter the BIOS menu.

	A. Select Language
		1. Standard English
		2. Standard French
		3. English
		4. Fench
	B. Device Manager
		1. Device List
			a. Tls Auth Configuration
				- Configures Client/Server TLS Certificates
			b. RAM Disk Configuration
				- Create a RAM storage device from system memory.
			c. O/S Hardware Description Selection
				- Select between ACPI mode or Device Tree (ACPI, better for UEFI; DT, better for U-Boot)
			d. Console Preference Selection
				- Boot the system in "Graphical" (video) or "Console" (serial) mode
			e. iSCSI Configuration
				- Configure iSCSI
			f. Network Device List
				- Select NIC and configure boot/protocol settings
	C. Boot Manager
		1. Boot Manager Menu
			- Lists all bootable devices including custom entries
	D. Boot Maintenance Manager
		1. Boot Options
			a. Add Boot Option
				- Select a bootable EFI file from device list to add to boot list
			b. Delete Boot Option
				- Remove an entry from the boot list
			c. Change Boot Order
				- Use + and - to change an entry's load order in the boot list
		2. Driver Options
			- Add, Remove, and Delete custom EFI boot drivers including load order
		3. Console Options
			- Change Console input/output devices, number of lines (width/height), baud rate, etc.
		4. Boot From File
			a. Boot From File
				- Select an EFI file from device list to immediatly boot from
		5. Boot Next Value
			- Select an entry from the boot list to load on next reboot
		6. Auto Boot Time-out
			- Enter a value for how long the Splash Screen is shown for (Default value is 10)
	E. Continue
		- Continue booting the system as normal
	F. Reset
		- Immediately reboot the system


## J. Overclocking & Timings

	1. Insert your MicroSD card into your build PC or LX2K.
	2. Run "sudo apt install device-tree-compiler acpica-tools" to install the dtc dependency we will need for building our BIOS.
	3. Navigate to a directory you want to use for building and run "git clone https://github.com/SolidRun/lx2160a_uefi.git"
	4. Enter the newly created "lx2160a_uefi" folder.
	5. Now we can build the Tianocore EDKII UEFI BIOS:
		a. To check dependencies and prepare build, run:
			"INITIALIZE=1 ./runme.sh"
		b. Below is a list of parameters that can be used after running the command above:
			- "SOC_SPEED=xxxx" / Sets the CPU Clock Speed (ex. SOC_SPEED=2200 sets CPU frequency to 2.2Ghz) (Default SOC_SPEED=2000)
			- "BUS_SPEED=xxx"  / Sets Bus frequency (ex. BUS_SPEED=800 sets Bus speed to 800Mhz) (Default BUS_SPEED=700)
			- "DDR_SPEED=xxxx" / Sets the DDR4 RAM timings (ex. DDR_SPEED=3000 locks your RAM to 3000Mhz) (Adjust according to your memory's factory speed)
			- "XMP_PROFILE=x"  / Enables or Disables XMP Profile (ex. XMP_PROFILE=1 enables XMP, XMP_PROFILE=0 disables XMP) (Default XMP_PROFILE=0)
			- "INITIALIZE=x"   / Used prior to building image to check dependencies are installed
		c. Select from the first four parameters above followed by "./runme.sh" to build your BIOS image.
			EXAMPLE: "SOC_SPEED=2400 DDR_SPEED=3200 XMP_PROFILE=0 ./runme.sh"
			Outputs an image with CPU Speed of 2.2Ghz, Bus @ 700Mhz, DDR4 SODIMM Speed @ 3.2Ghz w/ XMP Disabled named:
				"lx2160acex7_2200_700_3200_8_5_2_sd_xxxxxxx.img"
		d. There will be a newly created "images" folder in your current directory, open it to find an img file ready to flash!
		e. Simply flash the image with dd or BalenaEtcher to a MicroSD card of your choice, and insert it into the LX2K.
		f. Boot and test!
	
	
