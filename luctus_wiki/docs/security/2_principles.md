# Security principles


## The one principle

The most important and only security principle you should know is:

> *NEVER TRUST THE CLIENT*

A client with bad intentions can always change their code and thus send you either garbage or malicious data.  
Coming from this I also always tell people:

> *A player with malicious intent can send any and all net messages whenever he wants and how many times he wants.*

The kind-of second principle, that stems from the first one, is:

> *ALWAYS CHECK AND VERIFY USER INPUT*

Most addons' exploits nowadays stem from "missing input verification" or "trusted the client with verification".


## Example

Let's go over an example. (Please remember this is bad code on purpose.)  
You want a system where, on a DarkRP server, admins can retrieve a physgun if they need one.  
The current bad code:

```lua
if CLIENT then
    function GetPhysgun()
        if not LocalPlayer():IsAdmin() then return end
        net.Start("admin_physgun")
        net.SendToServer()
    end
else --SERVER:
    net.Receive("admin_physgun",function(len,ply)
        ply:Give("weapon_physgun")
    end)
end
```

The above code has a big exploit: Nothing gets checked on the serverside. Every player can send the net message to the server's net.Receive function and get a physgun.  
Ofcourse a player needs to execute custom lua code to be able to use this exploit, but it is not difficult to do that nowadays. An anticheat won't save you from this exploit 95% of the time.  

The serverside code snippet from above can be made better by only adding the check from client to server:

```lua
if CLIENT then
    function GetPhysgun()
        if not LocalPlayer():IsAdmin() then return end
        net.Start("admin_physgun")
        net.SendToServer()
    end
else --SERVER:
    net.Receive("admin_physgun",function(len,ply)
        if not ply:IsAdmin() then return end
        ply:Give("weapon_physgun")
    end)
end
```

Now the code is following the "NEVER TRUST THE CLIENT" principle. This is only an example though, more detailed examples are in the next following pages.
