Arduino YUN
===========
[TOC]

## Intro 
Simple example and stuff for manage the Arduino Yun 

### Getting Start 
To start with the Arduino Yun, connect to 5V Power through a USB, once the board is powered we have 
two choice to connect to it : 

+ Wait some seconds and connect to the Yun-xxxxx Network 
+ Connect a Eternet Cable with a Router with DHCP active 

After that we can find the Yun at the address 192.168.240.1 or in our network, so now you are ready to 
connect to the board via http : 

    http://192.168.240.1
    >> (default password) : arduino 
    
### Time 
To set the right time, without it could be raise many error in commons OS operations such as installing process : 

    $ ntpd -q -p ptbtime1.ptb.de
    
    
### Linino 
Now you can meet the Linino system (based on WRT) installed on the Arduino Yun board, to get more infor about it following the official documentation. To install package on the Linino environment use the dpkg, remember to update the packages list every time, because there are no persistent memory on Yun for save all the packages list! The list will be saved in the RAM once and when the board is shutdown the list won't be on the board next start ! 

    $ opkg update 
    $ opkg install vim      // for example 
    
Remember to set the current time and location configuration on the board, you can do that from Luci environment in System 
tab click > Sync with Browser ! 

### Expand Memory 
One common operation to do on the Arduino Yun board, is expand the OpenWRT filesystem and the memory on the microSD card, 
for do that follow the official arduino [link][1], with more memory for the OS file system we can think to install programs such as Node.js, JoHnny5, Autobhan and many more ... 

### Node.js 
For install Node.js if you do not need the last version use the OpenWRT package, it can be trivial to install the latest version of Node.js on the Arduino Yun (you need to change the g++, gpp compilers chain), so here we're going to install the OpenWRT version 0.10.33-1 : 

    $ opkg update 
    $ opkg install node 
    
    $ node --version 
    > v0.10.33
    $ npm --version
    > 1.4.28
    
### Node.js Modules - serialport
If you are a Node.js fan, you probably want to use your favourite modules. Unfortunately, not every Node.js module is “pure javascript”, so some modules won’t work on the Yún. We are solving this by preparing packages for the most common native Node.js modules. We have already prepared node-serialport :

    $ opkg update
    $ opkg install node-serialport
    
### Swap Memory 
Ok now Node.js is installed but when i tried to install some more node packages using node package manager (npm) i ran in to "out of memory" issue. Arduino Yun have only 16MB Flash Memory (10 used by OpenWRT), RAM 64MB but only 18/20 MB of this will be free for you to run other process. 

So when you run out of this 18+ MB free memory in RAM, we will run in to fatal error.To avoid this you should set up a swap file or a swap partition for the Linux side of the Yun. When you expand disk space as described here, the YunDiskSpaceExpander sketch doesn’t set up a swap partition by default.  So we have to create a swap file that can be used by openwrt-yun to avoid running out of RAM memory. Run command free to check the amount of free and used memory : 

     total         used         free       shared      buffers
    Mem:         61116        55356         5760            0        29884
    -/+ buffers:              25472        35644
    Swap:            0            0            0
    
So we can notice the SWAP memory is zero ! So we need to add some SWAP ... 

 


### Disabling the Bridge Script
We need to disable the YUN's bridge so we can take control of the serialport interface.To remove the bridge setup from the YUN's inittab script and Edit the **/etc/inittab** file to comment out the ttyATH0 line (put a # before it) When you're done, the file should look like this:

    ::sysinit:/etc/init.d/rcS S boot
    ::shutdown:/etc/init.d/rcS K shutdown
    #ttyATH0::askfirst:/bin/ash --login
    
After you're done, restart the YUN with a long press of the YUN RST button.
    




[1]:https://www.arduino.cc/en/Tutorial/ExpandingYunDiskSpace
    

