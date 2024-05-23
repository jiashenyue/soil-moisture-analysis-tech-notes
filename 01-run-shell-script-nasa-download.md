# Tech Note 1: How to run `.sh` script in Windows 11

## Overview

-   This document is a step-by-step guide on how to run a Shell Script (`.sh`) file in Windows 11
-   If downloading multiple files from [NASA EarthData](https://earthdata.nasa.gov/), a `.sh` file is normally generated to streamline the downloading process
-   Shell script is not designed to be directly run using terminal tools of Windows
-   Two options to run it in Windows
    -   Running the script using Windows Subsystem of Linux (WSL)
        -   Recommended way as installling Windows Subsystem of Linux makes it easier to run Unix-shell commands easily in the future
    -   Install Git for Windows and run a Shell script using Git
        -   Less recommended

## Install Windows Subsystem of Linux (WSL)

-   Open PowerShell as Administrator and run:

## 

``` powershell
wsl --install
```

-   This command will install WSL and the default Ubuntu distribution.
-   Restart your computer
-   Once restarted, a Ubuntu command line window will show up to ask you to set up user name and password for the Ubuntu system (Windows Subsystem Linux)
-   In this tool, you can execute any linux command without other tools

## How to access a local directory in WSL

-   WSL treats the entire hard disk of your PC as a mounted hard drive
    -   `mnt`
-   Every time you want to get access to a folder, you need to access `mnt` virtual directory first
    -   For example

## 

```         
cd /mnt/c/Users
```

-   If using `ls` command in the Ubuntu command window, it will list all directories and files available under the current folder.

## 

```         
cd /mnt
ls
```

-   The above command shows all drives **mounted** to Ubuntu (WSL)

## Edit the original download Shell script

### Tips for saving

-   After building your search in NASA EarthData and select Direct Download option, you will get a Shell script for downloading

-   This script can be opened by Windows Notepad

-   If changes are made in Windows Notepad, **DO NOT** save the Shell script file as a Text file

    -   By default, Notepad tries to save this file as a **Text Documents**, or `.txt`

![img](https://github.com/jiashenyue/soil-moisture-analysis-tech-notes/blob/main/pics/001-tech-note.png)

```         
- This will create trouble in the future as we do not want to run Shell script file using Notepad
```

-   Make sure you select **All Files** while saving, this will ensure the Shell script is saved as a script

![img](https://github.com/jiashenyue/soil-moisture-analysis-tech-notes/blob/main/pics/002-tech-note.png)

### Changing line endings to Unix style

-   Remember we are trying to run a Shell script in Ubuntu (WSL), which is a Linux/Unix based system
-   Files downloaded from NASA in a Windows system tends to create a Shell script using a line ending character for windows
    -   The \^M character represents a carriage return (CR) character, which is commonly encountered when a file has Windows-style line endings (\r\n) instead of Unix-style line endings (\n).
    -   We need to fix this to the Unix-style so that this Shell script can be run
-   If not fixed, you will get an error like below:

## 

```         
bash: ./test.sh: /bin/bash^M: bad interpreter: No such file or directory. 
```

-   You can use Windows PowerShell to fix it very easily

    -   Launch Windows PowerShell from Start menu, or simply type Windows PowerShell in Search bar at the bottom of your screen (if using Windows 11)
    -   Use the cd command to change to the directory containing your shell script. For example

## 

```         
cd C:\Users\jias10\Documents
```

-   Run the following command to rewrite the file with Unix-style line endings

## 

```         
(Get-Content **your_script.sh** -Raw) -replace "`r`n", "`n" | Set-Content **your_script.sh** -NoNewline
```

-   Make sure you change the script name in two places in the above command

-   Now the script is converted to Unix-style and ready to be run using WSL

## Run the download script in WSL

-   Launch WSL by launching Ubuntu (start menu or search bar)
-   Navigate to the directory where the script is saved
    -   Remember to include `/mnt/` before the actual directory so that the WSL knows local disks are mounted

## 

```         
cd /mnt/c/Users/jias10/Documents/smap_sm_download/script
```

-   Run the Shell script using the command below

## 

```         
chmod +x download_script.sh
```

## 

```         
./download_script.sh
```

-   A prompt will ask for your Username to NASA EarthData
-   Another prompt will ask for your password to NASAS EarthData
-   Once login credentials are entered, it may take **up to 2 minutes** for the download to start
-   Below is a screenshot showing the download process

![img](https://github.com/jiashenyue/soil-moisture-analysis-tech-notes/blob/main/pics/003-tech-note.png)
