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
    
    


    




[1]:https://www.arduino.cc/en/Tutorial/ExpandingYunDiskSpace
    

