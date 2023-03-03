# Protect net message

Net message exploits are one of the most common exploits in gLUA. This is because the programmers have to secure net.Receive functions themselves.


## Good Example

The following is a code snippet (the serverside `net.Receive` function) from an armory addon. With this the police players can retrieve selected weapons from their armory.

```lua
local validWeapons = {
  ["weapon_m42"] = true,
}
net.Receive("police_armory_retrieve", function(len, ply)
  local ent = net.ReadEntity()
  local weaponClass = net.ReadString()
  
  --[1] spam delay
  if not ply.armoryTimeout then ply.armoryTimeout = 0 end
  if ply.armoryTimeout > CurTime() then return end
  ply.armoryTimeout = CurTime() + 10
  
  --[2] check team
  if ply:Team() ~= TEAM_POLICE then return end
  
  --[3] check entity and distance
  if not IsValid(ent) then return end
  if ent:GetClass() ~= "sent_police_armory" then return end
  if ply:GetPos():Distance(ent:GetPos())) > 512 then return end
  
  --[4] check requested weapon
  if not validWeapons[weaponClass] then return end
  
  --do stuff
  ply:Give(weaponClass)
end)
```

This code checks everything that needs to be checked before giving the sending player his weapon.  
The first check (code block) is against net spam. You can only retrieve a weapon from the police armory every 10 seconds.  
The second code block checks if the player is even in the right team to retrieve things from the police armory.  
The third block checks the entity. Did the player send a valid entity? Is it a police armory? Is he close enough to interact with it? (we need a valid entity to calculate the distance between it and the player)  
The last check is about the weapon the player wants to retrieve. We shouldn't let the player take out any weapon they want, this could lead to exploits too.  

Now for a bad example.


## Bad example

Please remember: THIS IS A **BAD** EXAMPLE!

```lua
net.Receive("police_armory_retrieve", function(len, ply)
  local officer = net.ReadEntity()
  local weaponClass = net.ReadString()
  officer:Give(weaponClass)
end)
```

The code example above not only gives any player that sends the net message any weapon they requested. You can send any player with the net message and he receives the weapon you sent.  
This means that you, for example, can give everyone on the server a physgun with the following code:

```lua
for k,v in pairs(player.GetAll()) do
    net.Start("police_armory_retrieve")
        net.WriteEntity(v)
        net.WriteString("weapon_physgun")
    net.SendToServer()
end
```

This is, ofcourse, not good and should be avoided at all costs.


## Summary

To write non-exploitable net.Receive functions you have to look out for the following:

 - Never trust the client, always do checks on the server
 - First thing first, check for admin if its admin-only
 - If it could be spammed by users, set an anti-spam protection
 - Always check if the player is allowed to send the net message
 - Always verify user input (e.g. ValidWeapons table)

Ofcourse you can do checks on the client too, but never do checks *only* on the client. Either server-only or server and client.
