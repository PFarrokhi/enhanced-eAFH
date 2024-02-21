# Steps for embedding the original eAFH into the installed Zephyr RTOS on windows
For embedding the original eAFH into the installed zephyr RTOS on windows, just run a "Command Prompt" terimnal in your installation directory and run the following commands one-by-one.

## Get the changed files of eAFH compare to the original Zephyr files:
```
wget https://raw.githubusercontent.com/PFarrokhi/enhanced-eAFH/main/The%20original%20eAFH/zephyr-3.5-changes.7z
7z x zephyr-3.5-changes.7z
xcopy zephyr-3.5-changes\* zephyr-3.5\ -Y
```

## Build the eAFH for a board
```
cd zephyr-3.5\zephyr
west build --pristine -b nrf52840dk_nrf52840 ../apps/sample_eAFH
```

## Boot the built eAFH files to your board
```
west flash
```

**NOTE:** For more information about the different configurations of eAFH please visit the original website (https://github.com/ds-kiel/eAFH).

# Changed files
Some files are changed to make eAFH compatible with Zephyr RTOS v3.5

## zephyr\subsys\bluetooth\Kconfig
This file has been changed a lot in the Zephyr RTOS v3.5. So, the main file is used and the changes of eAFH has been inserted to lines 219 to 241.

## zephyr\subsys\bluetooth\controller\CMakeLists.txt
This file has been changed a lot in the Zephyr RTOS v3.5. So, the main file is used and the changes of eAFH has been inserted to lines 148 to 164.

## apps\sample_eAFH\src\main.c
<zephyr.h> is depricated. So, it is changed to <zephyr/kernel.h>
