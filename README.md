
sudo apt-get install software-properties-common

sudo add-apt-repository -y ppa:mercurial-ppa/releases

sudo apt-get update

sudo apt-get install -y python-pip python-dev

sudo pip install mercurial --upgrade

	## Debian 11

	sudo apt install python3-venv python3-pip 

	sudo pip3 install mercurial --upgrade

## Download the ethercat master source files

$ hg clone http://hg.code.sf.net/p/etherlabmaster/code ethercat-hg

$ cd ethercat-hg

$ hg update stable-1.5 (Check new version at https://sourceforge.net/projects/etherlabmaster/)

## Create or edit your ~/.hgrc file and ensure it has at least the following contents:
 	
	[extensions]
 	mq =
 
$ hg qpush -a

## Get Ethernet infomation

$ sudo lspci -v 
	
	Example: My network is Intel Pro/100, Kernel driver in use: e1000e
	
## Build
$ cd etherlab

$  ./bootstrap

$ ./configure --disable-8139too --enable-e1000e 

	error: Failed to find Linux sources. Use --with-linux-dir!

	$ sudo apt install linux-headers-$(uname -r)

	$ ls -l /usr/src/linux-headers-$(uname -r)

	./configure --with-linux-dir=/usr/src/linux-headers-$(uname -r) --enable-8139too=no --enable-e1000e --enable-wildcards=yes

$ make

$ make modules

$ sudo make install

$ sudo make modules_install

$ sudo /sbin/depmod



## Configure your environnement

$ sudo mkdir /etc/sysconfig

$ sudo cp script/sysconfig/ethercat /etc/sysconfig

$ sudo cp script/init.d/ethercat /etc/init.d/ethercat

$ sudo nano /etc/sysconfig/ethercat

	$ ip address (get Mac address)
	
	In the file, enter the Ethernet devices Mac address in the MASTER0_DEVICE variable and the driver in DEVICE_MODULES variable

	MASTER0_DEVICE="e8:40:f2:3e:73:b4"

	DEVICE_MODULES="e1000e"

## The EtherCAT master service can now be started, restarted and stopped using

sudo chmod a+x /etc/init.d/ethercat

sudo /etc/init.d/ethercat start

	ERROR: could not insert 'ec_master': Required key not available (fix this issue is to disable Secure Boot in UEFI (BIOS) settings)
	
sudo /etc/init.d/ethercat restart

sudo /etc/init.d/ethercat stop

See output messages with dmesg:

	dmesg

## To run the service without root, a rule file has to be created 

cd /etc/udev/rules.d/

nano 98-EtherCAT.rules

In the file add the line: “KERNEL==“EtherCAT[0-9]* “ , MODE=”0664” , GROUP=”users”. 

## Alternatively, to run the service without root, type the following line in the command prompt

echo 'KERNEL=="EtherCAT[0-9]*" , MODE="0664" , GROUP = "users"' | sudo tee /etc/udev/rules.d/98-EtherCAT.rules

## Start EtherCAT master at startup

sudo update-rc.d ethercat start 51 S .

# Test EtherCAT with EK1100, EL2042, EL1004, EL2089

## Start EtherCAT Mater

sudo /etc/init.d/ethercat start

## Get infomation EK1100

sudo /opt/etherlab/bin/ethercat slaves

sudo /opt/etherlab/bin/ethercat mater

sudo /opt/etherlab/bin/ethercat pdos
