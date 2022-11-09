## Configuring the Voron V0 Display for USB with CanBoot

### CanBoot allows flashing Voron V0 display without having to jumper the DFU pins. 

## Setup Steps

### Install CanBoot if you haven't already

- Run the following from your klipper host to copy the canboot github files
```
cd ~/
git clone https://github.com/Arksine/CanBoot
```

- Add Canboot to the moonraker update manager by adding the following lines to your moonraker.cof file.
```
[update_manager CanBoot]
type: git_repo
path: ~/CanBoot
origin: https://github.com/Arksine/CanBoot.git
is_system_service: False
```

### Compile V0 Display Canboot Firmware
- ssh to your pi console
- CD to the klipper directory
```
cd CanBoot
```

- open menuconfig
```
make menuconfig
```
- Set the following options for CANBUS on USB 
   
![Config](/images/V0_display_canboot_usb.png)

- Exit and Save      

- Set the DFU boot jumper on the V0 display and connect to the Pi by USB

- Run lsusb from the command prompt. Make sure you see a device labeled STM32 in DFU mode listed
```
lsusb
```
   -  note the text inside the on the line tagged with DFU mode [xxxx:yyyy]

- Run make clean to clean up the make environment.
```
make clean
```

- Run make 
```
make
```

- Run DFU util to flash the canboot.bin file
```
sudo dfu-util -a 0 -D ~/CanBoot/out/canboot.bin --dfuse-address 0x08000000:force:mass-erase:leave -d 0483:df11
```
- Remove the DFU boot jumper.
- Reboot

### Compile Klipper Firmware

- ssh to your pi console
- CD to the klipper directory
```
cd klipper
```

- open menuconfig
```
make menuconfig
```
- Set the following options for KLipper on USB 
   
![Config](/images/V0_display_klipper_usb.png)

- Exit and Save      

- Run make clean to clean up the make environment.
```
make clean
```

- Run make 
```
make
```
- Get the V0 Display Serial ID
```
ls /dev/serial/by-id
```

- Flash Klipper with the Canboot Utility
```
python3 ~/CanBoot/scripts/flash_can.py -f ~/klipper/out/klipper.bin -d /dev/serial/by-id/{your serial id}
```

- Reboot

### Re-Flashing new Klipper builds with CanBoot.

- Repeat the [Compile Klipper Firmware steps](./README.MD#compile-klipper-firmware). 
 

