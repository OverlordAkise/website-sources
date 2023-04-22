# Image as material

You can easily download and use images from the internet in your UI elements.

To, for example, download an image as a client you could use the following snippet:

```lua
http.Fetch("https://luctus.at/images/istina_banner.png",function(body,size,headers,code)
    if code != 200 then
        ErrorNoHaltWithStack(body)
        return
    end
    file.Write("istina_banner.png",body)
    print("[luctus_status] Banner saved successfully!")
end,
function(err)
    ErrorNoHaltWithStack(err)
end)
```

You can only write files in the `data` folder, so the current path of the above image is `garrysmod/data/istina_banner.png`.

To then use this as a material you can simply call it with a relative path like this:

```lua
luctus_banner = Material("../data/istina_banner.png")
```

and then use the material variable in your UI like this:

```lua
local banner = vgui.Create("DImage", dframe)
banner:SetMaterial(luctus_banner)
```

To get a full, working example you can take a look at my `luctus_serverstatus` addon here: [https://github.com/OverlordAkise/darkrp-addons/blob/main/luctus_serverstatus/lua/autorun/client/cl_luctus_sstatus.lua](https://github.com/OverlordAkise/darkrp-addons/blob/main/luctus_serverstatus/lua/autorun/client/cl_luctus_sstatus.lua)
