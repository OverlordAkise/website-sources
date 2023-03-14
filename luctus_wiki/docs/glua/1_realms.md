# Realm basics

This page serves to explain the basics about what the meaning of *SH / CL / SV* is.

## What is a realm?

In gLUA there exist 2 main sides for running code:

 - Serverside
 - Clientside

Any code you run always runs on either client or server. There is no code that is - willingly and at the same exact time - ran by both server and client simultaneously.

In the gmod wiki there is a color scheme for differentiating between server, client or shared functions:  
If the color of the square on the left of the function name is 

 - blue , then it is server-only
 - orange, then it is client-only
 - blue and orange , then it can be used on both

## Serverside

The server starts loading lua files when it starts.  
Basically speaking: It loads all `sv_` and `sh_` files.

This means that the code in the sv file can be expected to only run on the server and can thus contain only serverside functions.  
If you have a function like `render.X` or `draw.X` in a server file then it will create errors because the server doesn't know these functions.

In the gmod wiki the, for example, `chat.AddText` function has an orange square beside it, which means you can only use it in clientside files and NOT in serverside files.

To quickly know if a function is server-only: If you think you could cheat / hack / exploit with it by running it on the client then it is server-only.


## Clientside

The client starts loading lua files when it joins a server and finished downloading all required assets like workshop and fastdl.  
Basically speaking: It loads all `cl_` and `sh_` files.

Code in clientside files is expected to only run on the client. The client doesn't know functions like `PrintMessage` because it is server only, which can also be seen by the blue square in the gmod wiki beside the function.

To quickly know if a function is client-only: If it has something to do with the HUD or other frame-drawing logic then it is client only.


## Shared

The shared (`sh_`) files are loaded by both server and client.  
This means that the code inside those files has to be programmed while expecting this. This means that, for example, a function call that has to be made in a `CLIENT` only codeblock could look like this:

```lua
if CLIENT then
    print("I am the client!")
end
if SERVER then
    print("I am the server!")
end
```

If a client loads this file then it prints the first message in the game console, if the server starts up the second print will be printed to the server console.

Warning: The client and the server do **NOT** share the same variables!

Take a look at the following example:

```lua
mySharedVar = 0
function PrintMyVar()
    print("My var is: "..mySharedVar)
end
function SetMyVar(num)
    mySharedVar = num
end
```

Now if we run this and set the value only on the serverside the clientside value doesn't change:

```lua
] lua_run_cl PrintMyVar()
My var is: 0
] lua_run PrintMyVar()
> PrintMyVar()...
My var is: 0
] lua_run SetMyVar(3)
> SetMyVar(3)...
] lua_run_cl PrintMyVar()
My var is: 0
] lua_run PrintMyVar()
> PrintMyVar()...
My var is: 3
```

The shared file is loaded by both realms but does not share anything between server and client except the text that the file contains.
