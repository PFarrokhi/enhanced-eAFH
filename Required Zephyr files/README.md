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

## Build a sample the application for a board:
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
west flash --runner jlink
```

**NOTE:** "nrfjprog" is necessary for "west flash" command. Install it using this link:
```
https://www.nordicsemi.com/Products/Development-tools/nrf-command-line-tools/download
```
**NOTE:** The "west flash" command will not necessarily work for every device. Check the "Supported Boards" documentation of "Zephyr Project" for more details. For example, the following commands are needed to run for nRF52840 dongle:
```
nrfutil pkg generate --hw-version 52 --sd-req=0x00 \
  --application build/zephyr/zephyr.hex \
  --application-version 1 program.zip
sudo chmod a+rw /dev/ttyACM0
nrfutil dfu usb-serial -pkg program.zip -p /dev/ttyACM0
```
