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
