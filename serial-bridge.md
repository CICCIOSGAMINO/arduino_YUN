Serial - Bridge 
===============
[TOC]

## Intro 


### ssh
First of all connect to the Yun board through the ssh : 

    ssh root@192.168.240.1
    > arduino 
    > BusyBox v1.19.4 (2014-04-10 11:08:41 CEST) built-in shell (ash)
    Enter 'help' for a list of built-in commands.
    
      _______                     ________        __
     |       |.-----.-----.-----.|  |  |  |.----.|  |_
     |   -   ||  _  |  -__|     ||  |  |  ||   _||   _|
     |_______||   __|_____|__|__||________||__|  |____|
              |__| W I R E L E S S   F R E E D O M
     -----------------------------------------------------
    
    
    root@CICCIOYUN:~# 
    
now you are ready to rock ! 

### serial 
To find the serial port used for the communication with the 

    demsg 
    > [    0.630000] Serial: 8250/16550 driver, 16 ports, IRQ sharing enabled
      [    0.640000] ar933x-uart: ttyATH0 at MMIO 0x18020000 (irq = 11) is a AR933X UART
      [    0.640000] console [ttyATH0] enabled, bootconsole disabled
      
  So to communicate with the Arduino chip (AR933X) we use the **ttyATH0** serial port. 
