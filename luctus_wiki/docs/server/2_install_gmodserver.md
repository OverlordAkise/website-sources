# Install the gmodserver

## Advantages of LinuxGSM

**WARNING: Do NOT install it manually via steamcmd!**

Please use LinuxGSM for installing a gmod server.

If you use the manual way with steamcmd you will have no logs of the server. This means if a problem occurs it is near impossible to find the root cause, because the logs are really bad.

If you use LinuxGSM you have the log/console folder where *ALL* the console output of the gmod server is logged. This way you can find ANY error on your server easily.

Advantages of using LinuxGSM:

 - The best possible logs of your server for easy bug fixing
 - Optimizations and Fixes of the source engine automatically applied
 - Easy to remember and standardized file structure
 - Auto restart feature with the monitor command
 - Auto update feature with the force-update command


## Installing

Follow the guide on [https://linuxgsm.com/servers/gmodserver/](https://linuxgsm.com/servers/gmodserver/).

If you have Debian11 running the commands are as follows:

    #install dependencies
    sudo dpkg --add-architecture i386; sudo apt update; sudo apt install curl wget file tar bzip2 gzip unzip bsdmainutils python3 util-linux ca-certificates binutils bc jq tmux netcat lib32gcc-s1 lib32stdc++6 libtinfo5:i386
    #create user
    adduser darkrp
    #switch to user
    su - darkrp
    #download linuxgsm installer
    wget -O linuxgsm.sh https://linuxgsm.sh && chmod +x linuxgsm.sh && bash linuxgsm.sh gmodserver
    #install gmodserver
    ./gmodserver install

That's it!

Afterwards you will have a running gmod server, with amazing logs and easy to use commands.

For commands to restart, etc. visit the operator guide on this wiki at [/wiki/server/operatorguide](/wiki/server/operatorguide)


## Automatic restarts

The server runs, by default, with no auto-restart functionality.  
This can be added with the following cronjobs, use the command `crontab -e` to edit the cronjobs for the gmodserver-user.

```
# Automatically restart if crashed
*/5 * * * * ~/gmodserver monitor
# Automatically restart everyday at 5am
0 5 * * * ~/gmodserver restart
```

The monitor command verifies if the server is still running or if it crashed. If it crashed then it will restart the server and it checks this every 5 minutes.  
The restart command simply restarts the server everyday at 5:00 am. This restart has no announcement, the server simply shutsdown and starts again.


## DarkRP Setup

First we will install the darkrp gamemode. If you do not need it then skip this section.  

Use the following commands:

    cd ~/serverfiles/garrysmod/gamemodes/
    wget https://github.com/FPtje/DarkRP/archive/refs/heads/master.zip
    unzip master.zip
    mv DarkRP-master darkrp
    rm master.zip

The commands, after reach other, will do the following:

 - go to the gamemodes folder
 - download the newest darkrp release
 - unpack it
 - rename it correctly
 - remove the zip file from the download


After this you need the darkrpmodification addon aswell. Without it you can't configure the darkrp gamemode.  
The install procedure is the same as the one above, so I will only mention the commands here:

    cd ~/serverfiles/garrysmod/addons/
    wget https://github.com/FPtje/darkrpmodification/archive/refs/heads/master.zip
    unzip master.zip
    mv darkrpmodification-master darkrpmodification
    rm master.zip


## LinuxGSM config

The config file for start parameters is at `~/lgsm/config-lgsm/gmodserver/gmodserver.cfg`

The following is a standard linuxgsm config. You have to change the defaultmap, ip, workshop collection ID and gslt token.  

To get the IP of your server you can use the command `ip a`. It is visible in the `eth0` section.

    ip="1.2.3.4"
    port="27015"
    clientport="27005"
    sourcetvport="27020"
    defaultmap="gm_construct"
    maxplayers="50"
    tickrate="33"
    gamemode="darkrp"
    wscollectionid="1234567890"
    gslt="XXXXXXXX"


## Server config

The server config is at `~/serverfiles/garrysmod/cfg/gmodserver.cfg`

In there you can configure

 - server name
 - server password
 - loadingurl, downloadurl
 - commands to be run at start

I highly recommend to set `sbox_playershurtplayers` to `1` or else PvP damage could not work.

