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
Build and flash the central_hr for the DK using the commands bellow and connect the DK to a serial monitor.
```
west build --pristine -b nrf52840dk_nrf52840 central_hr
west flash
```
For reviewing the reults you can press the RESET button **ON THE DK** or entering the following command.
```
nrfjprog --reset
```
The following image is a sample output after reset.
![image](https://github.com/PFarrokhi/enhanced-eAFH/assets/50174354/c5f7ef10-c3f4-4466-9469-b73d22fbe0f3)


## Get eAFH and copy the changed files into the original Zephyr RTOS
```
git clone https://github.com/ds-kiel/eAFH.git
cp -r eAFH/src/app zephyr-2.4/
cp -r eAFH/src/zephyr/* zephyr-2.4/zephyr/
```

## Build the program
```
cd zephyr-2.4/zephyr/
west build --pristine -b nrf52840dk_nrf52840 ../apps/sample_eAFH
```
**NOTE:** The CMakeLists.txt contains "set(SHIELD ssd1306_128x32)" which prevents compilation on a hardware like nRF52840 dongle. It can be removed for compilation on the other hardwares. For further information for Linux installation visit the eAFH page (https://github.com/ds-kiel/eAFH).

# Steps for embedding the original eAFH into the installed Zephyr RTOS on Windows
For embedding the original eAFH into the installed zephyr RTOS on Windows, the v2.4 which is used by eAFH is not compatible with Windows. So, we will use the v3.5 which is the latest stable version at this time. According to the huge changes of v3.5 compared to v2.4, we will use the original Zephyr project and change or add some files according to the README.md of eAFH project.

## Get the changed files of eAFH compare to the original Zephyr files:
```
wget https://raw.githubusercontent.com/PFarrokhi/enhanced-eAFH/main/The%20original%20eAFH/zephyr-changes.7z
7z x zephyr-changes.7z
xcopy zephyr-changes\* zephyr-3.5\ -Y
```

**NOTE:** For more information about the different configurations of eAFH please visit the original website (https://github.com/ds-kiel/eAFH).

# Changed files
The basic Zephyr RTOS v3.5 is used and changed some files according to the eAFH project. Note that the round robin channel selection is not implemented from eAFH.

## zephyr\subsys\bluetooth\Kconfig
The lines 256 to 280 of eAFH has been inserted to line 203 of Zephyr.

## zephyr\subsys\bluetooth\controller\CMakeLists.txt
The lines 81 to 97 of eAFH has been inserted to line 172 of Zephyr.

## zephyr\subsys\bluetooth\controller\ll_sw\ull.c
The line 42 of eAFH has been inserted to line 50 of Zephyr.  
The lines 368 to 373 of eAFH has been inserted to line 732 of Zephyr.

## zephyr\subsys\bluetooth\controller\ll_sw\nordic\lll\lll_conn.c
The line 28 of eAFH has been inserted to line 37 of Zephyr.  
The lines 165 to 172 of eAFH has been inserted to line 164 of Zephyr.  
The lines 341 to 355 of eAFH has been inserted to line 463 of Zephyr.  
The lines 364 to 371 of eAFH has been inserted to line 470 of Zephyr.  

## zephyr\subsys\bluetooth\controller\ll_sw\lll_conn.c
The lines 70 to 83 of eAFH has been inserted to line 155 of Zephyr.  


# Added files
The following added files by the eAFH are not compatible with Zephyr RTOS v3.5. 
- `zephyr/subsys/bluetooth/controller/ll_sw/ull_afh.h`
- `zephyr/subsys/bluetooth/controller/ll_sw/ull_afh.c`
- `zephyr/subsys/bluetooth/controller/ll_sw/afh_impl/PDR_exclusion.h`
- `zephyr/subsys/bluetooth/controller/ll_sw/afh_impl/PDR_exclusion.c`
- `zephyr/subsys/bluetooth/controller/ll_sw/afh_impl/eAFH.h`
- `zephyr/subsys/bluetooth/controller/ll_sw/afh_impl/eAFH.c`
- `zephyr/subsys/bluetooth/controller/ll_sw/afh_impl/none.c`
