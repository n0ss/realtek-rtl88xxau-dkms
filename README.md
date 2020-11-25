# realtek-rtl88xxau-dkms
Realtek RTL88XXAU Driver (sourced from : https://pkg.kali.org/pkg/realtek-rtl88xxau-dkms)

Tested in Ubuntu 20.04 LTS with AWUS1900 (RTL8814AU). Injection working, full driver support.
I suggest you use the DKMS install type and reboot. 

This repo was made to fill out the space left by the split of these two : https://github.com/aircrack-ng/rtl8812au & https://github.com/aircrack-ng/rtl8814au
None of which provide a RTL8814AU driver with working injection.

## RTL8812AU/21AU and RTL8814AU drivers
Only for use with Linux & Android

[![Monitor mode](https://img.shields.io/badge/monitor%20mode-working-brightgreen.svg)](#)
[![Frame Injection](https://img.shields.io/badge/frame%20injection-working-brightgreen.svg)](#)
[![GitHub version](https://raster.shields.io/badge/version-v5.6.4.2-lightgrey.svg)](#)
[![GitHub license](https://img.shields.io/github/license/aircrack-ng/rtl8812au.svg)](https://github.com/aircrack-ng/rtl8812au/blob/master/LICENSE)
<br>

[![Ubuntu](https://img.shields.io/badge/Ubuntu-supported-blue.svg)](https://www.ubuntu.com)
[![ParrotOS](https://img.shields.io/badge/ParrotOS-supported-blue.svg)](https://www.parrotsec.org/)
[![Kali](https://img.shields.io/badge/Kali-supported-blue.svg)](https://www.kali.org)
[![Arch](https://img.shields.io/badge/Arch-supported-blue.svg)](https://www.archlinux.org)
[![Armbian](https://img.shields.io/badge/Armbian-supported-blue.svg)](https://www.armbian.com)
[![ArchLinux](https://img.shields.io/badge/ArchLinux-supported-blue.svg)](https://img.shields.io/badge/ArchLinux-supported-blue.svg)
[![aircrack-ng](https://img.shields.io/badge/aircrack--ng-supported-blue.svg)](https://github.com/aircrack-ng/aircrack-ng)
[![hcxdumptool](https://img.shields.io/badge/hcxdumptool-supported-blue.svg)](https://github.com/ZerBea/hcxdumptool)
[![wifite2](https://img.shields.io/badge/wifite2-supported-blue.svg)](https://github.com/kimocoder/wifite2)


### DKMS
This driver can be installed using [DKMS]. This is a system which will automatically recompile and install a kernel module when a new kernel gets installed or updated. To make use of DKMS, install the `dkms` package, which on Debian (based) systems is done like this:
```
$ sudo apt-get install dkms
```

### Installation of Driver
In order to install the driver open a terminal in the directory with the source code and execute the following command:
```
$ sudo ./dkms-install.sh
```

### Removal of Driver
In order to remove the driver from your system open a terminal in the directory with the source code and execute the following command:
```
$ sudo ./dkms-remove.sh
```

### Make
For building & installing the driver with 'make' use
```
$ make && make install
```

### Notes
Download
```
$ git clone https://github.com/n0ss/realtek-rtl88xxau-dkms.git
cd rtl*
```
Package / Build dependencies (Debian-based)
```
$ sudo apt update
$ sudo apt install build-essential libelf-dev linux-headers-`uname -r`
```
#### For Raspberry (RPI)

```
$ sudo apt-get install raspberrypi-kernel-headers
```

Then run this step to change platform in Makefile, For RPI 1/2/3/ & 0/Zero:
```
$ sed -i 's/CONFIG_PLATFORM_I386_PC = y/CONFIG_PLATFORM_I386_PC = n/g' Makefile
$ sed -i 's/CONFIG_PLATFORM_ARM_RPI = n/CONFIG_PLATFORM_ARM_RPI = y/g' Makefile
```

But for RPI 3B+ & 4B you will need to run those below which builds the ARM64 arch driver:
```
$ sed -i 's/CONFIG_PLATFORM_I386_PC = y/CONFIG_PLATFORM_I386_PC = n/g' Makefile
$ sed -i 's/CONFIG_PLATFORM_ARM64_RPI = n/CONFIG_PLATFORM_ARM64_RPI = y/g' Makefile
```

In addition, if you receive an error message about `unrecognized command line option ‘-mgeneral-regs-only’` (i.e., Raspbian Buster), you will need to run the following commands:
```
$ sed -i 's/^dkms build/ARCH=arm dkms build/' dkms-install.sh
$ sed -i 's/^MAKE="/MAKE="ARCH=arm\ /' dkms.conf
```

For setting monitor mode
  1. Fix problematic interference in monitor mode.
  ```
  $ airmon-ng check kill
  ```
  You may also uncheck the box "Automatically connect to this network when it is avaiable" in nm-connection-editor. This only works if you have a saved wifi connection.

  2. Set interface down
  ```
  $ sudo ip link set wlan0 down
  ```
  3. Set monitor mode
  ```
  $ sudo iw dev wlan0 set type monitor
  ```
  4. Set interface up
  ```
  $ sudo ip link set wlan0 up
  ```
For setting TX power
```
$ sudo iw wlan0 set txpower fixed 3000
```

### LED control

#### statically by module parameter in /etc/modprobe.d/8812au.conf or wherever, for example:

```sh
options 88XXau rtw_led_ctrl=0
```
value can be 0 or 1

#### or dynamically by writing to /proc/net/rtl8812au/$(your interface name)/led_ctrl, for example:

```sh
$ echo "0" > /proc/net/rtl8812au/$(your interface name)/led_ctrl
```
value can be 0 or 1

#### check current value:

```sh
$ cat /proc/net/rtl8812au/$(your interface name)/led_ctrl
```

### USB Mode Switch

0: doesn't switch, 1: switch from usb2.0 to usb 3.0 2: switch from usb3.0 to usb 2.0
```sh
$ rmmod 88XXau
$ modprobe 88XXau rtw_switch_usb_mode:int (0: no switch 1: switch from usb2 to usb3 2: switch from usb3 to usb2)
```

### NetworkManager

Newer versions of NetworkManager switches to random MAC address. Some users would prefer to use a fixed address.
Simply add these lines below
```
[device]
wifi.scan-rand-mac-address=no
```
at the end of file /etc/NetworkManager/NetworkManager.conf and restart NetworkManager with the command:
```
$ sudo service NetworkManager restart
```

### Credits / Contributors

```
Alfa Networks - https://www.alfa.com.tw/
Realtek.      - https://www.realtek.com
aircrack-ng   - https://www.aircrack-ng.org
ParrotSec     - https://www.parrotsec.org/download/

astsam        - https://github.com/astsam
evilphish     - https://github.com/evilphish
fariouche     - https://github.com/fariouche
CGarces       - https://github.com/CGarces
ZerBea        - https://github.com/ZerBea
lwfinger      - https://github.com/lwfinger
Ulli-Kroll.   - https://github.com/Ulli-Kroll

```
