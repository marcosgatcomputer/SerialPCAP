Serial port capture to PCAP
===========

This tool can capture serial port traffic and store all data in PCAP format. It is later possible to open it by Wireshark and analyze it. It is also possible to use realtime mode with named pipe instead of file.

This tool was created to capture Modbus-RTU on RS-485 but can be used to any other similar traffic.

Tutorial on using this capture is on YouTube https://www.youtube.com/watch?v=YtudbhexPv8

Tool is only for command line,

usage: `mono SerialPcap.exe [options] <portName>`

Option | Description
------ | -----------
`-b, --baud=VALUE` | Serial port speed (default 9600)
`-y, --parity=VALUE` | o (=odd), e (=even), n (=none) (defaul none)
`-p, --stopbits=VALUE` | 1, 2 (defaul 1)
`-g, --gap=VALUE` | Inter frame gap in miliseconds (default 10)
`-d, --dlt=VALUE` | Data link type in pcap format (default 147)
`-o, --output=VALUE` | Output file prefix (defalut port name)
`--pipe` | Use named pipe instead of file
`-h, --help` | Show this message and exit

`portName` is `COM1`, `\\.\COM15` or `/dev/ttyUSB0` or similar definition.


It is possible to run this tool using Mono on Linux or using .Net framework on Windows.


Pipe (realtime) mode on linux
-----------
It is possible to run the application in pipe mode, so you can see realtime traffic in Wireshark. On linux, you should perform these commands

    mkfifo /tmp/wspipe
    wireshark -k -i /tmp/wspipe &
    mono SerialPcap -o /tmp/wspipe --pipe [options] <portName>

More info on Wireshark capture pipes can be seen on https://wiki.wireshark.org/CaptureSetup/Pipes


Latency
--------

You should adjust the latency timer value. From the version Ubuntu 16.04.2, the default latency timer of the usb serial is '16 msec'.

When you are going to use sync / bulk read, the latency timer should be loosen.
the lower latency timer value, the faster communication speed.
 
Note:
You can check its value by:
 $ cat /sys/bus/usb-serial/devices/ttyUSB0/latency_timer

If you think that the communication is too slow, type following after plugging the usb in to change the latency timer

Method 1. Type following (you should do this everytime when the usb once was plugged out or the connection was dropped)
$ echo 1 | sudo tee /sys/bus/usb-serial/devices/ttyUSB0/latency_timer
$ cat /sys/bus/usb-serial/devices/ttyUSB0/latency_timer

Method 2. If you want to set it as be done automatically, and don't want to do above everytime, make rules file in /etc/udev/rules.d/. For example,
$ echo ACTION==\"add\", SUBSYSTEM==\"usb-serial\", DRIVER==\"ftdi_sio\", ATTR{latency_timer}=\"1\" > 99-dynamixelsdk-usb.rules
$ sudo cp ./99-dynamixelsdk-usb.rules /etc/udev/rules.d/
$ sudo udevadm control --reload-rules
$ sudo udevadm trigger --action=add
$ cat /sys/bus/usb-serial/devices/ttyUSB0/latency_timer

or if you have another good idea that can be an alternatives,
please give us advice via github issue https://github.com/ROBOTIS-GIT/DynamixelSDK/issues

