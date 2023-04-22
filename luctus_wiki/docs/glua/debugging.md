# Debugging in Gmod

Programming can sometimes be a tedious task without any good debugger.  
Here I want to list all the small things that help me test my addons.

## lua run

The easiest way to inspect the LUA state is via `lua_run` and `lua_run_cl` console commands.

For example, if you are unsure what the current value of `GAMEMODE.Config.adminvehicles` (a DarkRP config value) is you can use the following console command on the server:

    lua_run print(GAMEMODE.Config.adminvehicles)

You can also do this for local or shared variables on the client with `lua_run_cl`.


## print and PrintTable

If you are unsure if code is being run then you can add some simple debug `print` statements.

I highly suggest to mark them as DEBUG or else you will forget them before releasing your addon.  
I always do the following, here as an example with a function called PlayerKill:

```lua
function PlayerKill(ply)
    print("[DEBUG]","PlayerKill:",ply)
    --[[ do something here ]]--
end
```

This way you can see immediately that it is a debug print made for developing and after you are done writing your addon you can search for (case sensitive) "DEBUG" and remove all the prints.

If you print using comas `,` you can also print `nil` values, which means your prints themselves will not create any errors.


## Entities

To easily debug entities you can use the `Entity`.  

The `Entity(id)` function gets you an Entity with the specific id, but the first `player slot amount` number of IDs are used for getting players. This means that, to get the first player that joined on the server, you can use `Entity(1)`.

Now, for example, you spawned an Entity and want to see if it has the correct class.  
You could run the following:

    lua_run print(Entity(1):GetEyeTrace().Entity:GetClass())

Here `Entity(1)` gets the first player, then his eyetrace, then the entity that he is looking at, then its class.  
This line also helps you to get player attributes, positions, entity attributes, etc..
