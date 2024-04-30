# Steps for embedding the original eAFH into the installed Zephyr RTOS on Linux
The installation of eAFH on Windows face problems as it will miss some libraries. However, for its istallation on Linux you will have two options:

1- Install it using the new VS code extention (recommended)

2- Install it using the linux terminal

## Install eAFH using the VS code extention
In this scenario we used Ubuntu 20 and the latest versions of each program. For building the eAFH do the following steps one-by-one.

### Step 1: Install VS code
Simply download your favourite version from https://code.visualstudio.com/download and install it.

### Step 2: Install the nRF Connect extension
From the extentions tab of VS code, search for "nRF Connect for VS Code Extension Pack" and install it.

### Step 3: Install the toolchains
From the nRF Connect extention, install the latest toolchain using the "Manage toolchains".

### Step 4: Clone eAFH
From "View->Command Palette..." (Ctrl+Shift+P), run the "Git: Clone" command and enter the original eAFH repository address (https://github.com/ds-kiel/eAFH.git). After clonning it, open its src/ folder in the VS code.

### Step 5: Update the workspace
From the nRF Connect extension, run "west update" using the "Manage west workspace".

### Step 6: Manage your build configurations
From the nRF Connect extention, manage your build configurations according to your board.

### Step 7: Install the rest of tools
Some tools are needed which some can be installed using the suggestions of VS code, or can be installed from the Nordic website. Try to build the project and flash the program to your board to find the rest of the required packages like the "nRF Command Line Tools" (https://www.nordicsemi.com/Products/Development-tools/nRF-Command-Line-Tools/Download).

## Install eAFH using the linux terminal
### Step 1: Install requirements
```
sudo apt update
sudo apt upgrade
wget https://apt.kitware.com/kitware-archive.sh
sudo bash kitware-archive.sh
sudo apt install -y --no-install-recommends git cmake ninja-build gperf \
  ccache dfu-util device-tree-compiler wget \
  python3-dev python3-pip python3-setuptools python3-tk python3-wheel xz-utils file \
  make gcc gcc-multilib g++-multilib libsdl2-dev libmagic1
```

### Step 2: Install west:
```
pip3 install --user -U "west==1.2.0"
echo 'export PATH=~/.local/bin:"$PATH"' >> ~/.bashrc
source ~/.bashrc
```

### Step 3: Initialize west directory:
```
west init -m https://github.com/zephyrproject-rtos/zephyr --mr v2.4.0 zephyr-2.4
```

### Step 4: Go into the built directory and update it:
```
cd zephyr-2.4
west update
```

### Step 5: Initialize cmake files:
```
west zephyr-export
```

### Step 6: Build requirements:
```
pip3 install -r zephyr/scripts/requirements.txt
```

### Step 7: Install the required SDK beside Zephyr RTOS:
```
cd ..
wget https://github.com/zephyrproject-rtos/sdk-ng/releases/download/v0.11.4/zephyr-sdk-0.11.4-setup.run
chmod +x zephyr-sdk-0.11.4-setup.run
./zephyr-sdk-0.11.4-setup.run
```

### Step 8: Build the application for a board:
```
west build --pristine -b nrf52840dk_nrf52840 samples/basic/blinky
```

### Step 9: Transfer the built files into the board:
```
west flash
```

**NOTE:** "nrfjprog" is necessary for "west flash" command. Install it using this link:
```
https://www.nordicsemi.com/Products/Development-tools/nrf-command-line-tools/download
```

**NOTE:** The "west flash" command will not necessarily work for every device. Check the "Supported Boards" documentation of "Zephyr Project" for more details. For example, the following commands are needed to run for nRF52840 dongle:
```
nrfutil pkg generate --hw-version 52 --sd-req=0x00 --application build/zephyr/zephyr.hex --application-version 1 program.zip
sudo chmod a+rw /dev/ttyACM0
nrfutil dfu usb-serial -pkg program.zip -p /dev/ttyACM0
```
