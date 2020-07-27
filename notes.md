**Notes**

The mictrack config cable is a prolific usb to serial chip, it is supported by linux 

It should have a label "config" on it, otherwise it has larger than normal USB connector (for the serial chip)
the regular cables in the mictrack box are only for chargning

[prolific linux driver discovery](http://programmingknowledgeblog.blogspot.com/2013/12/prolific-usb-to-serial-comm-port-linux.html) copied below

Once you have confirmed you have the serial dev available, for me it's /dev/ttyUSB0
with a serial terminal program send the following commands

Assure the device is charged

send this command to read current configuration, you may need to have your terminal set to local echo, software flow control, 115200 baud

    cmd:rconf,1#

set the apn (ATT example), no username or password

    cmd:iot0119.com.attz,,#

read config to assure apn was set

    cmd:rconf,1#

set the IP and PORT of cloud application, in this case it will be traccar on azure

    cmd:data,13.82.224.47,5191

now set the protocol, it's UDP no ACK

    cmd:protocol,0,0#

then set the mode 

for debugging while plugged into charger i use the always on mode

    cmd:mode,6,10#     

for power saving i use mode 7 and update every min, shake the device for 10 seconds and it comes alive

    cmd:mode,7,60#

For debugging and reviewing logs

turn log on with 

    cmd:logprint,1#

off with 

    cmd:logprint,0#



**Backup information**

mictrack commands
```
Item	Command format	Example
APN 	cmd:apn,apn, apn user, apn password#	cmd:apn,iot.nb,,#
cmd:apn,internet,internet,internet#
IP/Port	cmd:data,IP:Port# 	cmd:data,113.98.254.179:7700#
Mode 0	cmd:mode,0,T# 	cmd:mode,0,8#       #once a day report 8=8AM (24 hour)
Mode 1	cmd:mode,1,T# 	cmd:mode,1,10#      #deep sleep interval 10=10 seconds
Mode 5	cmd:mode,5,T# 	cmd:mode,5,10#      #GPS sleep mode 10=10 seconds
Mode 6	cmd:mode,6,T# 	cmd:mode,6,10#      #GPS always on 10=10 seconds
Mode 7	cmd:mode,7,T# 	cmd:mode,7,10#      #power saving mode 10=10 seconds
GSM Only	cmd:netlock,1,1,2# 	cmd:netlock,1,1,2# 
Cat M1 Only	cmd:netlock,2,3,0#	cmd:netlock,2,3,0#
NB-IoT Only 	cmd:netlock,3,3,1# 	cmd:netlock,3,3,1# 
AUTO	cmd:netlock,0,0,2# 	cmd:netlock,0,0,2# 
RESTART	cmd:reboot,0# 	cmd:reboot,0# 
RESET	cmd:factoryreset,1# 	cmd:factoryreset,1# 
Log ON	cmd:logprint,1#	cmd:logprint,1#
Log OFF	cmd:logprint,0#	cmd:logprint,0#
Read Config	cmd:rconf,1#	cmd:rconf,1#
UDP (No ACK)	cmd:protocol,0,0#	cmd:protocol,0,0#
UDP (with ACK)	cmd:protocol,0,1#	cmd:protocol,0,1#
TCP (No ACK)	cmd:protocol,1,0#	cmd:protocol,1,0#
TCP (with ACK)	cmd:protocol,1,1#	cmd:protocol,1,1#
Check network	AT+QNWINFO	AT+QNWINFO
```


```

**Prolific technology USB serial driver Linux**

When you connect USB (or serial to USB) to your Linux device and if it recognises the USB but A in a little different manner e.g usbdev1.XX and 
Not in the expected format like ttyUSB0 (ttyUSB1 , ttyUSB2 .....) . I am lising Out the the Steps which I have followed to Solve the problem.

First I checked What are the Devices are present in my dev directory by following command

# ls /dev/

and the response is as follows

usbdev1.1
usbdev1.2
usbdev1.3
usbdev1.4
usbdev1.5
ttyS0
ttyS1
ttyS2
ttyS3

Now I Give the Following command to Know the Devices specifications

# lsusb

The response is as follows 

Bus 001 Device 001: ID 1d6b:0002 Linux Foundation 2.0 root hub
Bus 001 Device 002: ID 1a40:0101 TERMINUS TECHNOLOGY INC. 
Bus 001 Device 003: ID 067b:2303 Prolific Technology, Inc. PL2303 Serial Port

Bus 001 Device 004: ID 0403:6001 Future Technology Devices International, Ltd FT232 USB-Serial (UART) IC
Bus 001 Device 005: ID 0403:6001 Future Technology Devices International, Ltd FT232 USB-Serial (UART) IC

So I know which device I am looking for . The Device is "Bus 001 Device 010: ID 067b:2303 Prolific Technology, Inc. PL2303 Serial Port"

and its 'usbdev1.3'


Now I Give the followin command to find if the PL2303 driver is present on my kernal or not

# find /lib/modules -name *2303*

this replys me 

/lib/modules/3.2.29-00165-gd626b6d/kernel/drivers/usb/serial/pl2303.ko

So the .ko file is present .Now All I need to do is to Initialise the pl2303.ko to recognize my USB as ttyUSB*

Give the following command to initialize the pl2303.ko 

# insmod /lib/modules/3.2.29-00165-gd626b6d/kernel/drivers/usb/serial/pl2303.ko

Now once again check fot the device 

# ls /dev/

and This time I gives me the response 

usbdev1.1
usbdev1.2
usbdev1.4
usbdev1.5
ttyS0
ttyS1
ttyS2
ttyS3
ttyUSB1

Now I can Use ttyUSB1 as normar USB .


I also created the bash file with the content 

 #!/bin/bash         

insmod /lib/modules/3.2.29-00165-gd626b6d/kernel/drivers/usb/serial/pl2303.ko

and put it into the /etc/profile.d/ folder with 'chmod a+x' privlages
-----------------------------------------------------------
Following Problem 
insmod: error inserting 'pl2303.ko'
insmod error -- Invalid module format
prolific technology usb serial driver linux
Prolific USB-to-Serial Comm Port LINUX DRIVER
prolific usb serial driver linux ubuntu fedora
Fedora Hardware :: Prolific Pl2303 + Serial Modem - Dials But PPP Won't Authenticate
map usbdev1.xx to ttyusb
As a side note, I went to the root file system, there is no /dev/ttyUSB anything...
There is /dev/usbdev1.1, /dev/usbdev1.2, etc...does this help??
usb1
usb2
usb3
usbdev1.1_epXX <-Several
usbdev1.2_epXX <-Several
usbdev2.1_epXX <- Several
usbdev3.1_epXX <- Several
Bus 003 Device 009: ID 1199:6880 Sierra Wireless, Inc.
Bus 003 Device 001: ID 1d6b:001
Bus 002 Device 001: ID 1d6b:001
Bus 003 Device 001: ID 1d6b:002
USB Serial Converter support
insmod: can't read 'usbserial': No such file or directory
insmod: error inserting 'usbserial.ko': 
insmod: Invalid module format
copy linux ubuntu usb driver from one computer to another
modprobe  vendor=0x067b product=0x2303
activate PL2303 Serial Port linux
PL2303 serial port.

```