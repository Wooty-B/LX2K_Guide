# Installing Box86 & Box64 w/ Steam, Pi-Apps & Chroot Alternative

1. Enable Multiarch (32-bit Support)
	```
	sudo dpkg --add-architecture armhf
	sudo apt update && sudo apt upgrade
	```
2. Install Box86
	```
	mkdir x86; cd x86
	sudo apt install gcc-arm-linux-gnueabihf git build-essential cmake curl
	git clone https://github.com/ptitSeb/box86.git; cd box86
	mkdir build; cd build; cmake .. -DRK3399=1 -DCMAKE_BUILD_TYPE=RelWithDebInfo; make -j16
	sudo make install; cd ../..
	```
3. Install Box64
	```
	git clone https://github.com/ptitSeb/box64.git; cd box64
	mkdir build; cd build; cmake .. -DLX2160A=1 -DCMAKE_BUILD_TYPE=RelWithDebInfo; make -j16
	sudo make install; cd ../..
	sudo systemctl restart systemd-binfmt
	```
4. Configure and Install Steam
	```
	sudo nano /etc/profile.d/steam.sh
		export STEAMOS=1
		export STEAM_RUNTIME=1
	source /etc/profile.d/steam.sh
	wget https://steamcdn-a.akamaihd.net/client/installer/steam.deb
	sudo dpkg -i steam.deb
	sudo apt install libnm0 libtcmalloc-minimal4 \
		libgmp10:armhf \
		libgnutls30:armhf \
		libvorbisfile3:armhf \
		libsdl2-2.0-0:armhf \
		libxtst6:armhf \
		libbz2-1.0:armhf \
		libselinux1:armhf \
		libjpeg62-turbo:armhf \
		libcairo2:armhf \
		libfontconfig1:armhf \
		libxrender1:armhf \
		libxinerama1:armhf \
		libxi6:armhf \
		libxrandr2:armhf \
		libxcursor1:armhf \
		libxcomposite1:armhf \
		libfreetype6:armhf \
		libpng16-16:armhf \
		libopenal1:armhf \
		libsm6:armhf \
		libice6:armhf \
		libuuid1:armhf \
		libc6:armhf \
		libegl1:armhf \
		libgbm1:armhf \
		libgl1-mesa-dri:armhf \
		libgl1:armhf
	steam -no-browser .
	```
5. Install Wine & Winetricks (32bit Windows Executables)
	```
	cd ~
	wget https://dl.winehq.org/wine-builds/debian/dists/buster/main/binary-i386/wine-devel-i386_5.21~buster_i386.deb
	wget https://dl.winehq.org/wine-builds/debian/dists/buster/main/binary-i386/wine-devel_5.21~buster_i386.deb
	dpkg-deb -xv wine-devel-i386_5.21~buster_i386.deb wine-installer
	dpkg-deb -xv wine-devel_5.21~buster_i386.deb wine-installer
	mv wine-installer/opt/wine* ~/wine
	rm wine*.deb; rm -rf wine-installer
	echo -e '#!/bin/bash\nsetarch linux32 -L '"$HOME/wine/bin/wine "'"$@"' | sudo tee -a /usr/local/bin/wine >/dev/null
	sudo ln -s ~/wine/bin/wineboot /usr/local/bin/wineboot
	sudo ln -s ~/wine/bin/winecfg /usr/local/bin/winecfg
	sudo ln -s ~/wine/bin/wineserver /usr/local/bin/wineserver
	
	sudo chmod +x /usr/local/bin/wine /usr/local/bin/wineboot /usr/local/bin/winecfg /usr/local/bin/wineserver
	sudo apt-get install cabextract -y
	wget https://raw.githubusercontent.com/Winetricks/winetricks/master/src/winetricks
	sudo chmod +x winetricks && sudo mv winetricks /usr/local/bin/
	BOX86_NOBANNER=1 winetricks -q corefonts msxml3 vcrun2010 dotnet20sp1 msvcrt40 quartz dxvk physx
	```
6. Install Wine64 (64bit Windows Executables) [NOT TESTED!!!]
	```
	wget https://dl.winehq.org/wine-builds/debian/dists/buster/main/binary-amd64/wine-devel-amd64_5.21~buster_amd64.deb
	wget https://dl.winehq.org/wine-builds/debian/dists/buster/main/binary-amd64/wine-devel_5.21~buster_amd64.deb
	dpkg-deb -xv wine-devel-amd64_5.21~buster_amd64.deb wine-installer
	dpkg-deb -xv wine-devel_5.21~buster_amd64.deb wine-installer
	mv wine-installer/opt/wine* ~/wine64
	rm wine*.deb; rm -rf wine-installer
	sudo ln -s ~/wine64/bin/wine64 /usr/local/bin/wine64
	sudo ln -s ~/wine64/bin/wineboot /usr/local/bin/wineboot64
	sudo ln -s ~/wine64/bin/winecfg /usr/local/bin/winecfg64
	sudo ln -s ~/wine64/bin/wineserver /usr/local/bin/wineserver64
	sudo chmod +x /usr/local/bin/wine64 /usr/local/bin/wineboot64 /usr/local/bin/winecfg64 /usr/local/bin/wineserver64
	wineboot64 --init
	```
6. Install Pi-Apps
	```
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
	```
7. 32bit ARM (armhf) Chroot Setup [Alernative/Addition to Multiarch]
   	```
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
    	usermod -a -G sudo <username>
    	su - <username>
    	nano ~/.bashrc
	
    	    	export LANGUAGE="C"
	    	export LC_ALL="C"
	    	export DISPLAY=:0
	    	export CPU_MHZ=2000
  	
    	exit
    	exit
 	```
