1. The following commands will setup a 32-Bit ARM Chroot Environment:
    
    	sudo apt install schroot debootstrap
    	sudo mkdir -p /srv/chroot/ubuntu-armhf
    	sudo debootstrap --arch armhf --foreign hirsute /srv/chroot/ubuntu-armhf http://ports.ubuntu.com/ubuntu-ports
    	sudo chroot "/srv/chroot/ubuntu-armhf" /debootstrap/debootstrap --second-stage
    	sudo nano /etc/schroot/chroot.d/ubuntu-armhf.conf
    
	    	[ubuntu-armhf]
	    	description=Ubuntu Armhf 32-Bit chroot
	    	aliases=ubuntu-armhf
	    	type=directory
	    	directory=/srv/chroot/ubuntu-armhf
	    	profile=desktop
	    	personality=linux
	    	preserve-environment=true
	    	root-users=<username>
	    	users=<username>
      
    	sudo nano /etc/schroot/desktop/nssdatabases
    
	    	# System databases to copy into the chroot from the host system.
	    	#
	    	# <database name>
	    	#passwd
	    	shadow
	    	#group
	    	gshadow
	    	services
	    	protocols
	    	#networks
	    	#hosts
	    	#user
      
    	sudo nano /srv/chroot/ubuntu-armhf/var/lib/dpkg/statoverride
	    
   	   	root root 2755 /usr/bin/crontab
    	   	root messagebus 4754 /usr/lib/dbus-1.0/dbus-daemon-launch-helper

    	sudo schroot -c ubuntu-armhf
    	apt install nano
    	nano /etc/apt/sources.list
	    
   	    	deb http://us.ports.ubuntu.com/ubuntu-ports/ hirsute main restricted universe multiverse
	    	deb http://us.ports.ubuntu.com/ubuntu-ports/ hirsute-updates main restricted universe multiverse
	    	deb http://us.ports.ubuntu.com/ubuntu-ports/ hirsute-backports main restricted universe multiverse
	    	deb http://us.ports.ubuntu.com/ubuntu-ports/ hirsute-security main restricted universe multiverse
    
    	apt update && apt upgrade
    	nano ~/.bashrc
	    
   	    	export LANGUAGE="C"
	    	export LC_ALL="C"
	    	export DISPLAY=:0
	    	export CPU_MHZ=2000

    	adduser <username>
    	passwd <username>
    	usermod -a -G sudo <username>
    	su - <username>
    	nano ~/.bashrc
	
    	    	export LANGUAGE="C"
	    	export LC_ALL="C"
	    	export DISPLAY=:0
	    	export CPU_MHZ=2000
  
    	exit
    	exit
 
 2. Setup Pi-Apps
    
    	sudo schroot -c ubuntu-armhf
    	su - <username>
    	mkdir build32 && cd build32
    	apt install git wget cmake build-essential python3 gcc-arm-linux-gnueabihf xterm libnss3 python3-apt zenity libappindicator1 libnm0 libtcmalloc-minimal4 libsdl2-2.0-0 libxss1
    	git clone https://github.com/Botspot/pi-apps.git
    	cd pi-apps && ./install
    	mv ~/pi-apps/ ~/pi-apps.32
    	sudo nano /usr/local/bin/pi-apps 
	
  	    	#!/bin/bash
	    	/home/wooty/pi-apps.32/gui "$@"
  
  3. Install Box86 & Steam
    
    pi-apps
    [Install Box86]
    cd ~
    wget https://steamcdn-a.akamaihd.net/client/installer/steam.deb
    sudo dpkg -i steam.deb
    sudo nano /etc/profile.d/steam.sh
	
		export STEAMOS=1
		export STEAM_RUNTIME=1
      
    sudo apt install mesa-va-drivers mesa-vulkan-drivers mesa-common-dev libglx-mesa0 libglu1-mesa linux-firmware
    steam -no-browser
    
   NOTE: I am currently troubleshooting required packages and specifics to get a stablke chroot working for Steam. Running the above on a fresh install usually works, howerver after a while it breaks for me and I cannot figure out if it is a dependancy or related to my chroot. Usually it pertains to gl or vulkan.
