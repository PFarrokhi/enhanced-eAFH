# Steps for embedding the original eAFH into the installed Zephyr RTOS on Linux
For embedding the original eAFH into the installed zephyr RTOS on Linux, just run a terimnal in your installation directory and run the following commands one-by-one.

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
**NOTE:** The CMakeLists.txt contains "set(SHIELD ssd1306_128x32)" which prevents compilation on a hardware like nRF52840 dongle. It can be removed for compilation on the other hardwares.

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
The lines 264 to 280 of eAFH has been inserted to line 203 of Zephyr.

## zephyr\subsys\bluetooth\controller\CMakeLists.txt
The lines 81 to 97 of eAFH has been inserted to line 172 of Zephyr.

## apps\sample_eAFH\src\main.c
Some libraries has been changed. Changed void main(void) to int main(int argc, char** argv), added a return 0 to the end of the main and changed the existing returns to return -1 to reduce warnings.

## zephyr\subsys\bluetooth\controller\ll_sw\ull.c
The following changes of eAFH has been inserted:  
Inserted #include "ull_afh.h" to line 49 of Zephyr RTOS v3.5.  
Inserted ull_afh_init() from line 368 to 373 of eAFH to line 734 of Zephyr RTOS v3.5.  

## zephyr\subsys\bluetooth\controller\ll_sw\afh_impl\eAFH.c
Some libraries has been changed.
