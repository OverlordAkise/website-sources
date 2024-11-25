# Dynamic HUDs

**Disclaimer: This page is an opinion of mine!**

TL;DR: Only do dynamic HUDs if you are already experienced with LUA and want to support all aspect ratios! If you do not want to explicitly support 21:9 or 4k in your HUD then use static variables for your elements!

You can easily, with the help of `ScrW()` and `ScrH()`, create dynamic HUDs that scale with the users screen resolution. For example, to always cover the top part of the users screen in darkness, you can use the following:

``` lua
hook.Add("HUDPaint","luctus_test_dynamic",function()
    draw.RoundedBox(0, 0, 0, ScrW(), ScrH()/2, Color(0,0,0,255))
end)
```

Here you calculate the height of the box to be half the screensize of the player (`ScrH()/2`).

BUT: Using dynamic screensize to scale your HUD is not always a good idea.  
If you do your HUDs like this then you want to make the HUD look nice on all aspect ratios and formats, e.g. 4:3, 16:9, 21:9, 4k, HD, etc..  

But many developers don't test their huds in these resolutions. That's why it's usually a bad idea to start coding HUDs with these dynamic values. Instead of calculating gaps and bar length (as you are supposed to do for a nice looking HUD) you are going to eyeball it with numbers like `ScrW()*0.09482`. (Yes, I have seen such numbers on HUDs and it looks atrocious)

That's why I recommend creating static HUDs. These will look good in ANY resolution because they never change. No matter your resolution or aspect ratio, they will always look the same. So instead of, e.g., `barLength = ScrW()*0.10417` (which is aprox. 200px) you should simply use `barLength = 200`.  
This also makes your code way more readable and even more efficient, because the HUD doesn't always have to calculate the length of the HUD.

A calculated example:  

You want to make a rectangular box that is 200px in width and 100px in height.  
You define it, as you play in FullHD resolution (1920x1080), like this:

```lua
local hudLength = ScrW()*0.10417
local hudHeight = ScrH()*0.092593
```

With FullHD (1920x1080) this would result in a 200x100 rectangle.  

But with a 21:9 (2560×1080) aspect ratio it would result in a 266x100 rectangle.  

This means the whole "design" is suddenly way more stretched (from 2:1 to nearly a 3:1 ratio) and looks completely different. The other way around would be a 4:3 aspect ratio, which would make it nearly a square.

A working code example would be:

```lua
local health_icon = Material( "icon16/heart.png" )
hook.Add("HUDPaint","luctus_test_dynamic",function()
    draw.RoundedBox(0, ScrW()*0.002, ScrH()*0.092, ScrW()*0.13, ScrH()*0.052, Color(10,10,10,235))--box
    draw.RoundedBox(0, ScrW()*0.005, ScrH()*0.118, ScrW()*0.124, ScrH()*0.0185, Color(255,10,10,255))--hp
    --Icon
    surface.SetDrawColor(Color(255,255,255))
    surface.SetMaterial(health_icon)
    local w, h = 16, 16
    surface.DrawTexturedRect(ScrW()*0.062, ScrH()*0.119, w, h)
    --Name
    draw.DrawText(LocalPlayer():Nick(), "ChatFont", ScrW()*0.005, ScrH()*0.097)
    --Job
    draw.DrawText(LocalPlayer():getDarkRPVar("job"), "ChatFont", ScrW()*0.13, ScrH()*0.097, Color(255,255,255), TEXT_ALIGN_RIGHT)
end)
```

(This is only quickly whipped-up by me and uses dynamic scaling everywhere.)  

This makes the HUD look quite nice in FullHD, the resolution I tested it on. It has enough space between the player name and job name.

But if you now switch your resolution to 4:3 or 21:9 then the whole aesthetic gets lost to either squished or way to spaced out text.

This is why I recommend newcomers to first learn how to correctly space and create their HUDs before jumping into dynamic scaling.
