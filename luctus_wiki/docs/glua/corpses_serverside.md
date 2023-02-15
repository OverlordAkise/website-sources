# Corpses serverside

By default corpses are only on clientside.

If you kill another player the corpse has the class `C_HL2MPRagdoll`.  
You may also have noticed that the entity graph in `net_graph 3` doesn't show a spike if a player dies. This is because the ragdoll is fully clientside and thus calculated locally, which means every player sees a different ragdoll and the server won't even notice that a ragdoll just spawned.

Clientside ragdolls are bad if you have, for example, a Medic Addon that needs players to revive others by pointing a weapon (e.g. defib) at them.

This is why garrysmod has an already built-in option to enable serverside ragdolls.  
The class of these serverside ragdoll will be `prop_ragdoll` and the owner will be set to the player that died.  

The camera will still follow the invisible clientside ragdoll. If you don't want that you could set the player to spectate the serverside one when he dies.

## Implementation

You can simply `:SetNoDraw(true)` the clientside ragdoll and enable serverside ragdolls.  
The only problem: Serverside ragdolls don't vanish by themselves if you respawn, so you have to clean them up yourself. See the code below.

First you have to disable the clientside ragdolls that are automatically being generated with:

``` lua
--This is CL
hook.Add("CreateClientsideRagdoll","luctus_hide_cl_ragdolls",function(ownEnt,ragEnt)
  if ragEnt:GetClass() == "class C_HL2MPRagdoll" then
    ragEnt:SetNoDraw(true)
  end
end)
```

Then you can simply enable serverside ragdoll for the player:

``` lua
--Enable serverragdoll and remove serverragdoll on spawn
hook.Add("PlayerSpawn","luctus_remove_svragdoll",function(ply)
  if ply.lragdoll then
    ply.lragdoll:Remove()
    ply.lragdoll = nil
  end
  ply:SetShouldServerRagdoll(true)
end)
--remove serverragdoll on disconnect
hook.Add("PlayerDisconnected","luctus_remove_svragdoll",function(ply)
  if ply.lragdoll then
    ply.lragdoll:Remove()
  end
end)
--set ragdoll to player for removal at respawn
hook.Add("CreateEntityRagdoll","luctus_set_death_owner",function(ply,rag)
  ply.lragdoll = rag
end)
```

This saves you from completely reinventing the wheel like every medic addon out there.  
Most medic addons create their own entity and ragdoll-spawn-process, but this is neither necessary nor efficient if this method already exists.
