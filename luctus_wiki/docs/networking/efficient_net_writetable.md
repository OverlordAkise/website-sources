# Efficiently network tables

The net library has a `net.WriteTable` function that is able to send any data you throw at it. This is quite unoptimized.

Side Note: In my over 5 years of LUA experience I never have reached the 64kb limit with a net message.

Instead of writing a multi-net.WriteInt function or a C-like networking wrapper you could compress big tables and send them as pure data with `net.WriteData`.

This would change the process from `net.WriteTable` to: Convert the table to JSON, compress it and send it via `net.WriteData`.  
This method requires a bit more CPU but it allows you to nearly "ignore" the limit on net-message sizes.


## Example code

Normally you would do the following:

```lua
if SERVER then
    util.AddNetworkString("tabletest")
    net.Receive("tabletest",function(len,ply)
        print("Message-Size:",len)
        local myTable = net.ReadTable()
    end)
else
    net.Start("tabletest")
        net.WriteTable(HUGE_TABLE)
    net.SendToServer()
end
```

If you send a randomized, 100 element string array it shows the message size as `11208` (in my example).

Now you could rewrite the logic to instead send a compressed json string that is way smaller in size.  
This means you have to send the length of data (as `net.ReadData` requires it) and then the data itself.

This would change the code to the following:

```lua
if SERVER then
    util.AddNetworkString("tabletest")
    net.Receive("tabletest",function(len,ply)
        print("Message-Size:",len)
        local textLength = net.ReadInt(17)
        local data = net.ReadData(textLength)
        local jsonText = util.Decompress(data)
        local myTable = util.JSONToTable(jsonText)
    end)
else
    net.Start("tabletest")
        local t = util.TableToJSON(HUGE_TABLE)
        local a = util.Compress(t)
        net.WriteInt(#a,17)
        net.WriteData(a,#a)
    net.SendToServer()
end
```

Now, if you send the same 100 element string array as the WriteTable example above it shows the message size as `7209`, which is 2/3 of the original size!


## Result

In comparison the different network-message lengths are:

    DEFAULT-WRITETABLE-LENGTH:          11208
    JSON-WRITESTRING-LENGTH:            12816
    COMPRESSED-JSON-WRITEDATA-LENGTH:   7209
