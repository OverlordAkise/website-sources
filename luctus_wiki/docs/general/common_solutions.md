# Common solutions

This is a page that tries to document common problems and solutions.  


## General Problems

### Walking through props

Symptoms: You can walk through some custom props. There is a short "stop" before being able to walk through them.

This means the server does not have the model of the prop. Your client thinks that you can not walk through it but the server doesnt know the model (and the hitbox) so it just lets you walk through it.

Solution: Add the workshop item to the workshop collection that the server loads and it should work again.

### Gray Playermodels after joining

Symptoms: You join the server and only have a blocky gray model and lots of lua errors.

This happens because something created lua errors during the loading process of darkrp. This could be because of a lua error in the `jobs.lua`

Fix the first server-console errors that come up (except IHTTP errors) and it should work again.



## Errors

### HTTP failed - ISteamHTTP isn't available!

Symptoms: When starting the server an error that looks like "\[ADDON-NAME\] HTTP failed - ISteamHTTP isn't available!" appears in the server log.

This happens because some addons try to check for updates (or similar stuff) too early in the server start process.

Solution: This is not a big problem and can be left as is without noticing any problems. To fix it you would have to contact the addon creator and ask them to fix it.

### Error Variable X is multiply defined in material

Symptoms: The following error message in console:

    Error! Variable " " is multiply defined in material "models/qq_rec/v_rif_xm8/front_sight"!

Solution: This can not be easily fixed on your end. Please contact the addon creator and ask them to fix it.



## Addon specific

### Zero's Vendingmachines

#### entities/zvm_machine/init.lua:37: attempt to index global 'zvm' (a nil value)

Symptoms: When the server starts you see the following error message in your server console.

This happens because you used permaprops to save the vending machines instead of using Zero's console command for saving them.

Solution: Un-Permaprop these vending machines and save them with the correct !savezsm command instead
