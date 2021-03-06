# Mesh Box
Documentation and Files for building a Mesh Network Node based on a Banana Pi Zero and Reticulum Stack

This project aims at creating a easy to build reticulum router. Reticulum is a network stack that can be used via TCP/IP, LoRa networks or other forms of rf connections. It is fully encrypted and operates mesh based. For more details please have a look at https://unsigned.io/projects/reticulum/

This makes it possible to use existing internet connections and extend them borderless to own rf based connections. However, other than existing mesh solutions like e.g. meshtastic, it does not run on the LoRa hardware itself but is implemented in python, thus requiring some kind of sbc to run on. 

Thus this project combines a ESP32 Lora microcontroller with an banana pi zero M2 to create a router to enable all computers in a local wifi to use reticulum.
 

## Required Hardware
### Banana Pi Zero
<p>
The banana pi zero is required to run the reticulum stack. The reason for using a banana pi zero was simply its current availability and exact identical form factor to a raspberry pi sero. It has however 3 additional pins for a serial port, which the rPi lacks and that are used in this project.

**Name** Banana Pi M2 Zero<br>
**URL** https://wiki.banana-pi.org/Banana_Pi_BPI-M2_ZERO<br>
**Source** https://www.reichelt.de/banana-pi-m2-zero-1-2-ghz-quad-core-512-mb-ddr3-banana-pi-zero-p218297.html<br>

</p>
<p align="left" width="60%">
  <img width="49%" src="images/bPi_zero/bPi_zero_front.jpg">
  <img width="49%" src="images/bPi_zero/bPi_zero_back.jpg">
</p>

### ESP32 based LoRa module
<p>
The ESP32 LoRa T3 module basically acts as a LoRa network device. It thus requires to have special reticulum compatible firmware flashed.

**Name** LILYGO® TTGO ESP32-Paxcounter LoRa32 V2.1 1.6<br>
**URL** http://www.lilygo.cn/prod_view.aspx?TypeId=50003&Id=1130&FId=t3:50003:3<br>
**Source** https://www.ebay.de/itm/164396959192<br>
**Frequency** 868 Mhz<br>

</p>
<p align="center" width="60%">
  <img width="49%" src="images/T3/T3_front.jpg">
  <img width="49%" src="images/T3/T3_back.jpg">
</p>

### Optional: Battery module
<p>
The battery module initially was planned as ups for use with solar power, however due to it turning off for a second once the power source is disconnected, this is not feasable. It makes the unit however autonomous for either mobile use or temporary backup power in case of a blackout.
As I bought some of the modules, I'm still going to built this version, but eventually I'll switch to powering the unit using external usb power banks.

**Name** diymore Battery Shield V8<br>
**URL** https://www.diymore.cc/products/18650-battery-shield-v8-mobile-power-bank-3v-5v-for-arduino-esp32-esp8266-wifi<br>
**Source** https://www.ebay.de/itm/402137178128<br>

</p>
<p align="center" width="60%">
  <img width="49%" src="images/battery/battery_board_front.jpg">
  <img width="49%" src="images/battery/battery_board_back.jpg">
</p>

### WIFI Antenna and cable for Banana Pi Zero
<p>
A wifi antenna is required for the banana pi zero as without one it simply has no wifi reception at all. In case a raspberry pi zero is used, the antenna is not needed.

**Name** 2.4GHz WIFI antenna SMA Female<br> 
**Source** https://www.ebay.de/itm/183989745716<br>

</p>
<p align="left" width="60%">
  <img width="49%" src="images/antenna/antenna_cables.jpg">
</p>


### SMA - IPEX adapter cable
<p>
To connect the antenna with the banana pi zero board, a small adapter cable is required

**Name** RP-SMA(Male) to IPEX/U.FL Pigtail Cable<br>
**Source** https://www.ebay.de/itm/265495161098<br>

</p>

### Cables
<p>
3 wire cables are used for the serial connections. In the pictures 4 wires. The length is not relevant, however, if you plan to use the provided 3d printed case, these values fit it:

1x 8.2cm (Banana Pi Zero M2 - gpio serial)<br>
1x 6.5cm (Banana Pi Zero M2 - debug serial)<br>
1x 4 cm (ESP32)


Optional:<br>
2 wire cables are used for the optional power supply. Length used with the 3d printed case:

2x 4.5cm (battery board)<br>
1x 7.8cm (Banana Pi Zero)<br>
1x 3.3cm (ESP32)
</p>

### Connectors
<p>
In order to be able to separate the boards, connectors are used on the serial and optional power connections. the following are used:

1x Dupont connector female (6 pin)<br>
1x JST SM connector female (3 pin)<br>
1x JST SM connector male (3 pin) 

Optional:<br>
For power connections, these connectors are used:

2x JST SM connector male (2 pin)
2x JST SM connector female (2 pin)

</p>


## Hardware setup

### Connect to bPi Serial Debug Interface
<p>
In order to interact with the banana pi zero, one can use the HDMI interface, but for most single board computers, it is much easier to use the serial interface. As most arm boards lack a video bios, this is also often necessary to actually see the bootloader or kernel messages.

The banana pi zero has three additional pins compared to the raspberry pi zero, and provides a serial port via them. All that is needed, is a serial<->usb adapter and a second computer to display the serial messages from the pi.

To use it, first solder three wires to the three pins and attach a connector. Keep in mind that with serial connections, the TX and RX lines are crossed:

Pi  <->	Serial Adapter<br>

RX  <-> TX<br>
TX  <-> RX<br>
GND <-> GND<br>

</p>
<p align="center" width="60%">
  <img width="99%" src="images/schema/bPi_zero_schema_ftdi.png">
</p>
<p>
Cables soldered to the Banana Pi Zero:
</p> 
<p align="center" width="60%">
  <img width="49%" src="images/bPi_zero/bPi_zero_front_with_serial.jpg">
  <img width="49%" src="images/bPi_zero/bPi_zero_back_with_serial.jpg">
</p>

<p>
Banana Pi Zero connected to Serial<->USB adapter:
</p>
<p align="center" width="60%">
  <img width="49%" src="images/connected/bPi_zero_ftdi.jpg">
  <img width="49%" src="images/connected/bPi_zero_ftdi_usb.jpg">
</p>


### Connect bPi with ESP32
<p>
To not have to deal with USB otg cables and for saving space, the serial connection between the Banana Pi Zero M2 and the ESP32 device is realized via GPIO pins. The required pins on the ESP32 are marked as GND,TX and Rx, while the pins on the Banana Pi Zero M2 are Pins 6,8 and 10. Note, again RX and TX lines are crossed:

Raspberry Pi  <->   ESP32

Pin6  = GND   <->   GND<br>
Pin8  = TX    <->   RX<br>
Pin10 = RX    <->   TX<br>

For details on the pinout of the Banana/Raspbian Pi Zero: https://pinout.xyz/

</p>
Please note, here the boards already have also power cables connected, these are optional:
<p>
</p>
<p align="center" width="100%">
  <img width="32%" src="images/T3/T3_back_with_cables.jpg">
  <img width="32%" src="images/T3/T3_front_with_cables.jpg">
  <img width="32%" src="images/connected/T3_bPi_zero_connected.jpg">
</p>

### Connect with Power Source
<p>
This step is optional. Solder cables to the 5V pins of the battery board and attach female connectors to them. Note that it si not possible to power a Raspberry/Banana Pi zero using the 3.3V pins.Now also solder cables in the 5V and GND pins of the Banana Pi Zero M2 and the ESP32 board and connect them with the battery board:
</p>
<p align="center" width="100%">
  <img width="49%" src="images/battery/battery_with_cables.jpg">
  <img width="49%" src="images/connected/T3_bPi_zero_battery_connected.jpg">
</p>


## Software Setup

- Download Armbian image and checksum<br>
  Either get the "official" image:<br>
  `wget https://stpete-mirror.armbian.com/archive/bananapim2zero/archive/Armbian_21.08.1_Bananapim2zero_hirsute_edge_5.13.12.img.xz`<br>
  `wget https://stpete-mirror.armbian.com/archive/bananapim2zero/archive/Armbian_21.08.1_Bananapim2zero_hirsute_edge_5.13.12.img.xz.asc`
  
  Or use a custom build image already including the required reticulum stack and sample application `nomadnet`<br>
  Please note, this image currently is still WIP and will be replaced by improved versions over time:<br>
  https://drive.google.com/file/d/1QC5H9WiDliAjp1J49x-_k1hdVQcxskhf/view

- Verify checksum<br>
  `sha256sum -c Armbian_21.08.1_Bananapim2zero_hirsute_edge_5.13.12.img.xz.sha`
Output should be:<br>
  `Armbian_21.08.1_Bananapim2zero_hirsute_edge_5.13.12.img.xz: OK`

- Extract archive<br>
  `xz -d Armbian_21.08.1_Bananapim2zero_hirsute_edge_5.13.12.img.xz`

- Transfer image to microSD card
  - insert micro SDcard
  - check `dmesg` output for new device
    ```
    [454114.809037] ums-realtek 1-3:1.0: USB Mass Storage device detected
    [454114.821987] scsi host6: usb-storage 1-3:1.0
    [454114.822121] usbcore: registered new interface driver ums-realtek
    [454115.853806] scsi 6:0:0:0: Direct-Access     Generic- SD/MMC/MS PRO    1.00 PQ: 0 ANSI: 4
    [454115.854150] sd 6:0:0:0: Attached scsi generic sg1 type 0
    [454115.856826] sd 6:0:0:0: [sda] 62333952 512-byte logical blocks: (31.9 GB/29.7 GiB)
    ```
   - write image to microSD card<br>
     `sudo dd if=Armbian_21.08.1_Bananapim2zero_hirsute_edge_5.13.12.img of=/dev/sda bs=4096k status=progress`

### Image Setup

- Insert microSD card into the bPi
- Connect the bPi serial debug interface with the serial<->usb adapter to yout computer
- Open a connection
  - check `dmesg` output for new device
    ```
    [456060.557945] usbserial: USB Serial support registered for FTDI USB Serial Device
    [456060.557967] ftdi_sio 1-3:1.0: FTDI USB Serial Device converter detected
    [456060.557997] usb 1-3: Detected FT232RL
    [456060.565508] usb 1-3: FTDI USB Serial Device converter now attached to ttyUSB0
    ```
  - connect to device<br>
    `screen /dev/ttyUSB0 115200`<br>
    Your screen terminal will stay blank. This is normal, leave it running<br>
  - bootup bPi by pluging in microUSB power source
  - wait for the bootup messages finish
- Once the initial login program starts, enter password and create user as guided

<p align="center" width="100%">
  <img width="49%" src="images/screenshots/bPi_zero_bootup_small.gif">
</p>

### Configure Wifi

- Connect the wifi antenna with the bPi
- run `nmtui` and use its UI to choose you network and provide credentials

<p align="center" width="100%">
  <img width="49%" src="images/screenshots/nmtui_startup.png">
  <img width="49%" src="images/screenshots/nmtui_select.png">
</p>

- Show ip adress<br>
  `ip a l wlan0 | awk '/inet/ {print $2}'`

### System Setup

- Login to the bPi using ssh
- Update system<br>
  `apt-get update && apt-get upgrade`
- Run `armbian-config` and configure<>br
  - System->CPU<br>
	Minimum Speed: 480000<br>
    Maximum Speed: 480000<br>
    Governor: powersave<br>
  - System->Hardware<br>
	Enable: uart3<br>
    

### Required Software

- Install required packages<br>
  `apt install python3-pip python3-all-dev libffi-dev rustc cargo`

### Python Modules

- Install required python modules<br>
  You will have to change the temporal build dir as /tmp on the bPi is too small and running just `pip3 install` will fail with an disk space error.<br>
  ```
  mkdir ~/tmp
  TMPDIR=~/tmp pip3 install rnodeconf
  ```

### Reticulum

### Access Point


## Casing
### TTGO ESP32 LoRa32 only
In case you don't want to built a stand alone version including an SBC, you can use this case for a TTGO ESP32 LoRa32 module and simply connect it to you Laptop. You then only have to flash the Rnode firmware, connect the module to your computer to use it like a LoRa/reticulum network card. The reticulum stack/nomadnet then needs to be installed on the connected computer(https://github.com/markqvist/nomadnet)
https://www.thingiverse.com/thing:5330732

### With Battery

### Without Battery
