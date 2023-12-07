# Debugging in Gmod

Programming can sometimes be a tedious task without any good debugger.  
Here I want to list all the small things that help me test my addons.


## serverconsole / lua_run

It is quite easy to do most debugging via the server console.

You can use the `lua_run` console command to execute lua code directly inside the console.  
To get the first player you can use `Entity(1)` in lua. So to, for example, get the model of the first player you can use:

```lua
lua_run print(Entity(1):GetModel())
```

Entity(x) gets you the entity with the specified index. The first few slots are reserved for players (e.g. a 20 player server has 1-20 reserved for players).  
You can get the Entity number of a player by using the following console command and checking the number infront of the player's name:

```lua
lua_run PrintTable(player.GetAll())
```

You can also debug or adjust serverside configuration, e.g. to allow any player to spawn NPCs you can use:

```lua
lua_run GAMEMODE.Config.adminnpcs = 0
```

This also works for clients if you have `sv_allowcslua` set to 1 with `lua_run_cl`.  
But be aware: This enables any player to execute any lua code, even cheats or exploits!


## print and PrintTable

If you are unsure if code is being run then you can add some simple debug `print` statements.

I highly suggest to mark them as DEBUG or else you will forget them before releasing your addon.  
I always do the following, here as an example with a function called PlayerKill:

```lua
function PlayerKill(ply)
    print("[DEBUG] PlayerKill:",ply)
    --[[ do something here ]]--
end
```

This way you can see immediately that it is a debug print made for developing and after you are done writing your addon you can search for (case sensitive) "DEBUG" and remove all the prints.

If you print using comas `,` you can also print `nil` values, which means your prints themselves will not create any errors.

Another way of doing it would be with a DebugPrint function like the following:

```lua
MYADDON_DEBUG = false
--Debug function that only prints if debug is ON
function MYADDON_DEBUG(text)
    if MYADDON_DEBUG then print(text) end
end
--Use it inside functions
function MyAddonDoStuff(ply)
    MYADDON_DEBUG(ply:Nick().." is doing stuff!")
    --[[...]]--
end
```

Both ways (print and debugprint) of debugging work the same and both have advantages and disadvantages.  
Putting prints anywhere and removing them before release keeps your code clean and efficient.  
Putting a debug function everywhere makes the performance a tiny bit worse and the code a bit more unreadable, but helps if you need to quickly debug something after release or on a live server.


## Finding function executions

An example: You want to see where the function "SetRunSpeed" is called because it is setting the wrong speed for players.

To get the "place of execution" for a function you can use the lua function `debug.Trace()`.  
This will print the stack trace of where it was called.

An example of using it: You can use the following code snippet in a server console to see whenever the `ply:SetRunSpeed(x)` function was executed and wher:

```lua
lua_run dplymeta = FindMetaTable("Player")
lua_run oldRunSpeed = dplymeta.SetRunSpeed
lua_run function dplymeta:SetRunSpeed(num) print("SetRunspeed") debug.Trace() oldRunSpeed(self,num) end
```

The above code overwrites the SetRunSpeed method and adds the stack-printing function to it.  
You could also do this in a lua file, but this way of doing it enables you to simply restart the server to remove this debug functionality.


## Hot-load scripts

If you add a new lua file on a server it won't be loaded until you do a full server restart. (a map restart is not enough for new lua files)  
You can load lua files from a website and thus execute them without having to restart the server.

Warning: This only works easily on the server and only works until the next map change!

You can load new scripts on a live server via a console command like this:

```lua
lua_run http.Fetch("https://mydomain.com/myscript.lua",function(body) RunString(body) end)
```

This gets the content of a website and executes it as lua code. This way you can load a lua file without having to restart/reload anything.


## Getting workshop's lua files

Sometimes there may be problems with workshop addons and you need to check the source code of them.  
This can be easily achieved on a running server with the following 2 console commands.  
They use my script from github to dump all the workshop collection's addons' lua files into `garrysmod/data/_dump/`.

```lua
lua_run http.Fetch("https://raw.githubusercontent.com/OverlordAkise/darkrp-addons/main/_scripts/sv_get_workshop_files.lua",function(b)RunString(b)end)
lua_run GetRecursiveWorkshopFiles("lua/","WORKSHOP")
```

This way you neither have to restart the server nor have to add a one-time script to execute.  
Warning: This will only dump the workshop lua files and not the server's addon folder lua files!


## Debugging props and networkvars

Sometimes you have to check if a door has the correct network variable set or if the entity has the correct class and angle.

This can be easily achieved with my "Dev HUD".

[https://github.com/OverlordAkise/darkrp-addons/blob/main/_scripts/cl_luctus_dev_hud.lua](https://github.com/OverlordAkise/darkrp-addons/blob/main/_scripts/cl_luctus_dev_hud.lua)

With this you can see most of the things you would need to see during development.

If you need to do it quickly on a live server you could also use the following console command to e.g. check the entity's model that Player#1 is looking at:

```lua
lua_run print(Entity(1):GetEyeTrace().Entity:GetModel())
```
