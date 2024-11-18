# The original eAFH

the "eAFH" folder is a mirror of files available in the original eAFH page (https://github.com/ds-kiel/eAFH) for preventing any conflicts of implemented "enhanced eAFH" with the future changes of the original eAFH. For its installation, you can whether refer to the original page or use the following guides and the "eAFH" folder.

# Steps for make the original eAFH working
The installation of eAFH on Windows face problems as it will miss some libraries. However, for its istallation on Linux you will have two options:

1- Install it using the new VS code extention (recommended)

2- Install it using the linux terminal

Installing using linux terminal is decribed in the original eAFH webpage. So, we introduce a more straight forward way to make it usable even on Windows.

## Install eAFH using the VS code extention
In this scenario we used Ubuntu 20 and the latest versions of each program. For building the eAFH do the following steps one-by-one.

### Step 1: Install VS code
Simply download your favourite version from https://code.visualstudio.com/download and install it.

### Step 2: Install the nRF Connect extension
From the extentions tab of VS code, search for "nRF Connect for VS Code Extension Pack" and install it.

### Step 5: Clone eAFH
From "View->Command Palette..." (Ctrl+Shift+P), run the "Git: Clone" command and enter the original eAFH repository address (https://github.com/ds-kiel/eAFH.git). After clonning it, open its src/ folder in the VS code.

### Step 4: Install the toolchains
From the nRF Connect extention, install the latest toolchain using the "Manage toolchains".

### Step 5: Update the workspace
From the nRF Connect extension, run "west update" using the "Manage west workspace".

**NOTE**: During updating the west, updating some packages may fail due to a bad internet cconnection. So, make sure you have updated all of packages or you may face error during the build.

### Step 6: Manage your build configurations
From the nRF Connect extention, open the application (apps/sample_eAFH) from the "existing application" of "applications" and manage your build configurations according to your board.

**NOTE**: The build folder will be under apps/sample_eAFH and not the one on the top.

**NOTE**: You might want to remove "set(SHIELD ssd1306_128x32)" part from the "CMakeLists.txt" of the application for your board.

### Step 7: Install the rest of tools
Some tools are needed which some can be installed using the suggestions of VS code, or can be installed from the Nordic website. Try to build the project and flash the program to your board to find the rest of the required packages like the "nRF Command Line Tools" (https://www.nordicsemi.com/Products/Development-tools/nRF-Command-Line-Tools/Download).
