# About Cheating

Cheating is very easy and you can not stop players from doing it.

Cheats are (mostly) third party programs or code that enables a player to do things he normally shouldn't be able to. This gives him the upper edge over other players.

There are 2 ways of "using cheats":

 - C++ (.dll) cheats
 - LUA (.lua) cheats

The cheats in LUA can be detected more easily than the cheats in C++.  
C++ cheats are "invisible" to the LUA code, which means anticheats can not detect it by injection alone.  
LUA cheats on the other hand change things that you can detect with other lua code.  
This ofcourse depends on what you are trying to do, but C++ cheats are considered "more undetectable" than LUA cheats.

There are also different types of cheats:

 - Exploit menu
 - Aimbot / Wallhack (Classic "cheats")

The first type, "exploit menus", are dangerous for the server itself.  
The second type, wallhack and aimbot, are more detrimental to other players and the fun of gameplay.


# About Exploits

Exploits are accidental bugs that enable players to do things they shouldn't be able to.  
The main difference to backdoors is that exploits are mostly created by accident and backdoors are placed on purpose.

Most exploits use net-messages to send data to the server it normally doesn't expect.  
Every player can, if he can execute lua code, send any data they want to any network receiver on the server they want.

A small example:

client code:
```lua
if not ply:IsAdmin() then return end
net.Start("GiveGodMode")
net.SendToServer()
```

server code:
```lua
net.Receive("GiveGodMode",function(l,ply)
    ply:GodEnable()
end)
```

The example above gives a player godmode if he is an Admin. This admin check is clientside only, which means the server - if it receives the network message from a player - simply gives the player godmode.  
An important fact to consider: In normal gameplay this doesn't create any problems. The player can not abuse this bug without executing third party code.

This example is considered an exploit because the creator tried to verify it correctly, just in the wrong place. It works in normal gameplay situations, but a cheater could easily use this to enable godmode for themselves.


# About aimbots&co.

The other side of cheats are the classic ones: Aimbot, Wallhack, ESP, NoRecoil, Bunnyhop, etc..

The easiest example would be a wallhack like this:

```lua
hook.Add("HUDPaint","MyWalls",function()
    for k,ply in ipairs(player.GetAll()) do
        local eyepos = ply:EyePos():ToScreen()
        draw.DrawText(ply:Nick(),"Trebuchet18",eyepos.x,eyepos.y,color_white,TEXT_ALIGN_CENTER,TEXT_ALIGN_CENTER)
    end
end)
```

This very simple example draws every player's name on their head through walls.  
This example is, ofcourse, very basic and is missing things like:

 - Do not draw your own name on your screen
 - Show the distance, weapon, rank of the player
 - Show where exactly the head and legs are, to interpret the distance quickly

An aimbot can be implemented in the same way: Go through all players and check which player is closest to your crosshair.  
Then set your Angles to that player's head position and that's it, aimbot done.
