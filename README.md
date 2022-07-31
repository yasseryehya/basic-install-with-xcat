# basic-install-with-xcat
basic unattended Centos8 ISO image installation with xCat

# Configure the Base OS Repository
- I coppied the iso image to /tmp on the management node
- mounted the iso to /mnt/iso/centos8 on the management node
- created a yum repo file /etc/yum.repos.d/centos8.repo

# Configure the Management Node
- hostnamectl set-hostname xcatmn.cluster.com
- didn't set the IP of the MN to static in /etc/sysconfig/network-scripts/ifcfg-<dev>
- didn't configure /etc/resolv.conf

# Automatic Install Using go-xcat

## Download the go-xcat tool using wget:
- wget https://raw.githubusercontent.com/xcat2/xcat-core/master/xCAT-server/share/xcat/tools/go-xcat -O - >/tmp/go-xcat
- chmod +x /tmp/go-xcat

## Run the go-xcat tool:
- /tmp/go-xcat install
- source /etc/profile.d/xcat.sh
- /tmp/go-xcat -x devel install

# configuring creds
- chtab key=system passwd.username=root passwd.password=root

# osimage definition
- copycds /tmp/CentOS-Stream-8-x86_64-20220513-dvd1.iso

# current site table values:
- domain=cluster.com
- forwarders=192.168.121.2
- master=192.168.121.143
- nameservers=192.168.121.143

# Initialize DHCP services
- chdef -t site dhcpinterfaces="ens160,virbr0"

# node definition and nodeset
- mkdef -t node -o cn1 arch=x86_64 mac="00:0C:29:16:54:EB" ip="192.168.121.170" netboot="xnba" groups="all"
- genimage centos-stream8-x86_64-netboot-compute
- packimage centos-stream8-x86_64-netboot-compute
- makehosts cn1
- makedns -n		# was asked to disable SELINUX (was disabled already!)
- nodeset cn1 osimage=centos-stream8-x86_64-netboot-compute

# create a new DHCP configuration file with the networks defined
- makedhcp -n
--> I got this: No dynamic range specified for 192.168.121.0. If hardware discovery is being used, a dynamic range is required.
--> next to work on: how to specify dynamic range for dhcp?
- makedhcp -a
