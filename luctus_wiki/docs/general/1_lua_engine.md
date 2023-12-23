# gLUA engine details

Garrysmod uses **NOT** `lua`, but `lua-jit`.

This can be confirmed via benchmarks and also the available jit commands like: [https://wiki.facepunch.com/gmod/jit.opt.start](https://wiki.facepunch.com/gmod/jit.opt.start)

This means that the "2 - Lua Performance Tips" pdf and the lua-users "Optimisation Tips" articles are not always correct.

For some benchmarks between lua and lua-jit you can visit [https://github.com/OverlordAkise/gmod-lua-performance#gmod-lua-performance](https://github.com/OverlordAkise/gmod-lua-performance#gmod-lua-performance)

gLUA is lua-jit expanded with a few custom functions. That's why others and myself call it "gLUA", because it has noticable differences to other lua implementations.  
A good rule of thumb for detecting custom garrysmod lua functions and default lua functions: If the function of a module, e.g. `math.Distance`, starts with a capital letter then it is a custom garrysmod function.  
Examples:
 - math.log, math.random - inbuilt, default lua functions
 - math.Rand, math.Clamp - garrysmod lua functions

gLUA is also single-threaded and the main execution thread of the gmod "runtime".  
If you run a very expensive function in a `for` loop on a client then the game becomes unresponsive and freezes.  
If you have unoptimised functions on a server then the whole server is going to lag.

The binding of source engine to LUA is done via hooks.  
The source engine calls a hook, e.g. serversided PlayerSay, whenever such an event, e.g. a player sends a chat message, happens ingame.  
This also means, if a hook runs way too long and gets called very often, that the engine waits for the response and thus starts to lag. This can be easily observed if you have a very long `sql.Query` in a `net.Receive` function on the server: The whole server freezes until the database returns the result. (This problem happened with luctus_logs in the beginning but is fixed since beta)

## Max RAM usage

This was tested on a 32bit installation of garry's mod.  
Random strings were assigned in a loop until gmod returned `[ERROR] not enough memory` errors when assigning more variables.  
All these values are the return value of `collectgarbage("count")` which is the "kilobytes of memory used by Lua". (quoted from the wiki)

 - SV: ~2.372.236
 - CL: ~2.523.367

These are the values where any new assignment returns a "not enough memory" error.  
A manual garbage collection will not help in these situations as the GC runs regularly anyways.

