# steps to install zephyr RTOS on windows

# install west:
py -m pip install -U "west==1.2.0"

# initialize west directory:
west init -m https://github.com/zephyrproject-rtos/zephyr --mr v3.5.0 zephyr_3.5

# go into the built directory and update it:
cd zephyr_3.5
west update

# initialize cmake files:
west zephyr-export

# build requirements:
py -m pip install -r zephyr\scripts\requirements.txt

# install the required SDK beside Zepyr RTOS from:
cd ..
wget https://github.com/zephyrproject-rtos/sdk-ng/releases/download/v0.16.5/zephyr-sdk-0.16.5_windows-x86_64.7z
7z x zephyr-sdk-0.16.5_windows-x86_64.7z
cd zephyr-sdk-0.16.5
setup.cmd

# build the application for a board:
cd ..\zephyr_3.6\zephyr
west build --pristine -b nrf52840dk_nrf52840 samples\basic\blinky

# transfer the built files into the board:
west flash
