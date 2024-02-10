# Security Intro

It is important to always verify if a player is allowed to do something.  
If you write an admin chat command then you HAVE TO check if the player is actually an admin. Same goes for net messages.

In the below examples we show how to authorize users.


# Example: Chat

Recently someone send me an example of an `/adminmode` chat command:  
(this code is not a 1:1 copy, but a recollection from memory)  

```lua
hook.Add("PlayerSay","adminmode",function(ply,text)
    if text == "/adminmode" then
        if ply:HasGodMode() then
            ply:GodDisable()
        else
            ply:GodEnable()
        end
    end
end)
```

The code works and is syntactically correct, but has a major flaw: It does not check if the player is actually an admin!  
This means any player can use this chat command to get godmode.

It is very important to always implement authorization. This means checking if a player has the permission to do something.  
To check this you can verify a user's usergroup (=rank).  
Authorization / Validation can be done in multiple ways:

```lua
--Admin method 1, simply checking if a player is an admin
if not ply:IsAdmin() then return end

--Admin method 2, checking if a player has a specific rank
local allowedRanks = {
    ["superadmin"] = true,
    ["admin"] = true,
    ["moderator"] = true,
}
if not allowedRanks[ply:GetUserGroup()] then return end

--Job, checking if a player has the correct team
local allowedJobs = {
    ["Citizen"] = true,
    ["Hobo"] = true,
}
if not allowedJobs[team.GetName(ply:Team())] then return end

--Distance, verifying that you are close enough to another player or entity
if ply:GetPos():Distance(otherPly:GetPos()) > 512 then return end

--Entity, verifying that the entity is a ragdoll
if ent:GetClass() ~= "prop_ragdoll" then return end
```

The first admin method is easier to implement but doesn't allow other ranks like supporter,moderator,operator to access specific functions. (=Less granular)  
The second admin method is easier to configure and can allow specific usergroups access to specific functions, but needs manual adjustment if you change usergroups.

This means the first code example should look something like this:

```lua
hook.Add("PlayerSay","adminmode",function(ply,text)
    if text == "/adminmode" then
        --new:
        if not ply:IsAdmin() then return end
        
        if ply:HasGodMode() then
            ply:GodDisable()
        else
            ply:GodEnable()
        end
    end
end)
```

In the above example we simply return and do nothing if the user that wrote `/adminmode` is not an admin.

# About error messages

I personally do not display error messages if it's either self-explanatory why a user can't do it or if the user shouldn't be able to do it anyways.  

In the chat example above: The command is called `/adminmode`. A user that is not admin shouldn't be surprised if the command doesn't work, because he is not an admin. This means we do not need to display any error message.

In the net message example above: A player shouldn't be able to send this net message if he isn't an admin. This means a normal player would never see this error message anyways. It is also a bad idea to show cheaters/hackers an error message as it would leak internal workings of your code.

A different example would be "validation" of user input.  
As an example:

```lua
hook.Add("PlayerSay","admin_respawn",function(ply,text)
    if text ~= "/adminrespawn" then return end
    if not ply:IsAdmin() then return end
    if ply:Alive() then
        ply:PrintMessage(3,"You can only respawn yourself if you are dead!")
        return
    end
    ply:Spawn()
end)
```

In the above example we validate 3 things: The chat message, the rank of the player and if the player is dead.  
The command is named "/adminrespawn", which means "user" players won't be able to use it. This is why we do not show an error if the ply who entered this chat command is not an admin.  
But if the player is alive we have to show an error message to the admin who entered the command. If we wouldn't show the admin this error message he wouldn't know why the command doesn't work, which is bad because in the worst case he will dislike the addon and not use it anymore.

Giving players feedback is important. If it is not clear what is happening (from context or other displays) then we should show players what happened. This means we show error messages for "common error cases" which could happen. Examples:

 - "You are too far away to do that"
 - "You are not the correct job for this!"
 - "You can not do that while you are dead!"

These are good and important feedbacks for the player.  

A gratuitous example would be if we return the following error message for users who use `/adminmode`: "You can only do this if you are an admin!".  
This is because the command is self explanatory already: `/admin` only works if you are an admin.
