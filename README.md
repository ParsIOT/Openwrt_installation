# Openwrt_installation

It's better to use two computers (first with linux and second with windows that can run winbox and netinstall)

1.
    Disconnect wifi. 

    Clear static ip if you set 
    
    Connect first mikrotik lan to linux pc
    
    Connect second mikrotik lan to windows pc  
    
2.
    Install dnsmasq
    
    Stop dnsmasq service
    
    Run `sudo ./leader.sh <your ethernet interface name, e.g. eth0 or enp4s0f2>`
    
    
    **It first main dnsmasq then run its own one.**
    
3. 
    Connect to mikrotik with winbox 

    System --> Routerboard --> Settings 
                            , "Boot Device" = "try-ethernet-once-then-nand"
                            , "Boot Protocol" = "dhcp"
                            , tick the "Force Backup Booter" checkbox
                            , Click OK
                            
    System --> Reboot --> Yes (It takes some second to reboot)
    
4.
    You must see "vendor class: Mips_boot" in command line out (that you ran loader.sh)
    
    Then 5th LED must be red color
    
    Mikrotik is running .elf file, now you need to setup .bin file in nand memory. So when you reboot it reload the openwrt instead of routeros or other things!
    
    In this step, if there is any problem. Don't worry just unplug mikrotik power and use Netinstall app according to mikrotik document to reinstall the Routeros.
    
    No Disconnect all of mikrotik lan.  
    
    Kill loader.sh
    
    Connect linux pc lan to second, third, fourth or fifth mikrotik(not first one!).
    
    Set static ip addr in linux pc:
    
    ```bash
    sudo ifconfig <ethernet interface> down && sudo ifconfig <ethernet interface> 192.168.1.2 up
    ```
            
    Recheck that your ip is 192.168.1.2  
    
    Now ping 192.168.1.1 to see the openwrt.
    
    Use scp to transfer .bin file :
    ```bash
    scp lede-17.01.4-ar71xx-mikrotik-nand-large-squashfs-sysupgrade.bin root@192.168.1.1:/tmp
    ``` 
    **Transfer .bin file to /tmp folder in router(don't transfer it to home or ~)**
            
    Now ssh to 192.168.1.1:
    
    ```bash
    ssh root@192.168.1.1
    cd /tmp
    sysupgrade -n lede-17.01.4-ar71xx-mikrotik-nand-large-squashfs-sysupgrade.bin
    ```
    
    Now you will see this output :
    
        Image metadata not found
        killall: watchdog: no process killed
        Connection to 192.168.1.1 closed by remote host.
        Connection to 192.168.1.1 closed.
        
    Just wait about 2 or 3 minutes to rebooting(beep sound) and boot again. 
    
    If it boots you can ssh to it(New ssh-key setted, so use this to delete old one.
    ```bash
    ssh-keygen -f "/home/hadi/.ssh/known_hosts" -R "192.168.1.1" 
    ```
   
                            
For reinstalling to Routeros, just shutdown the openwrt, unplug power cable. Now put it into Etherboot(see mikrotik Netinstall doc) mode(For RB951Ui-2hnd: Press reset button the plugin power cable and wait some more time than normal state(Etherboot mode when Routeros installed) about 30 or 45 seconds) 
Now your on Etherboot mode, use Netinstall to reinstall Routeros


useful links :

https://github.com/DavyJ0nes/OpenWRT-Mikrotik

https://forum.openwrt.org/t/installing-on-mikrotik-rb951ui-2hnd/3751/5

https://oldwiki.archive.openwrt.org/toh/mikrotik/common

https://wiki.mikrotik.com/wiki/Manual:Netinstall

https://downloads.openwrt.org/releases/17.01.4/targets/ar71xx/mikrotik/

https://forum.mikrotik.com/viewtopic.php?t=93011

https://forum.mikrotik.com/viewtopic.php?t=93341
