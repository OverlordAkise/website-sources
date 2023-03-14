# Don't use FastDL

FastDL used to be the main method of making players download custom content.  
Nowadays the workshop download is a way better alternative that should always be used.


## Using Workshop Auto Download

To let a user download something automatically you can add the following code in a lua file that is executed on the server.  
This example makes every player who joins download the workshop addon with the id "123":

garrysmod/lua/autorun/server/sv_workshop.lua:
```
resource.AddWorkshop("123")
```

You could manually do this for every addon in your workshop collection or simply use an addon like [https://steamcommunity.com/sharedfiles/filedetails/?id=2214712098](Ultimate Workshop Downloader) to automatically add any addon your server has to the download list for players.  
To use the ultimate workshop downloader simply add it to your workshop collection so that your server uses it, that's it. The addon will automatically scan your addons and add them for player download upon joining.


## Complexity

FastDL is way more complicated to set up. It also makes you download individual files instead of a single file for the whole addon. In comparison, workshop download is easily setup and optimized for clients.

With FastDL you have to setup a webserver and make players download every material file one after each other via single HTTP calls.  
With WorkshopDL you can simply say `resource.AddWorkshop("id")` in any lua file and every client will download the addon automatically while joining.


## Bandwidth usage

FastDL makes users download the addons from your own server (your webserver).  
This means: If 10 people are joining at once, they are all downloading the addons from your server, using up your bandwidth. This could create higher pings for the currently playing players because the bandwidth is all used up by the joining players if the webserver is on the same machine as the gameserver.

With workshop download they download the addons from steam servers. This means that steam has all the bandwidth usage and your server gets relieved from those uploads. 

Example: If you use FastDL with 3GB of addons and 5 people join together, they will all download the addons from your server which would create a total upload load of 15GB for your server!


## Download size

With FastDL you "zip" the single files of your addon and the client has to download every texture and model one by one, which has a lot of overhead. For every file you have to create a new request and the compression of every file (with .bz2) can vary a lot.

With WorkshopDL the player downloads the addon as one big package in a single .gma file from steam servers.  
If the player already has the addon then he skips the download, saving download and load times.  
The .gma file seems to be really good with compression and this makes the download size smaller and thus creates faster join times.
