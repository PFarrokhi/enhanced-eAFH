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

# Steps for embedding the original eAFH into the installed Zephyr RTOS on Windows
For embedding the original eAFH into the installed zephyr RTOS on Windows, just run a "Command Prompt" terimnal in your installation directory and run the following commands one-by-one.

## Get the changed files of eAFH compare to the original Zephyr files:
```
wget https://raw.githubusercontent.com/PFarrokhi/enhanced-eAFH/main/The%20original%20eAFH/zephyr-changes.7z
7z x zephyr-changes.7z
xcopy zephyr-changes\* zephyr-3.5\ -Y
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
Some libraries has been changed. Changed void main(void) to int main(int argc, char** argv), added a return 0 to the end of the main and changed the existing returns to return -1 to reduce warnings.

## zephyr\subsys\bluetooth\controller\ll_sw\ull.c
This file has been changed a lot in the Zephyr RTOS v3.5. So, the main file is used and the following changes of eAFH has been inserted:  
Inserted #include "ull_afh.h" to line 49 of Zephyr RTOS v3.5.  
Inserted ull_afh_init() from line 368 to 373 of eAFH to line 734 of Zephyr RTOS v3.5.  

## zephyr\subsys\bluetooth\controller\ll_sw\afh_impl\eAFH.c
Some libraries has been changed.
