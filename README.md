This is the description of my video about the XIAOMI Mi Router 4A gigabit edition

you can find the video [here on youtube](https://www.youtube.com/watch?v=a4fDwG3aEb8).


download location for OpenWrtInvasion:

https://github.com/acecilia/OpenWRTInvasion

running it as a docker container:

``` bash 
docker build -t openwrtinvasion https://github.com/acecilia/OpenWRTInvasion.git
docker run --network host -it openwrtinvasion
```

It will ask for the IP address (by default this is 192.168.31.1)
in the video, I had set this to an address in my LAN (192.168.139....)
The stok can be copied from the Web interface URL

```
telnet 192.168.31.1
user:root
pwd: root
```

Hoddys Guide links:
the video is here https://www.youtube.com/watch?v=VxzEvdDWU_s
His blog site: https://hoddysguides.com/installing-openwrt-xiaomi4a/
the debrick tool: https://hoddysguides.com/xiaomi-debrick-tools-all/

Test scenario:

on the router:

``` bash
top
```

on the server run iperf3 in server mode:

``` bash
iperf3 -s -p 5201
iperf3 -s -p 5202
```

(this runs two instances, one on port 5201, the other on 5202)

on the client:

``` bash
iperf3 -c (IP of the server) -p (portnumber)
```

(in the video I added a route manually from my 192.168.139.0/24 LAN to another network 10.139.139.0/24 as the router was not my default gateway):

``` bash
ip route add 10.139.139.0/24 via 192.168.139.98
```

In order to scan a device for open ports (from the outside) just type 

``` bash
nmap (IP address of the device to scan), e.g.
nmap 192.168.31.1
```

The linux kernel version on a device can be shown with

``` bash
uname -a
cat /proc/sys/kernel/version
cat /proc/sys/kernel/osrelease
```

pci devices can be shown using lspci, e.g.

``` bash
lspci -k
```

loaded modules such as hardware drivers can be shown with

``` bash
lsmod
```

running processes can be shown with the ps command. If you want to be able to scroll, then you can pipe to the "less" or "more" command:

``` bash
ps | less
ps | more
```

`netstat` shows open ports and sockets. the parameters are:

-t show tcp connections
-u show udp connections
-p show the running/using process
-n do not resolve names
-l show listening ports

listening sockets are shown with 

``` bash
netstat -tulpn
```

established connections can be shown with 

``` bash
netstat -tupn
```

the name of a host can be looked up in DNS with nslookup

``` bash
nslookup 3.127.110.143
nslookup www.google.de
```

(this works forward and reverse name < - > IP)

In order to query the mqtt server I used

``` bash
mosquitto_sub -v -h 3.127.110.143 -t '#'
```

The waving EU flag has been taken from youtuber IndyX66
https://www.youtube.com/watch?v=MmS1xZElAqw
The music "ode to joy" is from the youtube library and performed by Cooper Cannell

Many thanks to the artists !!!!!

query the OpenWrt config:

``` bash
uci show
```

The QoS lua script is in `/usr/sbin/miqosd`

Using `nc/netcat` to transfer something over the network:

on the router

``` bash
echo hello | nc (IP address) 10000
```

or

``` bash
cat /proc/mtd | nc (IP address) 10000
```

where (IP address is the address of the workstation where you run the listening netcat command on) - 10000 is just any free port I use

on the work station - listen to that port 10000:

``` bash
netcat -l 10000
```

If we want to transfer all 10 mtdblock devices we can do 

on the receiver (your workstation):

``` bash
for i in {0..9} ; do nc -l 10000 > "mtdblock${i}" ; done
```

on the sender (the router)

``` bash
for i in 0 1 2 3 4 5 6 7 8 9 0 ; do cat "/dev/mtdblock${i}" | nc (IP_OF_THE_RECEIVER) 10000 ; sleep 1 ; done
```

The receiver needs to be ready before we send, therefore the sleep 1.

Steps to flash OpenWrt:

1. copy the link to the Sysupgrade image from the firmware selector
https://firmware-selector.openwrt.org/

2. download the image to the router

``` bash
cd /tmp
curl -4 --insecure (URL FROM ABOVE) >openwrt.bin
```

3. Write the image into the mtd device OS1:

``` bash
mtd -e OS1 -r write openwrt.bin OS1
```

grab a coffee, wait, reboot router

ssh into the router with OpenWrt

``` bash
ssh root@192.168.1.1
```

update packages and install luci

``` bash
opkg update
opkg install luci
```

just to make sure that luci starts on reboot

``` bash
/etc/init.d/uhttpd enable
/etc/init.d/uhttpd start
```

install htop for later

``` bash
opkg install htop
```
