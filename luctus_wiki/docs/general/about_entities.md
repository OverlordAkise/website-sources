# About Entities

In Garry's Mod nearly everything is an entity, from a prop to a player.

## Networking/Rendering

The server only transmits data about entities that are close to the player.  
The client only renders entities that are close to the player.  

This system is called PVS (Potentially Visible Set). It is responsible for filtering network and rendering.  

To easily see this system ingame type the following in your console:

    sv_cheats 1
    mat_wireframe 1
    net_graph 3

With `mat_wireframe` (which is a cheat command) you can see things through walls, BUT you can only see the things that are rendered in your PVS.  
If you now spawn an Entity that has a model with animations (e.g. a standing NPC) then you will see the red bar in the `net_graph` rise. This is because this entity is close to you and actively networked to you.

If you move far away, until you do not see the entity through the wall anymore, then the `net_graph` will also show less red, because less entities are networked to you.

This system is very optimized and can not really be enhanced much as far as I know.


## Nodraw

To make an Entity invisible you can use `entity:SetNoDraw(true)` on the client. This only makes the entity invisible though, it is still networked to the client.  
To make an Entity vanish you can set `entity:SetNoDraw(true)` on the serverside, which makes the entity completely vanish for every player (no networking to the client anymore).


## Think delays

If you have to use the `Think` hook on your custom entity then consider using `self:NextThink()` to delay the next think call.  
Most of the logic in an Entities Think hook does not need to run multiple times per second, so you could make it run once per second like the following code example shows:

```lua
function ENT:Think()
    --[[ Your code here ]]--
    self:NextThink(CurTime()+1)
    return true
end
```

This works for serverside `ENT:Think()`, for clientside you have to use `self:SetNextClientThink(CurTime()+1)` instead.


## Unique ID

Entities that have been created by the map have a so-called "MapCreationID". This ID is unique and always the same for a map entity. This means no matter on what server or with which addons you start the map, this one door will always have the same mapid X.

You can use this to, for example, always delete a door on startup. An example:

```lua
hook.Add("InitPostEntity","remove_door_131",function()
    local ent = ents.GetMapCreatedEntity(131)
    if IsValid(ent) then ent:Remove() end
end)
```

To get the MapCreationID of an entity simply use `ent:MapCreationID()`. This function is serverside only.


## Entity limits

The game has an entity limit of 8176. This limit applies to networked entities (aka. not clientside entities).

If you create an entity (e.g. with the `ents.Create` function) and you reached this limit it will fail to create the entity and return NULL.

This limit is lower if you use DarkRP's FPP.  
Falco's Prop Protection sends information about an entity when it is created. It sends this information to all players. This information gets bigger the more entities exist.  
After around 7000 entities the message is too big to send at once, which means FPP creates "net-message overflow" errors and fails to send these messages.  
This means you should try to keep the current entity count on a map lower than ~7000 for the best experience (also because of performance reasons).

On the other hand, you can remove nearly all entities from the map and it will still run. According to my tests you can remove all entities except 3:

 - The map itself, entity 0, also called "worldspawn"
 - You yourself, the player
 - The player manager

The output from `PrintTable(ents.GetAll())` in this state returns the following:

```lua
-- [1]	=	Entity [0][worldspawn]
-- [2]	=	Entity [4][player_manager]
-- [3]	=	Player [1][OverlordAkise]
```

This means that all doors, all lights, all custom events from the map and all weapons have been removed.

In summary, according to my tests: The upper limit is 8176 and the lower limit is 2 + playercount.
