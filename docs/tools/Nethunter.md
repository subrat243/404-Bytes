# Nethunter

To install Kali NetHunter using Termux on an unrooted Android device, follow these steps:

1. Install the NetHunter-Store app from store.nethunter.com.
    
2. From the NetHunter Store, install Termux, NetHunter-KeX client, and Hacker’s keyboard. Note that the "install" button may not change to "installed" after installation; this is normal and can be ignored.
    
3. Open the Termux app and run the following commands sequentially:
    
    - `termux-setup-storage` 
        
    - `pkg install wget` 
        
    - `wget -O install-nethunter-termux https://offs.ec/2MceZWr` 
        
    - `chmod +x install-nethunter-termux` 
        
    - `./install-nethunter-termux` 
        
4. During the installation, you may be prompted to choose a device architecture; select the appropriate option (e.g., the first option for the full version). When asked if you want to delete the root file system, choose "N" (No) to keep the downloaded file.
    
5. The installation process will download and extract the Kali NetHunter root filesystem, which can take 30-60 minutes depending on your internet speed and device performance. Ensure your device is connected to a stable Wi-Fi connection and has sufficient battery.
    
6. After installation, set a password for the graphical interface by running `nethunter kex passwd` in Termux.
    
7. Start the graphical interface by running `nethunter kex &` in Termux. This command will display a port number.
    
8. Open the NetHunter-KeX client app, enter the port number displayed in Termux, set the password you created, and tap "Connect" to access the Kali NetHunter graphical user interface.
    

For a better viewing experience, you can enter a custom resolution under "Advanced Settings" in the KeX Client. It is recommended to run `sudo apt update && sudo apt full-upgrade -y` immediately after installation to update Kali.

**Other Commands for Nethunter in Termux :**

|**Command**|**Details**|
|---|---|
|`nethunter`|start NetHunter|
|`nethunter kex passwd`|configure KeX password (only needed before 1st use)|
|`nethunter kex &`|start NetHunter Desktop Experience as user|
|`nethunter kex stop`|stop NetHunter Desktop Experience as user|
|`nethunter [command]`|run in NetHunter environment|
|`nethunter -r`|start NetHunter cli as root|
|`nethunter -r kex passwd`|configure the KeX password for root|
|`nethunter -r kex &`|start NetHunter Desktop Experience as root|
|`nethunter -r kex stop`|stop NetHunter Desktop Experience root sessions|
|`nethunter -r kex kill`|Kill all KeX sessions|
|`nethunter -r [command]`|run [command] in NetHunter environment as root|

**NOTE :** The command nethunter can be abbreviated to nh. Tip: If you run kex in the background (&) without having set a password, bring it back to the foreground first when prompted to enter the password, i.e. via fg [job id] - you can later send it to the background again via Ctrl + z and bg [job id]

To use KeX, start the KeX client, enter your password and click connect. For a better viewing experience, enter a custom resolution under "Advanced Settings" in the KeX Client

**TIPS :**

- Run sudo apt update && sudo apt full-upgrade first thing after installation. If you have plenty of storage space available you might want to run apt install kali-linux-default as well.
- All of the penetration testing tools should work but some might have restrictions, e.g. metasploit works but doesn't have database support. If you discover any tools that don't work, please post it in nethunter forums.
- Some utilities like "top" won't run on unrooted phones.
- Non-root users still have root access in the chroot. That's a proot thing. Just be aware of that.
- Galaxy phone's may prevent non-root users from using sudo. Just use su -c instead.
- Perform regular backups of your rootfs by stopping all nethunter sessions and typing the following in a termux session: tar -cJf kali-arm64.tar.xz kali-arm64 && mv kali-arm64.tar.xz storage/downloads That will put the backup in your Android download folder. Note: on older devices, change "arm64" to "armhf"
- Please join us in kali forums to exchange tips and ideas and be part of a community that strives to make NetHunter even better.