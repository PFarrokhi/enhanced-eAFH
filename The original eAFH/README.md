# Steps to install zephyr RTOS on windows
For building your zephyr RTOS on windows, just run a "Command Prompt" terimnal in your installation directory and run the following commands one-by-one.

# Install west:
py -m pip install -U "west==1.2.0"

# Initialize west directory:
west init -m https://github.com/zephyrproject-rtos/zephyr --mr v3.5.0 zephyr_3.5

# Go into the built directory and update it:
cd zephyr_3.5

west update

# Initialize cmake files:
west zephyr-export

# Build requirements:
py -m pip install -r zephyr\scripts\requirements.txt

# Install the required SDK beside Zephyr RTOS:
cd ..

wget https://github.com/zephyrproject-rtos/sdk-ng/releases/download/v0.16.5/zephyr-sdk-0.16.5_windows-x86_64.7z

7z x zephyr-sdk-0.16.5_windows-x86_64.7z

cd zephyr-sdk-0.16.5

setup.cmd

# Build a sample the application for a board:
cd ..\zephyr_3.6\zephyr

west build --pristine -b nrf52840dk_nrf52840 samples\basic\blinky

# Transfer the built files into the board:
west flash
