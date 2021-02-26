# [Kali-xRDP • xRDP Installer for Kali Linux on WSL1/WSL2 (Version 1.0 / 20210225)](https://github.com/DesktopECHO/Kali-xRDP)

Kali-xRDP is a fully-automated script to install xRDP on Kali Linux from the Windows Store.  It works under WSL1 or WSL2, is able to switch between either without issue, and includes optimizations and visual tweaks to provide a smooth and responsive desktop experience.  

Other Kali GUI-installers are designed to work with WSL2 only.  Kali-xRDP works with Windows 10 version 1809 and newer, Windows Server 2019, Server Core, or Hyper-V Server 2019.  This is useful when you're on older hardware without VT-d, or in a virtual machine without nested virtualization support.  Note that WSL1 has limitations in its networking stack that prevent some of Kali's included tools from working as they should.  

The install script is meant to be run on a new Kali installation from the Microsoft Store.  If Kali is not detected on your system it will download the .AppX image from Microsoft and install it for you. 

**INSTRUCTIONS:  Open a NEW elevated command prompt window (admin rights are required to open firewall ports for RDP and SSH) then type/paste the following command:**

    PowerShell -executionpolicy bypass -command "wget https://github.com/DesktopECHO/xWSL/raw/KaliWSL/xWSL-Kali.cmd -UseBasicParsing -OutFile xWSL-Kali.cmd ; .\xWSL-Kali.cmd"

You will be asked a few questions.  The installer script determines the current Windows DPI scaling of your system but you may set your own if preferred:

     [Kali xRDP Installer 20210223]

     Hit Enter to use your current display scaling in Windows
     or set the desired value (1.0 to 3.0 in .25 increments) [1.5]: 1.25
     Port number for xRDP traffic or hit Enter for default [3399]:
     Port number for SSHd traffic or hit Enter for default [3322]:
     [Not recommended!] Type X to eXclude from Windows Defender:

The script will download the [LxRunOffline](https://github.com/DDoSolitary/LxRunOffline) distro manager to bootstrap the installation.  Install times will vary depending on system performance and presence of antivirus software.  A fast system with broadband kaliInternet can complete the install in under 10 minutes and most machines will complete within 20 minutes. 

     [22:18:12] Prepare Distro (~1m00s)
     [22:19:08] Install xRDP and Kali-Linux-Core packages (~3m00s)
     [22:22:53] Kali-Desktop-XFCE (~5m00s)
     [22:30:39] Additional Components (~1m00s)
   
At the end of the script you will be prompted to create a non-root user which will automatically be added to sudo'ers.

     Create a NEW user in Kali for xRDP GUI login. Enter username: kaliuser
     Enter password for kaliuser: **********

Open Windows Firewall Ports for xRDP, SSH, mDNS...
Building RDP Connection file, Init system...
Building Scheduled Task...
SUCCESS: The scheduled task "kali-linux" has successfully been created.

           Start: Thu 02/25/2021 @ 22:17
             End: Thu 02/25/2021 @ 22:31
        Packages: 1154

       - xRDP Server listening on port 3399 and SSHd on port 3322.

       - Link for GUI session has been placed on your desktop.

       - (Re)launch init from the Task Scheduler or by running the following command:
         schtasks.exe /run /tn kali-linux

      Installaion of xRDP GUI on "kali-linux" complete, graphical login will start in a few seconds...

A fullscreen remote desktop session will launch Kali using your stored credentials.   

**Configure Kali-xRDP to start at boot (like a service, no console window)**

* Open the Windows Task Scheduler 
* Right-click the task in Task Scheduler (kali-linux) and click properties
* Click the checkbox for **Run whether user is logged on or not** and click **OK**
* Enter your Windows credentials when prompted
 
Reboot your PC when complete and the xRDP service in Kali will startup automatically with your system.

**Start/Stop Operation**

* Reboot the instance (example with default distro name of 'xWSL'): ````schtasks /run /tn kali-linux```` 
* Terminate the instance: ````wslconfig /t kali-linux````

**xWSL leverages Multicast DNS to lookup WSL2 instances**

If your computer has virtualization support you can convert it to WSL2.  xWSL is faster on WSL1, but WSL2 has additional capabilities. 

Example of conversion to WSL2 on machine name "LAPTOP":
 - Stop WSL on LAPTOP:
    ````wsl --shutdown````
 - Convert the instance to WSL2:
    ````wsl --set-version xWSL 2````
 - Restart kWSL Instance:
    ````schtasks /run /tn kali-linux````
 - Edit the RDP file on your desktop to point at the WSL2 instance by adding ````-kali-linux.local```` to the hostname:
    ````LAPTOP-kali-linux.local:3399````

**Make it your own:**

From a security standpoint, it would be best to fork this project so you (and only you) control the packages and files in the repository.

- Sign into GitHub and fork this project
- Edit ```xWSL.cmd```.  On line 2 you will see ```SET GITORG=DesktopECHO``` - Change ```DesktopECHO``` to the name of your own repository.
- Customize the script any way you like.
- Launch the script using your repository name:
 ```PowerShell -executionpolicy bypass -command "wget https://github.com/YOUR-REPO-NAME/xWSL/raw/master/xWSL.cmd -UseBasicParsing -OutFile xWSL.cmd ; .\xWSL.cmd"```

**Quirks / Limitations / Additional Info:**

* When you log out out of a desktop session the entire xWSL instance is restarted, the equivilent of a clean-boot at every login.  Disconnected sessions will wait for your return.  
* WSL1 Doesn't work with PolicyKit. Enabled gksu for apps needing elevated rights (Synaptic, root console)
* [Apt-fast](https://github.com/ilikenwf/apt-fast) added to improve download speed and reliability.
* Mozilla Seamonkey included as a stable browser that's kept up to date via apt.  Current versions of Chrome / Firefox do not work in WSL1.
* Installed image consumes approximately 10GB of storage
* XFCE uses Windows fonts (Segoe UI / Cascadia Code)