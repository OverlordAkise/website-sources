# Preperation/Intro

Garry's Mod uses gLUA as it's scripting language.  
LUA is a general-purpose scripting language. Based upon that is luajit, a faster version which also adds a few more functions. Based on that is gLUA, which is luajit with more commands and syntax additions.


# Preperation

To edit .lua files I would recommend Notepad++. It can be downloaded here: [https://notepad-plus-plus.org/downloads/](https://notepad-plus-plus.org/downloads/). Click on the newest version and on the big "Download" image.

I prefer Notepad++ over Visual Studio Code (VSC) because it has a "codeblock" line highlight and is more lightweight.


# Using the wiki

[https://wiki.facepunch.com/gmod/](https://wiki.facepunch.com/gmod/)

The most important tool beside your code editor is the gmod wiki.  
You can find any and all functions in there. Use it whenever you need anything.

Example article:

[https://wiki.facepunch.com/gmod/Player:Spawn](https://wiki.facepunch.com/gmod/Player:Spawn)

This is the `ply:Spawn()` function. The color of the rectangle besides the function name is `blue` in the wiki, which means this function can only be used on the `SERVER`. 

If it is `orange` then it can only be used on the CLIENT. If it is both blue and orange it can be used on both.

An easy helper to quickly differentiate between server and client:

 - Does it draw anything on the screen? Then it is CLIENT only
 - Would it be cheating if I could use this function as a player? Then it is SERVER only

Example of using this helper:
 - draw.Roundedbox , draws on the screen, so CLIENT only
 - ply:Spawn() , could respawn yourself to avoid death, so SERVER only


# Addon folder structure

Always put your lua files in addons. NEVER put them in the `garrysmod/lua` folder. This makes it very unsorted and difficult to find, there is no advantage of putting your lua files there.

An addon's general lua files always have the same structure:

```
respawncommand
    └───lua
        └───autorun
            └───server
                └───sv_respawncommand.lua
```

In an addon you have multiple folders, one of them is the lua folder.  
Your addon's `lua` folder ALWAYS has to be directly after your addon folder.  
Inside the lua folder ONLY the files inside the `autorun` folder will be automatically executed. Other lua files in the lua folder outside of this autorun folder have to be manually included.

Inside the autorun folder can be a `client` or `server` folder, which hold .lua files that only get executed on either the client or server. By default all lua files inside the autorun folder get executed by BOTH server and client.

In the above example we have a `respawncommand` lua file which gets automatically executed on the server upon starting.  
It is good practice to name your lua files after the environment where they are being run.  

 - sv_ , for server only files
 - cl_ , for client only files
 - sh_ , for shared files which get executed on both client and server

This way you can instantly know where a file is being run at without having to look into the file.


Another example from an addon of mine:

```
luctus_nlr
    │   LICENSE
    │
    └───lua
        ├───autorun
        │   │   sh_luctus_nlr_config.lua
        │   │
        │   ├───client
        │   │       cl_luctus_nlr.lua
        │   │
        │   └───server
        │           sv_luctus_nlr.lua
        │
        └───entities
                nlr_zone.lua
```

In the above example you can see 4 lua files.  
One is named `sh_luctus_nlr_config.lua`, which gets run by both client and server and holds the configuration of the addon. Such configuration is commonly a `shared` file, because both client and server have to know about it.

Then there is a client and server file, which hold the actual logic of the addon. This logic is split between client and server, for example: The client has to draw the NLR sphere but doesn't need to know when he needs to create a sphere, because the server has the logic when a player died to create the NLR sphere.

Then there is also an entity called `nlr_zone`.  
All entities inside this folder are named after either the folder they are in or after the lua file that contains it. In this cause the `nlr_zone.lua` file will create an entity that is named `nlr_zone`.
