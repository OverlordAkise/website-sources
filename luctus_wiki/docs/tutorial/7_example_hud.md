# Addon example: HUD

This page will go over how to create a simple HUD addon, from file creation to completion.

# Creating the addon

We want to create a HUD, so it should have it's own addon.  
Create the following folders in your "garrysmod/addons" folder:

`myhud/lua/autorun/client`

Inside this folder create a lua file called `cl_myhud.lua`.  
This makes the following path to your file:

```
garrysmod/addons/myhud/lua/autorun/client/cl_myhud.lua
```

The file is created inside the `lua/autorun/client` folder.  
The `lua` folder contains all your addons' lua scripts.  
The `autorun` folder contains lua files that are automatically executed when the server starts.  
The `client` folder contains only lua files that are executed automatically by the client, not by the server.  

A HUD is a clientside lua file because the server never renders the HUD. The players see it rendered on their screen, which makes it a clientside file.


# Lua file frame

The lua file `cl_myhud.lua` is empty. An empty lua file will not be executed or loaded when the server starts.  
I recommend to add your "credit" to the first line and a "print" to the last line. This makes it easy to see who created it and easy to notice in the console when the script loaded.  
This "code frame" that you put into every lua file can also be called a "skeleton" or "template".

An example of this, to put into your lua file:

```lua
--MyHUD, created by Me



print("MyHUD loaded")

```

Inbetween those lines we will now add the HUD code.


# HUD functions

For creating a HUD you need the hook that paints things in your HUD. You could search the gmod lua wiki for "HUD" to find it: It's called `HUDPaint`.  
A hook is an event that calls a function whenever the event occurs. In this case whenever the HUD is being drawn we can add a function to it that will also get drawn on the HUD. For this we use the `hook.Add` function.

A really basic example:

```lua
hook.Add("HUDPaint","myhud_test",function()
    print("HUD has been drawn!")
end)
```

The above example will print out `HUD has been drawn!` as fast as your current framerate. This is because every frame that has been rendered your HUD also needs to be drawn. This means your FPS equals the number of HUDPaint calls per second.

To draw things we can use multiple functions:

```lua
--To draw boxes:
draw.RoundedBox(0,0,0,100,100,Color(255,255,255,255))
--To draw text:
draw.DrawText("Hi!","Default",0,0,Color(0,0,0,255))
```

The above example draws a white box in the top left corner of the screen and then draws a black text that says "Hi!" over it.  
The full example code for this:

```lua
--MyHUD, created by Me

hook.Add("HUDPaint","myhud_test",function()
    --To draw boxes:
    draw.RoundedBox(0,0,0,100,100,Color(255,255,255,255))
    --To draw text:
    draw.DrawText("Hi!","Default",0,0,Color(0,0,0,255))
end)

print("MyHUD loaded")

```

To find more functions you can visit the wiki:

 - draw functions at [https://wiki.facepunch.com/gmod/draw](https://wiki.facepunch.com/gmod/draw)
 - surface functions at [https://wiki.facepunch.com/gmod/surface](https://wiki.facepunch.com/gmod/surface)

(The `draw` functions are just easier `surface` functions)

2 other important functions are `ScrW()` and `ScrH()`. These 2 functions return the game's screen width and height, which should in most cases be your screen resolution of 1920 and 1080.



# HUD coordinates logic

We will try to create a rectangular HUD in the bottom left of the screen.  
It is important to understand the logic of relative HUD locations.

Let's imagine the HUD as follows (in the bottom left corner):

```
      +----------------------------+
      |  Chris            Citizen  |
      |                            |
      |  100      [██████████████] |
      |    0      [              ] |
      |                            |
      +----------------------------+
```

To start drawing the rectangle for this HUD you should choose a point as the start and draw the rectangle from there.  
To draw it you need 3 variables:

 - The starting point for the HUD
 - The width of the hud
 - The height of the hud

Let's take the top left corner as the starting point.  
We want to not have it stuck in the bottom left so we leave a 20px space around it.  


```
    20px from the left -> x = 20
    320px from the bottom -> y = ScrH()-320
        |
        v
       /+----------------------------+
        |  Chris            Citizen  |
        |                            |
 100px  |  100      [██████████████] |
        |    0      [              ] |
        |                            |
       \+----------------------------+
                  - 300px -
```


This means to draw the rectangle using the `draw.RoundedBox` function you would do the following:

```lua
--Starting point and width/height
local startX, startY = 20, ScrH()-320
local width = 300
local height = 100

draw.RoundedBox(0,startX,startY,width,height,color_black)
```

This way you have a black, unrounded box in the bottom left which serves as the main box for your HUD.  
All your other elements are also calculated from the starting point.

To now draw the Name of your player we calculate this point from the original box X and Y coordinate of the box.  
Because the name should not be stuck in the top left corner we simply add a few pixels to the startX and startY coordinates and draw the name there with an alignment to the left like this:

```
20px from the left + 20px -> x = 40
320px from the bottom + 20px -> y = ScrH()-320+20
           |
       /+--v-------------------------+
        |  Chris            Citizen  |
        |                            |
 100px  |  100      [██████████████] |
        |    0      [              ] |
        |                            |
       \+----------------------------+
                  - 300px -
```

So the draw function for the name would be (using the variables from the previous example again):
```lua
draw.SimpleText(LocalPlayer():Nick(),"Trebuchet18",startX+20,startY+20,color_white,TEXT_ALIGN_LEFT)
```

You can continue the logic like this for every HUD element.  
This way every HUD will look the same no matter your screen resolution or screen ratio.  
A disadvantage of programming this way is that the HUD will not scale for higher resolutions, which could result in it looking small when coding in FullHD but playing with 4K resolution.



# HP bars and co.

To programm a changing bar you simply draw 2 boxes over each other.  
The first box is the background for the HP bar, in a darker color.  
The second box is the HP bar, which's length is proportional to the HP of the player.

An example:

```lua
local barHeight = 50
--The max. HP bar width, = the background bar width
local maxBarWidth = 200
--Calculate the width of the HP bar in relation to your HP
local curBarWidth = (LocalPlayer():Health()*200)/LocalPlayer():MaxHealth()
--Draw the background
draw.RoundedBox(0,0,0,maxBarWidth,barHeight,color_black)
--Draw the HP bar ontop with color red
draw.RoundedBox(0,0,0,curBarWidth,barHeight,Color(255,0,0))
```

To see a finished HUD written like this you can visit the following link:

[https://github.com/OverlordAkise/darkrp-addons/blob/main/luctus_hud/lua/autorun/client/cl_luctus_hud.lua](https://github.com/OverlordAkise/darkrp-addons/blob/main/luctus_hud/lua/autorun/client/cl_luctus_hud.lua)

