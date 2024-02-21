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
west build --pristine -b nrf52840dk_nrf52840 apps/sample_eAFH
```

## Boot the built eAFH files to your board
```
west flash
```

**NOTE:** For more information about the different configurations of eAFH please visit the original website (https://github.com/ds-kiel/eAFH).
