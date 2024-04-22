# Addon example: Chatcommands

This page will go over how to create chatcommands on server and client, from file creation to completion.


# Creating the addon structure

We want to create a simple chatcommand, so it doesn't necessarily need it's own addon.  
For multiple, not connected chatcommands I would recommend creating a `scripts` addon or similar. In that addon you can collect and maintain all your smaller lua scripts without having a new addon folder for each.  
Create the following folders in your "garrysmod/addons" folder:

`_scripts/lua/autorun/server`
`_scripts/lua/autorun/client`

Inside the `server` folder create a lua file called `sv_chat_physgun.lua`.  
Inside the `client` folder create a lua file called `cl_chat_info.lua`.  
This makes the following paths to your files:

```
garrysmod/addons/_scripts/lua/autorun/server/sv_chat_physgun.lua
garrysmod/addons/_scripts/lua/autorun/client/cl_chat_info.lua
```

We create 2 seperate lua files because there are 2 different types of chat events: clientside and serverside.  
We will create a chat command for both realms, one that only the client executes and one that only the server executes.

Clientside chat commands are for opening windows or changing settings for your game only, not for the whole server.  
Serverside chat commands are for changing server settings, getting information from the server, getting information that only admins should get, and other things.  
(You could also open a window from a serverside chat command by sending a net message to the client, but this unnecessary networking should be avoided for performance reasons if no data is to be transfered from server to client)


# Example: Serverside chat command

Important: Always verify if a user is allowed to do something before doing it!

For our example inside `sv_chat_physgun.lua` we will create a chat command that gives a player a physgun if he types `!physgun` into chat.

To implement this we need to add a function to the "player sent a chat message" event. Events are called "hooks", and the hook for this is called `PlayerSay` (=When a player said something, this comes from the chat console command being `say`).

Let's create an outline (also called skeleton) in our lua file like this:

```lua
--Physgun chat command, created by Me

hook.Add("PlayerSay","physgun_chat_command",function(ply,text,isTeam)
    print("Someone wrote a chat message!",ply,text,isTeam)
end)

print("physgun chat command loaded")

```

If you write a chat message then you will get a message into your serverconsole which displays the player who sent the chat message, the text he wrote and if it was written into the teamchat.

Our logic for giving an admin a physgun if he writes !physgun would be the following:

 - If the player is an admin
 - If the text is !physgun
 - then we give him a physgun

This means our code would be the following:

```lua
--Physgun chat command, created by Me

hook.Add("PlayerSay","physgun_chat_command",function(ply,text,isTeam)
    if ply:IsAdmin() and text == "!physgun" then
        ply:Give("weapon_physgun")
    end
end)

print("physgun chat command loaded")

```

The above chat command is done now.

We can optimize it by changing a few things.  
Currently the script only allows admins (e.g. admin, superadmin) to get a physgun.  
We can instead use a table of allowed usergroups, which allows moderators and operators to get a physgun too.

This would make the above code look like this:

```lua
--Physgun chat command, created by Me

local allowedRanks = {
    ["superadmin"] = true,
    ["admin"] = true,
    ["operator"] = true,
    ["moderator"] = true,
}

hook.Add("PlayerSay","physgun_chat_command",function(ply,text,isTeam)
    if allowedRanks[ply:GetUserGroup()] and text == "!physgun" then
        ply:Give("weapon_physgun")
    end
end)

print("physgun chat command loaded")

```

At the top we created a table of our allowed ranks.  
A rank in garrysmod is called a "usergroup".  
We have this variable only locally because the name is generic and we do not want to make it public and changeable from other addons by accident.

We check if a rank is allowed by checking if a key is in the table.  
This can be done by simply using the square brackets `table[value]`. If the value exists it will return the value for it, in this case "true".

The same logic can be used to create a chat command that can only be used by specific jobs.  
An example: We want only the Mayor and Assistant job to have the possibility to get a physgun.  
In this case we simply change the values in the table and the function "GetUserGroup" like this:

```lua
--Physgun chat command, created by Me

local allowedJobs = {
    ["Mayor"] = true,
    ["Assistant"] = true,
}

hook.Add("PlayerSay","physgun_chat_command",function(ply,text,isTeam)
    if allowedJobs[ team.GetName(ply:Team()) ] and text == "!physgun" then
        ply:Give("weapon_physgun")
    end
end)

print("physgun chat command loaded")

```

Now the chat command "!physgun" only works for 2 jobs instead of for everyone or for specific usergroups.  
We changed the `ply:GetUserGroup()` to `team.GetName(ply:Team())` for the if check.  
The `ply:Team()` function returns a number for the current team of the player. To convert this into their team name (=job name in darkrp) we use the `team.GetName()` function.  
We do not use the Team number of the player here because it is dynamically created when the server starts from the order of the jobs in the `jobs.lua` file. It would be a bit faster but also would need more code to use only Team enums, because teams only get created after addons get loaded.


# Example: clientside chat command

What if we now want to create a chat command that only works for a player locally?  
Example: We want to show a player's information (name,steamid,steamid64) in chat if he types "/info".

For this small chat command we do not need the server for, because all the information we want to display is already available on the client. It is more efficient to not let the server execute code for something the client can do by itself.

The hook for the clientside chat command is `OnPlayerChat`.  
It is important to know that this hook gets called for every chat message sent by every player, not just by you yourself.  
This is why we first check if the player that sent the chat message is you. Afterwards we do the stuff we want.

```lua
--Info chat command, created by Me
hook.Add("OnPlayerChat","info_chat_command",function(ply,text,isTeam,isDead)
    if ply ~= LocalPlayer() then return end
    
    if text == "/info" then
        local ply = LocalPlayer()
        chat.AddText("Info: ",ply:Nick()," // ",ply:SteamID()," // ",ply:SteamID64())
    end
end)

print("info chat command loaded")

```

In the above chat command we first check if we sent the chat message. If it is not from us then we do not continue.  
Then we check if the text is "/info", and if it is we add our info into our chatbox.  
For adding text into the chatbox we use the `chat.AddText` function. This function also supports colors, ply objects and ofcourse strings. (This function only works clientsided, to add text to a players chatbox from the server use `ply:PrintMessage(HUD_PRINTTALK, "Hello")` instead)


# Example: dynamic input

What if you want to create a chat command that puts your text into "/me" chat?  
This example means: If you write `/me sleeps` into the chat then everyone would get the message `Chris sleeps` in chat (if your name is "Chris" ingame).

For this to work we need to cut out the text after the chat command.  
To accomplish this we use the `string.StartsWith` function to check if the chat message started with "/me " and then cut out the rest of the message with `string.sub`.  
We explicitly check for `/me ` (with a space behind it) so that we do not accidentally think that `/message` is a "/me" command.  
Because we change the chat message of a player we need to create this code serverside inside a `PlayerSay` hook. We will use `PrintMessage` to send the changed chat message.

```lua
hook.Add("PlayerSay","me_chat_command",function(ply,text,isTeam)
    if string.StartsWith(text,"/me ") then
        PrintMessage(HUD_PRINTTALK,ply:Nick().." "..string.sub(text,5))
        return ""
    end
end)
```

In the above example we check if the user used the "/me" command and then print out his text behind is name in chat with `string.sub`.  
The `string.sub` function cuts the text beginning from the 5th character (in this example), because the 1st-4th character is the `/me ` and the rest of the message begins at the 5th character.  
We `return ""` so that the original chat message is hidden from chat.


