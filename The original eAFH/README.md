# Steps to embed the original eAFH into the installed zephyr RTOS on windows
For embedding the original eAFH into the installed zephyr RTOS on windows, just run a "Command Prompt" terimnal in your installation directory and run the following commands one-by-one.

## Get the changed files of eAFH compare to the original Zephyr files:
```
wget https://github.com/PFarrokhi/enhanced-eAFH/edit/main/The%20original%20eAFH/zephyr3.5-changes
xcopy zephyr3.5-changes\* zephyr3.5\ -Y
```
