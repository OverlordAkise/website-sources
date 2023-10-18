# About Anticheats

Anticheats are never perfect and are always a tradeoff in "Performance vs Detectability".  
To make a very very good anticheat you would have to sacrifice a lot of performance, but you can never create a perfect anticheat so it will never catch every cheater you encounter.  
This created a sort of "special anticheat" meta nowadays, where you try to detect a sub-section of cheats. (e.g. anti-exploit-menu or anti-aimbot anticheats)

Nowadays there are 2 main ways of "detecting cheats":

 - Detecting the injection / code change / execution
 - Detecting the changed gameplay of the player

The second one is way more complex than the first one.  
Detecting the injection of cheats can be implemented rather easily, but a good enough programmer can circumvent these detections.  
The second form of Detection analyzes a players gameplay (e.g. how perfect he executes bunnyhops, how good and precise he flicks and his headshot percentage, etc.). This needs way more resources than the first one, but if done serverside then the player can do nothing against it except "cheat less" to not be detected.

## Injection-Detection Anticheats

These use the least amount of resources but are able to catch nearly all script kiddies.  
These simply check if newly run code has any cheats and if yes tells the server to ban the player in question.

This is mostly done on different levels, I would logically divide them into 2:

 - Detect if newly compiled code comes from an unknown source (injection)
 - Detect if code contain cheat-like words (state)

All these different methods have to analyse the clientside LUA state and thus have to run on the client.  
This means the client could also manipulate these functions and overwrite them, making the anticheat useless.  
This is why most anticheats have everything "copied" to local variables, which make them immutable from the outside.

An example of an override:

```lua
local originalNetStart = net.Start
function net.Start(name)
    if name == "BanMe" then return end
    originalNetStart(name)
end
```

The above example does not send net messages who's name is "BanMe". This would ofcourse create errors on the clientside (because of calling SendToServer without net.Start first) but it works for not getting you banned.

Detecting the source of lua code is quite easy but can also be easily faked.  
An example: If a function in LUA gets compiled we will check the source of it. If the source file doesn't exist in the garrysmod/ directory or on the server then it is a cheat and we ban the player.  
This could be circumvented by telling the compiler that the source file is actually a file that is always there, e.g. `garrysmod/lua/autorun/game_hl2.lua`. Now the anticheat thinks everything is OK and the cheat gets executed without detection.

The second way of detection works one layer below: After the LUA code is compiled it will be executed, which will probably add hooks and functions which can be detected from within LUA.  
This means we could either scan regularly or simply overwrite the hook.Add function to check for "bad words" like this:

```lua
local oldHookAdd = hook.Add
function hook.Add(event,name,func)
    if string.find(string.lower(name),"aimbot") then
        net.Start("banme") net.SendToServer()
    end
    oldHookAdd(event,name,func)
end
```

The above code example overwrites the hook.Add function and adds a check to it: If a hook gets added with the word "aimbot" found in its name then the player will get banned.  
This could be circumvented by simply naming your hooks and functions differently. This could also be circumvented by not allowing specific net messages to be sent.

Most anticheats also verify that sv_cheats and sv_allowcslua is not set to 1 on the client. This could also be circumvented by not using such cheats to execute lua code (or by overwriting the functions to check these states).  
Some anticheats even scan your lua folder for "suspicious file names", which can be avoided very easily by not naming your lua files "aimbot.lua" or something like that.

As you can see above, it is not easy to detect the injection of third party code. It is also not easy to create an anticheat on the client side because you do not have control over the client.  
An example of this can be seen with EasyAnticheat or Riot Game's Vanguard: Both are clientside anticheats that boast with "being a very good anticheat", but there are still a lot of cheaters in the games that use those anticheats (e.g. Rainbow Six, Apex Legends, Valorant). 


## Gameplay-Detection Anticheats

These anticheats can use a lot of resources but are mostly serverside and can thus not be disabled by a cheater in any way.  
They try to analyze the gameplay of all players and ban those who have anomalies.

Examples include:

 - Analyzing how perfect someone bunnyhops in a row
 - How many degrees someone turned in one tick and if he aimed at someone's head afterwards
 - If someone is aiming (or shooting) at someone through a wall
 - Headshot percentage

The first example is the easiest to explain:  
If you bunnyhop you try to press jump again (as quickly as possible) after landing to stay on the ground as short as possible.  
Normally you can't do it perfectly (on a 33ticks per second server), which means the ticks that you stay on the ground are more than 1 most of the time. This means that if you do a frame-perfect bunnyhop multiple times in a row you are an anomaly and probably using a script or cheat to do it.

Another example: Detecting aimbot.  
You can check by how many degrees someone is turning and if he killed someone via a headshot afterwards.  This could be measured in "degrees per frame and kill afterwards".  

The last example is nearly the same as the second one: You can detect if someone flicks to someone else's head even though they can't see each other.  
This could also be paired with a "how many frames are you aiming at someone else's head" counter.

All of these methods can be done serverside, which makes it impossible for the cheater to simply disable them.  
This also means that they require more cpu resources than simple clientside cheats, which could create a CPU bottleneck if you have many players that have to be tracked.

The main problem with this detection method: They can all be circumvented very easily.  
How to stop getting detected for bunnyhop? Either do less hops in a row or simply make your bunnyhop cheat randomly worse so it skews the statistics.  
How to not get detected for aimbot? Set it to a lower setting where you turn less degrees per frame (also called anti-snap sometimes).

By simply "cheating less obviously" you can circumvent all these gameplay-based detection methods.  
They are also not good for detecting exploits or backdoors, as those are solely handled in code and not in gameplay.


## About Anti-Exploit

There are some addons called something like "Anti-Exploit" or similar. These try to stop exploits from being used on your server.

*This is impossible to do well.*

Most anti-exploit addons simply have a fixed list of exploited net-message names (e.g. "GiveMeAdmin") and overwrite them to ban the players who use it.  
Some anti-exploit addons even create fake net-channels that only exist to ban a player if they use it.  
An example:

```lua
util.AddNetworkString("SimplicityAC_aysent")
net.Receive("SimplicityAC_aysent",function(len,ply)
    ply:Ban(0,true)
end)
```

The above example creates a network channel from some old anticheat. This anticheat is not installed but some exploit menus will still show it as "existing and being exploitable". If you simply follow the exploit menu and click on "execute this exploit" then you will get banned permanently.

This can be very easily circumvented: Simply check if the addon you want to exploit is actually present on the server before executing it.

Another problem: New, recently found exploits or unknown exploits are not being protected by the anti-exploit addon. It can not detect 0-day exploits on the fly, this is impossible to achieve in gmod.

## Summary

Anticheats and Anti-Exploit addons are not perfect.  
They can catch "script-kiddies" (aka. people who only use cheats without understanding them) very well but against people who know the inner workings of gmod lua they do not work.

It is best to not buy an anticheat or put a lot of work into them, because not even I (OverlordAkise) can stop myself from cheating in garrysmod.

It is best to gauge it yourself if someone is cheating or not. Nowadays there are not many "server-destroying" exploits out there and most normal aimbots you can notice by playing yourself.
