>[Windows Subsystem for Linux (WSL)](https://learn.microsoft.com/en-us/windows/wsl/about) is a feature of Windows that allows you to run a Linux environment on your Windows machine, without the need for a separate virtual machine or dual booting.
## Steps :-

>Open PowerShell or Windows Command Prompt in **administrator** mode by right-clicking and selecting "Run as administrator", enter the wsl --install command, then restart your machine.

```Copy
wsl --install
```

>After that restart the system and setup the Ubuntu. By default, the installed Linux distribution will be Ubuntu. This can be changed using the `-d` flag.

- To change the distribution installed, enter: `wsl --install -d <Distribution Name>`. Replace `<Distribution Name>` with the name of the distribution you would like to install.

- To see a list of available Linux distributions available for download through the online store, enter: 
```Copy
wsl --list --online or wsl -l -o
```

- To install additional Linux distributions after the initial install, you may also use the command: 
```Copy
wsl --install -d <Distribution Name>
```

-  To remove additional Linux distributions after the installation by using following command: : 
```Copy
wsl --unregister <Distribution Name>
```

>But here we go to the Microsoft Store and download the Kali Linux. Then setup the environment because above commands will provide us the older version of Kali Linux.

Next we will install "win kex" in Kali Linux. It will provide users to access a graphical interface for Kali Linux tools directly from their Windows machine.

```Copy
sudo apt install kali-win-kex -y
```
 
>Next, we are still not going to have all the utilities that we’re used to, so browse to [metapackage](https://www.kali.org/docs/general-use/metapackages/)

>browse to this site, and select the metapackage that is of interest to you, I suggest kali-linux-default or kali-linux-large;

```Copy
sudo apt install kali-linux-large -y
```

>This will install Pentesting tools, so that you can run nmap, sqlmap, nikto, hydra, hashcat, or any other program in your toolset, as well as create, and edit files, manipulate or organize the filesystem structure (using grep/sed/awk on files, etc.)

>Then we will run the following command to display Kali Linux in VNC in Desktop.

```Copy
kex --win -s 
```

>(F8 is used to exit the VNC)

---



