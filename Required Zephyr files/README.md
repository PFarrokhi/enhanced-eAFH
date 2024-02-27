# Steps to install Zephyr RTOS for an example run
For building your zephyr RTOS on Windows, just run a "Command Prompt" terimnal in your installation directory and run the following commands one-by-one. For Linux, do it on the Linux termianl.

## Install requirements:
On Linux:
```
sudo apt update
sudo apt upgrade -y 
wget https://apt.kitware.com/kitware-archive.sh
sudo bash kitware-archive.sh
sudo apt install -y --no-install-recommends git cmake ninja-build gperf \
  ccache dfu-util device-tree-compiler wget \
  python3-dev python3-pip python3-setuptools python3-tk python3-wheel xz-utils file \
  make gcc gcc-multilib g++-multilib libsdl2-dev libmagic1
```

## Install west:
On Windows:  
```
py -m pip install -U "west==1.2.0"
```
On Linux:  
```
pip3 install --user -U "west==1.2.0"
echo 'export PATH=~/.local/bin:"$PATH"' >> ~/.bashrc
source ~/.bashrc
```

## Initialize west directory:
On Windows:
```
west init -m https://github.com/zephyrproject-rtos/zephyr --mr v3.5.0 zephyr-3.5
```
On Linux:
```
west init -m https://github.com/zephyrproject-rtos/zephyr --mr v2.4.0 zephyr-2.4
```

## Go into the built directory and update it:
On Windows:
```
cd zephyr-3.5
west update
```
On Linux:
```
cd zephyr-2.4
west update
```

## Initialize cmake files:
```
west zephyr-export
```

## Build requirements:
On Windows:
```
py -m pip install -r zephyr\scripts\requirements.txt
```
On Linux:
```
pip3 install -r zephyr/scripts/requirements.txt
```

## Install the required SDK beside Zephyr RTOS:
On Windows:
```
cd ..
wget https://github.com/zephyrproject-rtos/sdk-ng/releases/download/v0.16.5/zephyr-sdk-0.16.5_windows-x86_64.7z
7z x zephyr-sdk-0.16.5_windows-x86_64.7z
cd zephyr-sdk-0.16.5
setup.cmd
```
On Linux:
```
cd ..
wget https://github.com/zephyrproject-rtos/sdk-ng/releases/download/v0.11.4/zephyr-sdk-0.11.4-setup.run
chmod +x zephyr-sdk-0.11.4-setup.run
./zephyr-sdk-0.11.4-setup.run
```

## Build a sample blinky application for a board:
On Windows:
```
cd ..\zephyr-3.5\zephyr
west build --pristine -b nrf52840dk_nrf52840 samples\basic\blinky
```
On Linux:
```
cd zephyr-2.4/zephyr
west build --pristine -b nrf52840dk_nrf52840 samples/basic/blinky
```

## Transfer the built files into the board:
```
west flash
```

**NOTE:** "nrfjprog" is necessary for "west flash" command. Install it using this link:
```
https://www.nordicsemi.com/Products/Development-tools/nrf-command-line-tools/download
```
**NOTE:** The "west flash" command will not necessarily work for every device. Check the "Supported Boards" documentation of "Zephyr Project" for more details. For example, the following commands are needed to run for nRF52840 dongle:
On Windows:
```
nrfutil pkg generate --hw-version 52 --sd-req=0x00 --application build/zephyr/zephyr.hex --application-version 1 program.zip
nrfutil dfu usb-serial -pkg program.zip -p COM10
```
On Linux:
```
nrfutil pkg generate --hw-version 52 --sd-req=0x00 --application build/zephyr/zephyr.hex --application-version 1 program.zip
sudo chmod a+rw /dev/ttyACM0
nrfutil dfu usb-serial -pkg program.zip -p /dev/ttyACM0
```

# Steps toward a sample heartrate application
The eAFH and enhanced eAFH implementation is based on the zephyr/samples/bluetooth/central_hr and zephyr/samples/bluetooth/peripheral_hr. However, they are combined in the eAFH implementation and both are integrated into the apps/sample_eAFH code. Then, each device understands its duty according to its hardware address. Although it works fine with nRF52840DK boards used in eAFH evaluation, there is no printing output for a hardware like nRF52840 dongle which we used in the enhanced eAFH implementation. We describe bellow the way to build the separated files on a nRF52840DK and a nRF52840 dongle on Windows 11. We will use the dongle for the peripheral part, and the development kit for the central part.

## Step 1: Initialize the environment
Copy the zephyr/samples/bluetooth/central_hr and zephyr/samples/bluetooth/peripheral_hr directories to the apps/ folder. Then, open a command prompt and go to the zephyr directory.
## Step 2: Build the peripheral part
If you want to see the output of this part, first build it for the DK and connect to it using "nRF Connect for Desktop Bluetooth Low Energy" and the dongle. If you want to do this, follow the instructions bellow. Otherwise, jump to **Step 2.1**.
### Step 2.1: Build the peripheral part for nRF52840DK
First, connect the dongle to "nRF Connect for Desktop Bluetooth Low Energy" by pressing the RESET button.
Second, build and flash the peripheral_hr for the DK using the commands bellow and connect the DK to a serial monitor.
```
west build --pristine -b nrf52840dk_nrf52840 peripheral_hr
west flash
```
Third, scan for the DK and connect to it.
### Step 2.2: Build the peripheral part for nRF52840 dongle
Build and flash the peripheral_hr for the dongle using the commands bellow.
```
west build --pristine -b nrf52840dongle_nrf52840 peripheral_hr
nrfutil pkg generate --hw-version 52 --sd-req=0x00 --application build/zephyr/zephyr.hex --application-version 1 program.zip
nrfutil dfu usb-serial -pkg program.zip -p COM10
```
## Step 3: Build the central part
build and flash the central_hr for the DK using the commands bellow and connect the DK to a serial monitor.
```
west build --pristine -b nrf52840dk_nrf52840 central_hr
west flash
```
for reviewing the reults you can press the RESET button **ON THE DK** or entering the following command.
```
nrfjprog --reset
```
