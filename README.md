# Mesh Box
Documentation and Files for building a Mesh Network Node based on a Banana Pi Zero and Reticulum Stack

## Required Hardware
- Banana Pi Zero
<p align="center" width="100%">
  <div class="img-with-text">
    <img width="49%" src="images/bPi_zero/bPi_zero_front.jpg">
    <p>Banana Pi Zero (frontside)</p>
  </div>
    <img width="49%" src="images/bPi_zero/bPi_zero_back.jpg">
</p>

- ESP32 based LoRa T3 module
<p align="center" width="100%">
  <img width="49%" src="images/T3/T3_front.jpg">
  <img width="49%" src="images/T3/T3_back.jpg">
</p>

- Optional: Battery module
<p align="center" width="100%">
  <img width="49%" src="images/battery/battery_board_front.jpg">
  <img width="49%" src="images/battery/battery_board_back.jpg">
</p>

- Cables & Connectors
- WIFI Antenna for Banana Pi Zero

<p align="left" width="100%">
  <img width="49%" src="images/antenna/antenna_cables.jpg">
</p>

## Hardware setup

### Connect to bPi Serial Debug Interface
<p align="center" width="100%">
  <img width="49%" src="images/bPi_zero/bPi_zero_front_with_serial.jpg">
  <img width="49%" src="images/bPi_zero/bPi_zero_back_with_serial.jpg">
</p>
<p align="center" width="100%">
  <img width="32%" src="images/connected/bPi_zero_ftdi.jpg">
  <img width="32%" src="images/connected/bPi_zero_ftdi_usb.jpg">
  <img width="32%" src="images/schema/bPi_zero_schema_ftdi.png">
</p>

### Connect bPi with ESP32

<p align="center" width="100%">
  <img width="32%" src="images/T3/T3_back_with_cables.jpg">
  <img width="32%" src="images/T3/T3_front_with_cables.jpg">
  <img width="32%" src="images/connected/T3_bPi_zero_connected.jpg">
</p>

### Connect with Power Source

<p align="center" width="100%">
  <img width="49%" src="images/battery/battery_with_cables.jpg">
  <img width="49%" src="images/connected/T3_bPi_zero_battery_connected.jpg">
</p>


## Software Setup

- Download Armbian image and checksum<br>
  `wget https://stpete-mirror.armbian.com/archive/bananapim2zero/archive/Armbian_21.08.1_Bananapim2zero_hirsute_edge_5.13.12.img.xz`<br>
  `wget https://stpete-mirror.armbian.com/archive/bananapim2zero/archive/Armbian_21.08.1_Bananapim2zero_hirsute_edge_5.13.12.img.xz.asc`

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
  `mkdir ~/tmp'<br>
  `TMPDIR=~/tmp pip3 install rnodeconf`<br>

### Reticulum

### Access Point


## Casing

### With Battery

### Without Battery
