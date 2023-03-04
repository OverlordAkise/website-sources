# Error handling

This page goes over how I learned to handle errors in addons correctly.


## use error()

It is bad to only print one line for an error and then return. Check the following 2 examples.

This is an example of using print and return:

```
[AWarn3] Loading Utilities Module
[AWarn3] Loading Options Module
[AWarn3] Option geladen!
[AWarn3] Loading Punishments Module
[AWarn3] Bestrafungen geladen!
[AWarn3] Loading Presets Module
[AWarn3] Presets Loaded
[AWarn3] Loading Permissions Module
[AWarn3] Loading Blacklists Module
[AWarn3] Loading SQL Module
[AWarn3] Loading ConCommands Module
[AWarn3] Loading Chat Commands Module
[AWarn3] Loading Discord Module
[AWarn3] Loading AWarn2 Import Module
Initializing Darkrp Bodygroups Manager
Loading Arizard's bodyGroupr
ERROR setting up myaddon!
[BRICKSCRAFTING] german language loaded
[BRICKSCRAFTING] CLIENT brickscrafting_drawing.lua loaded
[BRICKSCRAFTING] SERVER brickscrafting_sql.lua loaded
[BRICKSCRAFTING] SHARED brickscrafting_administration.lua loaded
[BRICKSCRAFTING] SHARED brickscrafting_inventory.lua loaded
[BRICKSCRAFTING] SHARED brickscrafting_player_misc.lua loaded
[BRICKSCRAFTING] SHARED brickscrafting_player_skills.lua loaded
[BRICKSCRAFTING] SHARED brickscrafting_quest_goals.lua loaded
[mLib] Service starting now, auto-updater version: 2
[mLib] Registered mLib addons found: mLogs
```

The "ERROR setting up myaddon!" message can be easily missed because it fades in with the other lines of text.

The second example shows what the error() function would look like:

```
Particles: Missing 'particles/L4D/rain_fx.pcf'
Particles: Missing 'particles/L4D/rain_fx_unused.pcf'
[PAC3] Loaded total 194 particle systems

[pac3-develop] addons/pac3-develop/lua/pac3/extra/server/contraption.lua:73: attempt to index global 'pace' (a nil value)
  1. unknown - addons/pac3-develop/lua/pac3/extra/server/contraption.lua:73
   2. include - [C]:-1
    3. unknown - addons/pac3-develop/lua/pac3/extra/server/init.lua:5
     4. include - [C]:-1
      5. unknown - addons/pac3-develop/lua/autorun/pac_extra_init.lua:28

///////////////////////////////
//      Ulysses Library      //
///////////////////////////////
// Loading...                //
```

You can clearly see that something went wrong there just by quickly going through the log file.

To refactor your code for this you should find out the places of code where an unrecoverable error happens (e.g. an error that stops the current code from executing further) and then replace the return with an error call.  
A bad example with a database query:

```lua
--[[...]]--
    local players = sql.Query("SELECT * FROM myplayers")
    if players == false then
        print("ERROR DURING SQL SELECT IN MYADDON!")
        print(sql.LastError())
        return
    end
--[[...]]--
```

This above example could go unnoticed in the tons of log lines you have. It also explains unnecessary information in plain text and doesn't even tell you the exact line where the error occured, only what went wrong in the database. For a big error like this (nothing worse than a broken database) it doesn't show this in the logs.

To make this example better use the `error()` function, which not only can display your string but also shows the exact line where the error happens:

```lua
--[[...]]--
    local players = sql.Query("SELECT * FROM myplayers")
    if players == false then
        error(sql.LastError())
    end
--[[...]]--
```

This makes the code more simple and also make the errors more visible and better to interpret.

If you are a fan of one-liners you could also use the `assert()` function like this:

```lua
assert(players, sql.LastError())
```

The problem with the above code: `assert` not only has false but also has nil as an error saved. This means that, if you either don't have any data (nil) or have an SQL error (false), it will throw an exception both times.  
This could lead to confusion because a nil value doesn't have to be an error in this case.  
(If you have 0 rows in your table its OK and doesnt need an error, but if you cant connect to the database then throwing an error is a good idea)

I personally don't use the `assert()` function much because in my opinion it is less readable for newcomers and also not ideal for checking `sql.Query` errors.


## return on unreachable code

It is unnecessary to always send back a response for completely garbage data.  

If you have a piece of code that can only be called by admins (e.g. an admin menu that only admins can open) then it is unnecessary to tell the player that he can not open it if he is not an admin.

An example; Instead of this:

```lua
net.Receive("admin_menu_do",function(len,ply)
    it not ply:IsAdmin() then
        ply:PrintMessage(HUD_PRINTTALK, "You are not an admin!")
        return
    end
--[[...]]--
```

simply do this:

```lua
net.Receive("admin_menu_do",function(len,ply)
    if not ply:IsAdmin() then return end
--[[...]]--
```

If a non-admin shouldn't be able to open the menu then it is unnecessary to tell players that he can't do something that he shouldn't be able to.

> If you have to cheat to reach a dead end then you don't need to notify the player about reaching it

&nbsp;

## Hooks and errors

It is not always a good idea to call error() in a hook though.  

If you call error() in a hook it could cause all the subsequent hooks to be killed too, which could interrupt some important gameplay logic.

This is why `ErrorNoHaltWithStack()` exists. This does the same as error() but doesn't return and kill the whole execution.  
This allows you to display a nice error in the logs without interrupting anything for other code / logic.

I would use `error()` for panic like "Couldnt initialize database" or "player didnt load".  
I would use `ErrorNoHaltWithStack()` for errors in hooks that could interrupt others or for adding code to unfamiliar addons.  
For errors I would NOT use a print and return anymore, as this isn't enough visibility for real errors.
