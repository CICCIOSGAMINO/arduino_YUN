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
    
So we can notice the SWAP memory is zero ! So we need to add some SWAP ... (/dev/zero is a special file in Unix-like operating systems that provides as many null characters (ASCII NUL, 0x00), uses is to provide a character stream for initializing data storage) here you can find a well done [document][2] to follow  : 

Will create a 512 MB swap file named yunswapfile in folder "/swap"and fill it with zero
    
    $ cd /
    $ mkdir swap 
    
    $ dd if=/dev/zero of=/swap/yunswapfile bs=1M count=512
    > 512+0 records in
      512+0 records out
      
The step above just created an empty file. To make sure it can be used as a swap file, run this from the shell : 

    mkswap /swap/yunswapfile 
    > Setting up swapspace version 1, size = 524284 KiB
      no label, UUID=04f5d774-36d4-4b75-bc9c-f40190c03900
      
To verify that the swap file is good, try to load it by running this:

    swapon /swap/yunswapfile
 
This will not provide any output if everything is cool. So verify by checking free memory.

    free -mh 
    > total         used         free       shared      buffers
      Mem:         61116        59376         1740            0        12436
      -/+ buffers:              46940        14176
      Swap:       524284            0       524284
      
So now we have built the SWAP memory to use, there is a last step to do in order to start to use the SWAP ! If you stop with Step previus next time when you restart your Yun (linux part..either through power off/on or the Linux reset button near the LEDs) the swap file will not have been loaded. So to make sure that its gets loaded every time, you need to set the swap file as part of boot sequence.
 
**So now it's time for the fstab configuration**, The Fstab, or file systems table, is a central configuration that defines how file systems (usually on block devices) should be mounted if requested (such as on booting the device or connecting it physically). This way, you don't have to manually mount your devices when you want to access them. The mounting configuration can consist of static file systems but also swap partitions. The fstab UCI configuration file is where all the options for all devices and file systems to be mounted are defined and is located at…

    /etc/config/fstab
    > config global automount
    	option from_fstab 1
    	option anon_mount 1
    	
    config global autoswap
    	option from_fstab 1
    	option anon_swap 0

Here the commands to execute to fix the new swap memory in the fstab table partition : 

    //add swap config entry to fstab
    $ uci add fstab swap
    
    // set device config entry to swap. make sure you provide your full swap file name
    $ uci set fstab.@swap[0].device=/swap/yunswapfile
    
    // set swap is enabled
    $ uci set fstab.@swap[0].enabled=1
    
    // set file system type as "swap"
    $ uci set fstab.@swap[0].fstype=swap

    // set options to default
    $ uci set fstab.@swap[0].options=default

    // set fsck to 0
    $ uci set fstab.@swap[0].enabled_fsck=0
 
    // Commit the config changes. if you don't run commit, the config changes will not be added
    $ uci commit

That's it. Done. Restart the Linux part of Yun (reset button near LEDs). After reboot, if you run "free -m" you should see the Swap file loaded. You have successfully expanded the RAM on your Arduino Yun's linux side.

    free -mh 
    > ... 
      Swap:       524284            0       524284
      
### Update npm 
First thing to do after the swap creaation is update npm : 

    npm install -g npm 
    
with npm updated you can try to install the packages you want ! 


### Johnny5
Maybe you can run all this documents only to install Johnny-Five the Robot framework on the Arduino Yun ? Uhh good choice, so launch the last line of code : 

    mkdir test-j5
    npm init 
    npm install --save johnny-five 
    
On the Arduino Yun Board load the sketch you can find in File > Example > Firmata > StandardFirmataYun, write a little bit of code to check if it's works : 

    // Node.js - Simple Johnny Five test
    var j5 = require('johnny-five');
    var board = new j5.Board({
        port:'/dev/ttyACM0'
    });
    
    board.on('ready', function(){
        var led = new j5.Led(7);
    
        // Blink the LED
        led.blink(500);
    });
 
 
 Well done, in my case all the job is done and the led number 7 blink like a boss every 500 ms ! i hope your too ... 


### Disabling the Bridge Script
We need to disable the YUN's bridge so we can take control of the serialport interface.To remove the bridge setup from the YUN's inittab script and Edit the **/etc/inittab** file to comment out the ttyATH0 line (put a # before it) When you're done, the file should look like this:

    ::sysinit:/etc/init.d/rcS S boot
    ::shutdown:/etc/init.d/rcS K shutdown
    #ttyATH0::askfirst:/bin/ash --login
    
After you're done, restart the YUN with a long press of the YUN RST button.
    




[1]:https://www.arduino.cc/en/Tutorial/ExpandingYunDiskSpace
[2]:http://www.element14.com/community/groups/arduino/blog/2014/12/19/part-x3-arduino-yun-extending-the-ram
    

