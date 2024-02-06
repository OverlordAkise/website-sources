# Hooks intro

By far the most important thing to know about gLUA are `hooks`.

A `hook` is an "event" that can happen. Examples are:

 - PlayerDeath
 - PlayerSpawn
 - PlayerSay (=chat messages)

With the `hook` library you can easily add your own functions and logic to these events.  
(A "library" is basically a table with functions.)

The hook library/table has 4 main functions:

 - hook.Add() , which adds functions to events
 - hook.Run() , which manually starts events
 - hook.GetTable() , which lists all events and functions inside it
 - hook.Remove() , removes a function from an event

When learning to program you are going to have to rely on the wiki a lot for these hook "signatures".  
(A signature is basically the arguments a function needs and what values it returns, in this case its "what variables does the hook provide and what can you return")


# Example PlayerSpawn

Let's create an example "addon": We want to write a chat message whenever a player spawned.

First we have to find out which hook is the correct one for "when a player spawns". By searching "spawn" in the gmod wiki we find the "PlayerSpawn" hook, which is the correct one.  

The wiki page: [https://wiki.facepunch.com/gmod/GM:PlayerSpawn](https://wiki.facepunch.com/gmod/GM:PlayerSpawn)

The line at the top of the wiki page is:  
`■  GM:PlayerSpawn( Player player, boolean transition )`

The wiki shows a "blue square" left beside the function name. This means this function is server-only, which means we have to put our code in a serverside file. This can be a file inside the `lua/autorun/server` folder. (If you have read the previous wiki entries you can re-use your `sv_testing.lua` file.)

The `GM:` stands for gamemode hook, which basically means "we can use `hook.Add` to add a function to this event". With other hooks like `ENTITY:XXX` we can not do it with hook.Add and only in the definition file for that entity.  

The values inside the brackets are the variables. First is the type of the variable and then a name that tells you what it is. The first variable we get is `Player player`, which tells us its a player object and is the player that spawned. The second value is a boolean (=either true or false) and is an indicator if the player spawned after a map transition (=changelevel console command, like in singleplayer source engine games).

Let's add our function to it.  
First we match the "signature" of the function.

We want to add our function to this event. This means we also get the 2 variables of this event for our function to use. This means the basic outline of our function looks like this:

```lua
function EchoSpawn(ply,transition)

end
```

Now we simply add a chat message to it using the provided `ply` variable from the event.  
For this we use the `PrintMessage` function. It needs 2 values: An id of where to print the message and a text.

```lua
function EchoSpawn(ply,transition)
    PrintMessage(HUD_PRINTTALK, ply:Nick().." spawned!")
end
```

Now we have a simple function that echoes a player's spawn.  
To add it to an event we now use the `hook.Add` function like this:

```lua
hook.Add("PlayerSpawn","echospawns",EchoSpawn)
```

The `hook.Add` function takes 3 arguments:
 - The event name, case sensitive
 - A unique name for this function for this event
 - A function that is to be executed when this event occurs

Now if you die and respawn ingame you should see a text message in chat.

Another way to add a function is to "implicitly" create it.  
A "implicit" function decleration is a function without a name.  
An example with the same code above:

```lua
hook.Add("PlayerSpawn","echospawns", function(ply,transition)
    PrintMessage(HUD_PRINTTALK, ply:Nick().." spawned!")
end)
```

**!** Please take notice of the missing function name.  
**!** Please take notice of the closing bracket behind the `end` on the last line, which closes the `hook.Add(` 2 lines above.

This code example works exactly the same as above, but we didn't give the function a name and instead directly added it into the hook.  
This is the preferred way to do it because it doesn't create another function.

I highly recommend to select a unique name (= second argument, in the above example: "echospawns") for your hooks.  
I would recommend to name it after your name, the addon name and then usage.  
For example my (=luctus) NLR addon has a hook that was created like the following:

```lua
hook.Add("PostPlayerDeath","luctus_nlr_set",LuctusNlrHandleDeath)
```

As you can see, the hook name is as unique as it can get, which means no other addon should overwrite this function.



# Example PlayerSay

Now we create an addon that changes a chat message if sent.  
PlayerSay is the server event for every chat message written by a player.  
The name comes from the `say` console command.

The wiki page: [https://wiki.facepunch.com/gmod/GM:PlayerSay](https://wiki.facepunch.com/gmod/GM:PlayerSay)

The line at the top of the wiki page is:  
`■  string GM:PlayerSay( Player sender, string text, boolean teamChat )`

This hook has 3 arguments: The player who sent the message, the text of the message and a boolean if it was written in the teamchat.

Left of the GM: you can see the `string` word. This means that the function returns a string.  
In the specific case of hooks it means: "A function that gets run by a hook can return a value to do something specific".  
If you scroll down the wiki page of this hook you can see the description of the return value: "What to show instead of original text. Set to `""` to stop the message from displaying."  
This means we can check with `if` if a message is a specific text and replace it in the players chat message with `return`.

Let's implement the code: We add a function to the hook that checks if the player wrote "/foo" and if he did we replace the text with "/bar".

```lua
hook.Add("PlayerSay","foobar",function(ply,text,isTeam)
    if text == "/foo" then
        return "/bar"
    end
end)
```


# Creating own hooks

Warning: This is not recommended for beginners.

You can also easily create your own hooks so that others can "hook into" your addons.

To do this simply think of a new hook (=event) name and add a hook to it like this:

```lua
hook.Add("MyAddonDo","new",function()
    print("MyAddonDo new executed")
end)
```

And then call it with `hook.Run` like this:

```lua
hook.Run("MyAddonDo")
```

An example usage of custom hooks would be logging: If you have a hook for most important events you could implement logging for it via hooks instead of hardcoding logging-code into a foreign addon.
(So-called `hardcoding` is a value that is inbuilt into the code without an "easy" way of configuring it. This could be changing a bought addon's code to include logging or not including a config value in the config file for a chat command)
