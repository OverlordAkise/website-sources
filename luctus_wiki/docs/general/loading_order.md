# LUA files & loading order

There are 3 realms or "worlds" of lua files: Clientside, Serverside and shared.  
You can easily distinguish them by their short forms: `cl`, `sv` and `sh`.  
A clientside lua file is ONLY ran on the client, serverside only on the server and a shared file is being loaded by both realms.  

This does NOT mean that the server and client share the shared lua file's variables. Each side (server and client) have their own copy and variables of the shared lua file.

A *weapon* for example is shared: The client and the server needs to know what model the weapon has, the name of it, magazine capacity, firerate, etc..  

A *HUD* is clientside: The server never renders any graphics, only the client has a graphical interface.  
A *mysql* config file is serverside: The client should not and must not have the details of such a configuration.

The shared and clientside files must be send to the client. Without the files the client can't execute them.  
If you put them in an "autorun" folder they will be automatically sent to the client and executed.  

To manually include a lua file you have to use the `include` function. To send it to a client you have to use `AddCSLuaFile("myfile.lua")` in a serverside file and then use `include(myfile.lua)` inside a clientside file to load it.


## Addons

An example with an addon:

We have an addon called `luctus_hud` with a single lua file that contains the HUD. The path to the file is:

    garrysmod/addons/luctus_hud/lua/autorun/client/cl_hud.lua

This `cl_hud.lua` file is on the server. If a client joins this file will automatically be sent to the client and executed, because it is in the `autorun/client` folder inside the addon.  
On the other hand, every file you put in `autorun/server` will automatically be executed by the server.  
If you put a file into only `autorun`, for example `autorun/sh_config.lua`, it will be automatically executed by *BOTH* server and client.  

---

Another example with a manually loading addon:

You have a scoreboard addon named `myscoreboard` that has an `sh_loader.lua` file that loads the other files.

You have 2 files, which are located at:

    garrysmod/addons/myscoreboard/lua/autorun/sh_loader.lua
    garrysmod/addons/myscoreboard/lua/scoreboard/cl_scoreboard.lua

The `cl_scoreboard.lua` file is **NOT** being loaded automatically nor is it being sent to the client.  
This is why the sh_loader.lua file exists:

    if SERVER then
        AddCSLuaFile("scoreboard/cl_scoreboard.lua")
    end
    if CLIENT then
        include("scoreboard/cl_scoreboard.lua")
    end

The `sh_loader.lua` file manually adds the scoreboard file to the list of files the client has to download.  
Then when the shared file is loaded by the client it is included to be loaded.


## Order of loading

The loading order goes as follows:

 - (Before all, the server executes `garrysmod/lua/autorun`)
 - First the server reads all the addons and their files, executing them alphabetically
 - Then the gamemode gets executed completely (e.g. darkrp)
 - Then hooks from e.g. addons get executed and load other things (like `InitPostEntity`, `PostGamemodeLoaded`, `postDarkRPCustomThings`)

This means: Shared files always load before server/client ones, except in gamemodes.  

The order of loading lua for the server is:

 - lua/sh
 - addon/sh
 - lua/sv
 - addon/sv
 - gm/sv
 - gm/sh
 - all sweps and ents (gm and addon ents get loaded together)

lua/sh means the `garrysmod/lua/autorun` folder, and lua/sv means `garrysmod/lua/autorun/server`.  
gm means `garrysmod/gamemodes/darkrp` and addon means `garrysmod/addons/myaddon/lua/autorun/`.  
The client follows the same pattern just with `cl` instead of `sv`.


This order also shows that "custom loader" addons who include their serverside files in their shared addon files disrupt the natural loading order of gmod. (Hence why I dislike them for debugging purposes)  
