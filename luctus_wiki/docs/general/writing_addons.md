# Programming in gLUA

Garry's Mod is neither a complex nor complicated game.  
Your LUA code should reflect that and be written as simple and straightforward as possible.

In this very subjective page I want to go over common programming patterns and show why they need not be used.


## "Bad" Patterns


## Dynamic "auto-include"

TL;DR: If your addon doesn't have many files (e.g. more than 4) then simply put them in your myaddon/lua/autorun folder. Only use a custom directory structure and the `include` function if you either have many files to handle or need them to load in a specific order.

--

Most addons nowadays have a custom auto-include function that loops through the addons files and load them dynamically (and also AddCSLuaFile's them for the client).

This is not needed for most addons. It is unneccessary overhead and cpu usage if you don't have many files.

An example of this bad pattern:

You have an addon with 4 files: clientside,serverside,shared and shared-config.

The config file resides in `addons/myaddon/lua/sh_config.lua` and gets dynamically loaded via the `addons/myaddon/lua/autorun/sh_loader.lua` file.

This means that, instead of automatically being handled by the engine, the LUA interpreter has to do the following:

 - Loop through the `myaddon/lua` folder for files with sh_, cl_ or sv_ in their name
 - AddCSLuaFile the clientside and shared files
 - include all files on server and client

All this overhead could've been avoided if you simply put the sh_config.lua file in the `addons/myadon/lua/autorun/` folder.

I tested the load times for this specific scenario. The results were:

    INCLUDE - CL LOADED IN 0.00051820000000191 s
    INCLUDE - SV LOADED IN 0.00033640000000013 s
    AUTORUN - CL LOADED IN 0.00000049999999873 s
    AUTORUN - SV LOADED IN 0.00000409999999995 s

Look at the difference in time taken. Ofcourse it is not a lot of time, we are talking about way less than a second, but if you have even more lua files then it would take even longer.


## Dynamic HUDs with ScrW()

TL;DR: You do not have to make your HUD or UI fully responsive. It is nice to have, but nearly all of your players will play in FullHD (1920x1080) and thus make the hassle of scaling everything dynamically not worth it if it costs you considerably more time.

I recently heared that someone spent hours trying to fix his HUD because it was responsive but looked bad in 4:3 resolution. I haven't seen a player who plays in 4:3 though.

A responsive HUD uses for example `ScrW()` to calculate the size and position of it's elements according to the screensize of the user. This means that you have a different HUD length depending on if you play in 16:9 or 4:3 aspect ratio. This could create a lot of work, because you have to test your HUD multiple times in different resolutions.  

Some people then say "Well why are you playing in 4:3? Nobody plays in that resolution anyways". But why did you try to make it responsive then if your goal is not to be playable in multiple aspect ratios?

---

An easier way of creating HUDs is by making a fix-length box and creating the HUD elements inside it. This way it will look the same in all aspect ratios and doesn't need to be tested in multiple resolutions.

An example:

Instead of doing this

```lua
draw.RoundedBox(0, ScrW()/600, ScrH()/2.03, ScrW()/4, ScrH()/7, color_white)
```

where the box's length is between 480px (1920x1080 resolution) and 256px (1024 width resolution), you should simply do this:

```lua
draw.RoundedBox(0, 50, ScrH()-350, 300, 300, color_white)
```

where the box's length is always 300px and always in the bottom right corner with 50px padding from the bottom and 50px padding from the left.

This way the box always looks the same, no matter the screen resolution. 300px is also small enough to be able to fit nicely in any modern resolution that a player could use.
