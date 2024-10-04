# Analysis: cl_cheats.lua

This is a small clientside anticheat code I have found years ago.  
I sadly do not remember the source for it anymore, only the name has been preserved: `cl_cheats.lua`.

## Source code

The following code is the whole clientside `cl_cheats.lua` file:

```lua
local h = hook.Add local s = "Tick" local n = math.Rand(0,1) local t = (true) local g = GetConVar local l = "sv_" local z = "allow" local p = "cs" local y = "lua" local c = "sv_" local f = "ch" local e = "eats" local m = g(l..z..p..y) local d = g(c..f..e)
h(s, tostring(n), function()
	if (!m) or (!d) or (!m.GetInt) or (!d.GetInt) or (m:GetInt() == nil) or (d:GetInt() == nil) or (m:GetInt() != 0) or (d:GetInt() != 0) then
		while t do
		end
	end
end)
```

The code is obfuscated but not very complicated. It is written in plain and valid lua.

## Code analysis

If you un-obfuscate the code it would look like this:

```lua
local m = GetConVar("sv_allowcslua")
local d = GetConVar("sv_cheats")
hook.Add("Tick", tostring(math.Rand(0,1)), function()
	if (!m) or (!d) or (!m.GetInt) or (!d.GetInt) or (m:GetInt() == nil) or (d:GetInt() == nil) or (m:GetInt() != 0) or (d:GetInt() != 0) then
		while true do
		end
	end
end)
```

To explain the code:  
Every Tick (up to 66 times per second) do the following: Check if the "sv_allowcslua" and "sv_cheats" console-variables exists and check if they not 0. If they are not 0 then crash the game with an infinite loop (`while true do end`).

It is very simple and only checks for 2 possible ways to use exploits: The console variables sv_allowcslua and sv_cheats.  
If one of them is set to 1 then the player can execute any lua scripts they want, which could include cheats.  
Normally a player can not set them to 1 without any external tools. This means that if they are 1 then they used hacks and should be banned, or in this case, crashed.


## Security analysis

The obfuscation is very simple and does not hide the code logic very well. Even without restoring the code you can already see what it does and how it works.  
The filename, which was `cl_cheats.lua`, also does not help with the bad obfuscation.

The code only checks for 2 console variables. There exist a lot more ways to execute cheats and more than 2 console variables that could be abused for cheating.  
This makes it too minimalistic, because barely anything is covered with this.

Тhe random hookname also doesn't help with security because it can very easily be detected and removed. The easiest way would be: Go through all Tick hooks and check if the name is a number between 0 and 1.  

It is also a bad idea to let this check run every tick, as it unnecessary worsens performance for players.

