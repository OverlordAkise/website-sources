# DarkRP LUA

This page explains a lot of darkrp's internal workings, what TEAM_ variables are and everything else that you have to look out for.

## Random info

The DarkRP gamemode is quite old, but this doesn't mean it is bad.  
Older software is preferred for production systems because it has been "battle tested" and thus has less exploits or bugs than newer, untested software.  
I can be sure that darkrp has no known exploits, because everything has been fixed over time already. Other gamemodes are not as audited as darkrp and could have some hidden exploits.

DarkRP still uses the `umsg` networking library on some code pieces, even though the `net` library should be used nowadays.

DarkRP loads during its main gamemode loading process the darkrpmodification's `jobs.lua` file. This means that any problem in the jobs.lua file creates a problem in the darkrp loading process. This is the reason that sometimes you spawn as a gray and block-y model when joining the server. If anything during the gamemode loading process fails everything after it may fail too.

DarkRP has an AFK system and a Cook-job with a "hungermod" already included by default. These may be basic, but it means that you do not have to code it yourself if you need these functionalities.

The MySQLite library of darkrp enables you to have a central point for your sql configuration. If you use this in your addons you can toggle the functionality between mysql and sqlite with one config setting in darkrpmodification.
This also means if someone is using mysql in darkrp and your addon uses the MySQLite library then your addon automatically uses their mysql database too. (Which means you do not have to code a mysql library yourself)


## TEAM variables

DarkRP's jobs each have a unique TEAM number. This system uses Garry's Mod's internal team system for switching and getting teams.

This means that your jobs.lua `TEAM_CITIZEN` variable is only your team number. You can get your team name with `team.GetName(ply:Team())`.

BUT: This also means that these are variables which need to be loaded. TEAM_CITIZEN is `nil` before the team gets created via darkrp.  
If you now have a config file with a table like the following:

```lua
JOB_WHITELIST = {
    [TEAM_CITIZEN] = true,
}
```

Then the above codepiece will create an error if its in the root codeblock of your file.  

This is why most addons either run it in a `timer.Simple(0,function()` or in a `InitPostEntity` hook.  
I personally prefer the hook variation, but to be completely "in-the-loop" with DarkRP you can also use the `postLoadCustomDarkRPItems` hook.  
This hook runs after the `darkrp_modules` folders of your addons have been loaded, which includes the darkrpmodification's folder (which includes the jobs.lua).

So to be able to use TEAM_ variables in your config you could do the following:

```lua
hook.Add("InitPostEntity","configload",function()
    JOB_WHITELIST = {
        [TEAM_CITIZEN] = true,
    }
end)
```

And to check for this you could use the following:

```lua
function DoStuff(ply)
    if not JOB_WHITELIST[ply:Team()] then return end
    --[[ do stuff ]]--
end
```


## Fancy jobnames

Instead of using `TEAM_` variables for your config you could also use the "fancy jobnames" (e.g. `Citizen`).

To get the nice name of a job you can use `team.GetName(ply:Team())`.

An advantage of using nice jobnames is that players can configure it easier without getting `Attempt to index nil value` errors if they forget to remove old job configs.

Your config could look like this and load from the root of your lua file:

```lua
JOB_WHITELIST = {
    ["Citizen"] = true,
}
```

And your checking function could start like this:

```lua
function DoStuff(ply)
    if not JOB_WHITELIST[team.GetName(ply:Team())] then return end
    --[[ do stuff ]]--
end
```

This is ofcourse slower than using the `TEAM_` variables version, but if speed is not an issue then you can use this version.


## Performance

Many people say that DarkRP is not "good" or too slow.  

*I have never seen objective proof for this statement.*

DarkRP is neither slow nor bad. It is old and has weird decisions in its code, yes, but that doesn't mean it's straight up bad.  
Most performance problems with DarkRP come from the addons people are using. If your server has 300 workshop addons and 100 folder addons then it's no surprise that the server is lagging with 4 players.

If you are having performance problems take a look at your addons first, as DarkRP itself was never in the top 10 of resource hogs whenever I checked.


## Gamemode Folder Structure

Even though the DarkRP gamemode folders are called "modules" that doesn't mean they are "modular".  
You can NOT simply remove one folder from the modules directory without everything breaking apart.  

You also shouldn't really change anything inside the gamemode folder. If you are running a simple RP server then you won't need to change core gamemode files.  
Most things you would want to optimize, even if it is removing unneeded code, could have unintended side effects and errors later down the road. The risk of breaking something is not worth the barely noticeable performance boost.


## Jobname, Nick

DarkRP overwrites the `ply:Nick()` function with its own one. This means that `:Nick` and `:Name` are returning your DarkRP ingame name. To get the steam name you can use the new `:SteamName()` function.

To get the jobname of a player you can do it via 2 different methods.

The first one is via the players "official" job table.  
You can do this via e.g. `print(ply:getJobTable().name)`. This gives you the jobname of a players job via its unchangeable job config.  

The second method is via the players "live" job darkrpvar.  
You can do this via e.g. `print(ply:getDarkRPVar("job"))`. This gives you the jobname as it is currently ingame.

In comparison, the second method allows you to display a changing jobname while the first method always gets the configured one.  
I personally recommend using the second method, because if you ever want to have custom jobnames or a spy job then it's better to use the dynamic variant.
