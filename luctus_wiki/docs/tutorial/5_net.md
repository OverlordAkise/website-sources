# Networking Intro

Lua variables are not automatically transfered between client and server.

If you create a shared file (= a lua file in the `autorun/` folder) then the variables can be different on client and server.  
An example: Create a file like `garrysmod/addon/shtest/lua/autorun/sh_testing.lua` and write the following code into it:

```lua
if CLIENT then
    print("I AM THE CLIENT")
else
    print("I AM THE SERVER")
end
```

If you now restart your gmod and load into a local game it will print 2 different messages for server and client. (This is also visible by the 2 different colors the messages have in the console: orange for client and blue for server)

This also means: If you set a variable on the CLIENT then it will not automatically be set on the SERVER too and vice versa.

We have multiple ways to synchronize or send variables between realms:

 - **net** , with e.g. `net.Send(ply)`
 - **NW** , with e.g. `ply:SetNWString("test","yes")`
 - **NW2** , with e.g. `ply:SetNW2String("test","yes")`
 - **umsg** , with e.g. `SendUserMessage("test",ply,"yes")`

The last one, usermessage, is DEPRECATED and should NOT be used!  

All 3 remaining network methods (NW, NW2 and net) have their own use cases:

**net**  
If you want to send custom information (like a table) and have a function execute in the event that it was received then use the `net` library. You can send data to a single player or all players and also receive data back on the server (client->server). Clients can not talk between each other.  
This library is recommended for sending tables or sending data to only specific players. The NW and NW2 libraries always send/broadcast data to all players.

**NW**  
The `NW` library is used to set variables for a player on the server that get synchronized to every player automatically. This synchronization is "global", which means if you do `ply:SetNWString("test","ofcourse")` then it will instantly send every player on the server the updated NW string for this player.  
This library is not recommended, as it sends ALL NW variables of ALL players every 10 seconds even if nothing changed.  
This library is only recommended if you have very few such variables and use them only for things that the whole server should see, no matter of distance between players.

**NW2**  
The `NW2` library is nearly the same as the NW library except that if a player is too far away you will not get that player's NW variables.  
This means: If the server does `otherPly:SetNW2Bool("showStuff",true)` and the `otherPly` is too far away from you then you will not get this variable updated on your client.  
This library is recommended for variables that are only important if a player is close to you (=the client), for example if you want to have a NW variable for displaying a hat on a player's head.


To read more about the inner workings of gmod lua networking you can visit [https://luctus.at/wiki/#networking/1_intro/](https://luctus.at/wiki/#networking/1_intro/)



# Intro to NW/NW2

This library is rather simple to use.  

We can set variables on a player on the server like this:

```lua
ply:SetNW2Bool("isCool",true)
```

And on the client (or also server) we can get this variable of the player like this:

```lua
print( ply:GetNW2Bool("isCool",false) )
```

The above examples use the `NW2` library. To use the `NW` library instead simply remove the `2`.

The second value in the `GetNW` methods are the default value. If the NW variable doesn't exist on a player then it will return this second parameter.

The difference between NW and NW2 is optimization and a distance check.  
`NW` is unoptimized, sends data to all players if changed and resends data to all players every few seconds even if nothing changed.  
`NW2` is optimized, sends data only to players that are close to the changed-players position and it does not unnecessarily resend data.

For example, using `NW` for a player's "level" is OK. It would be weird if you use NW2 for that, join a server and see everyone's level as 0 because you havent been close to them yet (because of NW2's distance check for sending data).  
Using `NW` for things like `hasHatOn` or `ShowBloodDecal` is a bad idea. You do not need to know if a player has a hat on if he is very far away, using NW2 for this is a better idea.



# Intro to net

To simplify these examples we will use a shared lua file.  
This has lua code that gets executed on both the client and server, but each in their own "realm". (A realm is the place of execution, either server or client. This is visible by the file prefix, cl_ and sv_, but these are not mandatory and are only for readability)

If not already created: Create the `garrysmod/addon/shtest/lua/autorun/sh_testing.lua` lua file from above and write `print("loaded")` into it. (Empty lua files wont be loaded)

The wiki page for the net library is: [https://wiki.facepunch.com/gmod/Net_Library_Usage](https://wiki.facepunch.com/gmod/Net_Library_Usage)

A "net message" is literally a "network message" that you can send.  
You create this message by "starting" it and by "Sending" it (or Broadcasting).  
(Broadcasting is "sending it to everyone)  
This process is the same for server->client and client->server messages.

To be able to "receive" messages you have to register its name. This can only be done serverside and has to be done for every "channel" of network message you want to receive.  
(A "channel" is basically the name of the network message. There can be only one receiving function per network channel. A receiving function for a network channel is executed whenever a new network message with that specific name arrives on the server/client)

Let's start with an example. Don't forget, this is a shared lua file, so we have to differentiate between server and client:

```lua
if SERVER then
    util.AddNetworkString("my_channel")
    net.Receive("my_channel",function(len,ply)
        local text = net.ReadString()
        print("Received from client:",text)
    end)
end

if CLIENT then
    net.Start("my_channel")
        net.WriteString("Hello")
    net.SendToServer()
end
```

The above example shows everything needed for a working example.  
First we declare a new network channel with `util.AddNetworkString`. This can only be done on the server.  
Then we create a receiver for our channel. This means we can add a function to be executed whenever a player sends us a message with this name. The function has 2 parameters: The length of the network message (which you never need) and the player that sent it.  
In the next line we read the string the client sent with `net.ReadString()`. It is very important that you always write and read in the same order. This means if you write int and then a string, you also have to first read int and then the string.  
After this line we simply print the text the client sent us.

On the client we create a new net message with `net.Start(ChannelNameHere)`. You can not send 2 messages at once, which means calling `net.Start` twice back-to-back creates an lua error.  
In the line afterwards we write our string into the message with `net.WriteString()`. We could send multiple strings or a string and a number, the only limit is the max size of 64kb per net message.  
Last we send it to the server with `net.SendToServer()`. This function is, ofcourse, only available on the client.

There are a few things to take notice with net messages:

 - There is no spam protection, which means a malicious player could send > 33 net-messages per second and potentially lag your gmodserver
 - There is no inbuilt authorization, any player can send a net message to any channel whenever he wants
 - There is a limit on how many channels can exist (4095), but you will never reach this limit
 - There is a maximum size a net message can have (64kb) but you probably won't reach this if you do not send large tables


You can compress tables to never worry about the size again (hopefully): [https://luctus.at/wiki/#networking/efficient_net_writetable/](https://luctus.at/wiki/#networking/efficient_net_writetable/)


# Example: Colorful chat

Let's create a useful example: We want to send colorful chat message from server to clients.  
This means we create a network channel named `myname_chat` and send the client information if a player spawns. The client can then formats the received text and displays it in the chatbox.

```lua
--Please note: This is a shared file

if SERVER then
    
    --Define net name
    util.AddNetworkString("myname_chat_echospawn")
    --Add event to playerspawns
    hook.Add("PlayerSpawn","myname_spawn_echo",function(ply)
        --Send net message
        net.Start("myname_chat_echospawn")
            net.WriteString(ply:Nick())
        net.Broadcast()
    end)
    
end

if CLIENT then
    
    local color_red = Color(200,25,25)
    local color_green = Color(25,200,25)
    local color_white = Color(255,255,255)
    
    net.Receive("myname_chat_echospawn",function()
        local plyName = net.ReadString()
        chat.AddText(color_red, "[spawn] ", color_green, plyName, color_white, " spawned!")
    end)

end
```

The above code example on the serverside sends a net message everytime a player spawns. This net message contains the spawned player's name as a string and it will be Broadcast to every player on the server.  
On the client we create a receiver for this net message and print the player's name with an extra text in the chat.

On the client we create a receiving function without the 2 parameters (=len,ply) because we neither need the length of the net message nor do we have a player that it originated from (because it was the server).  
