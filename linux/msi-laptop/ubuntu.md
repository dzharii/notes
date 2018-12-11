https://ubuntuforums.org/showthread.php?t=2349782

Re: Ubuntu installation freezes at the start itself [16.04.1]
Ah, so I solved it this way:

1. Disable Fast Boot and Secure Boot (or Secure loader).

2. Plug in the bootable USB with the Linux distro (mine was Ubuntu 16.04)

3. When you see the loader to "Install Ubuntu" etc ... press "e" and edit a line:
Replace "quiet splash" to "nomodeset" and press F10 to boot.

Then after the installation is complete, you will have to reboot. 

4. This time you will now encounter the GRUB. Again, press "e" and edit a line:
In the line that starts with "linux", add "nouveau.modeset=0" at the end of that line.

Your Linux should now boot. 

P.S. Mark the thread as solved if this solves the issue 
5. After this, you need to install the nvidia drivers. Reboot. And then it's done.