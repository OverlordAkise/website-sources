# Timer intro

A `timer` is something like `setTimeout` from javascript and executes a function after a specific delay.  
It can use the local variables of the function it was called in.

This way you can for example respawn someone after a fixed amount of time when they die.

There are 2 different timers you can use:

 - timer.Simple , which can not be removed and is good for short timers
 - timer.Create , which can run multiple times and can be removed, good for long timers

The wiki page for timers: [https://wiki.facepunch.com/gmod/timer](https://wiki.facepunch.com/gmod/timer)

# Preperation

Create a new addon and lua file, which should look something like this: `garrysmod/addons/testingtimer/lua/autorun/server/sv_timertest.lua` and add the line `print("file loaded")` inside it. (Empty file wouldnt be loaded)


# Simple timer

If you simply want to delay the execution of a function you can use the `timer.Simple` function.

The wiki page for this function: [https://wiki.facepunch.com/gmod/timer.Simple](https://wiki.facepunch.com/gmod/timer.Simple)

The signature of this function:  
`timer.Simple( number delay, function func )`

This function doesn't return anything and takes 2 arguments: The delay of the execution and the function to execute.

Let's start with a simple (heh) example:

```lua
timer.Simple(3,function()
    print("Hi")
end)
```

If you run the above code example we will get a `Hi` message printed in your console after 3 seconds.

A timer runs in the same "context" where it was defined and can use the local variables it would've had when it was defined.  
An example to showcase this:

```lua
name = "Chris"

function DelayPrint()
    local name = "Luke"
    timer.Simple(3,function()
        print("Hi, im "..name)
    end)
end

DelayPrint()

name = "Franz Kafka"
```

In the above code example we have a timer that prints a name.  
If we execute the above code example it will print `Hi, im Luke`.  
This is because the function that gets called by `timer.Simple` has the same local variables as before, no matter how much time passes between the timer definition and execution of it.

If you already have a function defined you can use it as the second argument.  
Example:

```lua
function Greet()
    print("Hi")
end

timer.Simple(1,Greet)
```

In the above example we simply write the function name WITHOUT `()` behind it.  
With brackets we execute a function, but in this case we want to pass the function as a parameter to a function, so we simply use the name without `()` behind it.

An example of using a timer: Respawning a player 3 seconds after he died

```lua
--Disable that players can respawn
hook.Add("PlayerDeathThink","disable_respawn",function(ply)
    return true
end)

hook.Add("PostPlayerDeath","auto_respawn",function(ply)
    --Start a timer that executes a function in 3 seconds
    timer.Simple(3,function()
        --If the player left we can not spawn him, so check it:
        if not IsValid(ply) then return end
        --If the player is already alive we do not want to spawn him twice:
        if ply:Alive() then return end
        ply:Spawn()
    end)
end)
```

**!** Please take notice of the codeblocks in the above example. We have a function inside a function.  
In the above code example we create 3 second timer after a player dies and respawn him when the timer function runs.  
The ply object refers to the same player as before, but we still check if he is valid because if a player leaves the server we can not spawn him anymore.


# Complex timer

To create a more controllable or complex timer we can use `timer.Create`.  
With this timer we can make it run more than once (or an infinite amount) and also remove it later.

The wiki page: [https://wiki.facepunch.com/gmod/timer.Create](https://wiki.facepunch.com/gmod/timer.Create)

The signature of this function:  
`timer.Create( string identifier, number delay, number repetitions, function func )`

As with `timer.Simple` the function doesn't return anything but this time takes 4 arguments: An ID or name for the timer, the delay, the number of repetitionss and the function.

The name is important for controling a timer.  
With the name of a timer we can:

 - Stop it with `timer.Stop(name)`
 - Check how many repetitions are left with `timer.RepsLeft(name)`
 - Check if it exists with `timer.Exists(name)`
 - Remove it with `timer.Remove(name)`

For simplicity I would recommend to not stop timers and simply remove them if not needed anymore. This way you do not have to manage the state of timers.

An example of a timer:

```lua
timer.Create("Simple_Timer",1,1,function()
    print("I ran after 1 second and ran only once!")
end)
```

The above example is nearly synonymous with a `timer.Simple`: It runs after 1 second and 1 time only.  
We can also run a timer created this way multiple times:

```lua
timer.Create("Runs10Times", 0.1, 10, function()
    print("Repetitions left:",timer.RepsLeft("Runs10Times"))
end)
```

The above example executes the function 10 times with a 0.1 delay between the executions. This means the statement takes 1 second in total to run the 10 repetitions.  
Each execution it prints the number of repetitions it has left.  
A timer can also run an infinite amount of times if you set the repetitions to `0`.

An example of using such a timer: A health regeneration function.

```lua
--When a player first spawns we want to give him health regeneration over time
hook.Add("PlayerInitialSpawn","myname_health_regen",function(ply)
    local plySteamID = ply:SteamID()
    --Execute function 1/s an infinite (0) amount of times:
    timer.Create("healthregen_"..plySteamID,1,0,function()
        --Do not heal player if he is not alive:
        if not ply:Alive() then return end
        --If player has left remove his health regen timer:
        if not IsValid(ply) then
            timer.Remove("healthregen_"..plySteamID)
            return
        end
        --Heal player for 1hp until he is max health:
        ply:SetHealth(math.min(ply:Health()+1,ply:GetMaxHealth()))
    end)
end)
```

In the above example we give a player who spawned on the server a health regeneration function.  
This function heals the player only when he is alive and only if he hasn't left the server yet. If the player has left the server (=not IsValid(ply)) then we remove his timer.  
We save his SteamID in a variable so that we can remove it later after he left the server. (If the player left we maybe can't access his SteamID anymore, so we cache it for later in a variable)  
The `math.min` function selects the lower value of the 2 arguments provided. This means the player can not regenerate health above his MaxHealth, because the second one is the max. health a player can have and is lower than Health+1 if Health==MaxHealth.

