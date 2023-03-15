# Setup a DarkRP server

This "guide" is for people who just want to setup a DarkRP server.  
Before we begin: **Please do not buy any addons yet!**  
There will be many free addons mentioned that could save you money.


## Installing addons

There are 2 ways to install addons on your server: Either via a workshop collection or by manually uploading the folder.  
The workshop collection is easy: Simply get your steam api key and your workshop collection id and set it on your server.  
If you upload addons into your addon folder you have to look out for a few things:  
  
1. Addon-folders always have to start with a small (lowercase) letter!  
Good:easychat  
Bad:**E**asychat  
  
2. The addon should not contain subfolders of addons.  
Good: addons/easychat/lua  
Bad: addons/easychat**/easychat**/lua  
  
3. You can remove the *-master* from your addon name. It's just there because you downloaded it from github.  
4. Try not to have spaces in your addon name. Replace them with a dash (-) or an underscore (_).  
5. Always add the appropriate content packs to your workshop collection. Players and the server need the content packs for addons to work properly. For example: Blue's Addons need content packs from the workshop [here](https://steamcommunity.com/id/codeblue/myworkshopfiles?appid=4000) to work properly.


## Recommended addons

The 2 most important things are: The DarkRP gamemode itself ([https://github.com/FPtje/DarkRP](https://github.com/FPtje/DarkRP)) and the modification addon for it ([https://github.com/FPtje/darkrpmodification](https://github.com/FPtje/darkrpmodification)).

Now to the real addons.  
There are a few "must-have" addons that nearly every server has:

 - A [Fading Door](https://steamcommunity.com/sharedfiles/filedetails/?id=115753588) and a [Keypad](https://steamcommunity.com/sharedfiles/filedetails/?id=108424005) Addon
 - [3D2D Textscreens](https://steamcommunity.com/sharedfiles/filedetails/?id=109643223) for advertising ingame Shops (set the textscreen-limit to atleast 3)
 - [gm_apg](https://github.com/NanoAi/gm_apg) for anti-propkill / anti-propcrash (FPP is not that good at anti-crash protection)
 - [Permaprops](https://steamcommunity.com/sharedfiles/filedetails/?id=220336312) to make props/npcs respawn after a map change
 - [Classic Advert](https://steamcommunity.com/sharedfiles/filedetails/?id=1149344121) so that you can write in the global non-ooc chat with /advert
 - [ULX](https://steamcommunity.com/sharedfiles/filedetails/?id=557962280) and [ULib](https://steamcommunity.com/sharedfiles/filedetails/?id=557962238) for administration of your server. (You can also use the preinstalled FAdmin mod if you want)

A few more, non important addons:

 - [The sit anywhere script!](https://steamcommunity.com/sharedfiles/filedetails/?id=108176967) for more roleplay
 - [Pickpocket](https://steamcommunity.com/sharedfiles/filedetails/?id=503194668) to make the thief more THIEF
 - [Precision Tool](https://steamcommunity.com/sharedfiles/filedetails/?id=104482086) to make building bases easier and more fun
 - [Advanced Duplicator 2](https://steamcommunity.com/sharedfiles/filedetails/?id=773402917) to make building bases way faster and more comfortable
 - Cars, because younger players love having expensive cars (either [TDM](https://steamcommunity.com/sharedfiles/filedetails/?id=112606459) or any other type of car)
 - Custom Weapons for better **Dark** Roleplay ([M9K weapons](https://steamcommunity.com/sharedfiles/filedetails/?id=128089118) are pretty popular and easy to use)
 - A new HUD, Scoreboard and F4 menu Design
 - A custom chatbox addon, because the default chatbox lacks features
 - Either an inventory system or a good Pocket Blacklist (so that players can store their weapons)
 - Atleast one legal job that can make money without spending much for it, so that even poor players can get back to roleplaying quickly.
 - If you want a taser you can use [This free workshop taser](https://steamcommunity.com/sharedfiles/filedetails/?id=1729622779).

Addons you should **NOT** buy:

 - *Anticheat* - If you set up your server without being braindead then you don't need an anticheat, as most exploits are fixed anyways. Most Anticheats also use a lot of CPU / Network Bandwidth, which could make the server more laggy.
 - *Prop-Protection* - There are a lot of free and better alternatives than the paid ones, e.g. gm_apg.
 - *Admin-System* - ULX is free and way better than any admin mod you can buy. Do NOT buy xAdmin or SAM, they do the same thing but just cost something. Don't be an idiot!
 - *Job-Whitelist* - You can do Job-Whitelisting (with leveling systems, steamIDs, ulx ranks, etc.) without any addons! This is a feature of default DarkRP.
 - *Spawnpoint manager* - This is also a darkrp inbuilt function, you can set spawnpoints for specific jobs without any addon!
 - *Job-Configurator* - You can configure jobs easily in DarkRP itself. There is no need to spend money on a dynamic ingame job-configuration system!
 - *Logging System* - The default logging is more then enough. If you don't trust your players during support sessions, how can you trust them to make proper roleplay?


## Free addons

 - [https://github.com/OverlordAkise/darkrp-addons](https://github.com/OverlordAkise/darkrp-addons) for huds, scoreboard, warn system and co.
 - [https://github.com/In-memory-of-CODE-BLUE](https://github.com/In-memory-of-CODE-BLUE) for ATMs, Jobs and co.
 - [https://github.com/TeamUlysses/ulx](https://github.com/TeamUlysses/ulib) and [https://github.com/TeamUlysses/ulib](https://github.com/TeamUlysses/ulib) for Administration; do NOT buy SAM!
 - Everything in the steam gmod workshop


## Configuration

In the `garrysmod/cfg/server.cfg` you should set the following:

 - sv_playershurtplayers 1

Without this the players couldn't damage each other.  
You should also set the tickrate to **33** and not 66. 66 creates more serverload at best and lags / crashes at worst.

In the darkrpmodification's `settings.lua` you should atleast change the following:

 - disallowClientsideScripts should be set to true to disallow people from cheating (line 76)
 - dropspawnedweapons should be false or else people can put their cop weapon in their inventory and duplicate weapons (line 87)
 - restrictbuypistol should be true - if it's false citizens can buy pistols via the F4 menu (line 143)
 - adminnpcs and adminvehicles should be 1 or you won't be able to spawn anything as admin (line 179)
 - Change all the 'allowedProperties' to false except 'remover'. (Lines 468 to 473)

It is also a good idea to set sandbox restrictions. Those could be also set in the server.cfg file:

 - sbox_maxeffects 0
 - sbox_maxragdolls 0
 - sbox_maxballoons 0
 - sbox_maxdynamite 0
 - sbox_maxemitters 0
 - sbox_maxlamps 0
 - sbox_maxthrusters 0
 - sbox_maxwheels 0

*Don't forget to create a prop blacklist and toolgun blacklist!*


## Other checks

 - Check all of your addons (Playermodels of Jobs, Equipment of Jobs like Methcook, etc.) for ingame errors. Either lua errors or error models. A lot of servers have a lot of error models (mostly playermodels) when first joining, because they forgot to add it to the download. Let someone else join and let them check if they see any errors!
 - Also check if the console gets spammed by errors. That may not generate errors for the player, but lots of console messages with stuff like "0 nil" are still not nice to see and create more cpu load than needed.
 - Write your own DarkRP jobs and shipments and do NOT use something like dconfig to create them dynamically ingame. This could lead to conflicting problems with other addons. (Someone once had problems with realistickidnapsystem and dconfig jobs)
 - Have your rules easily accessible ingame. (Either in the F4 Menu, Scoreboard or with ulx's !motd)
 - You can also use ULX to create an auto popup whenever you join that shows you the rules. It's called MOTD, and with ULX you can set the MOTD to an URL of your choice.
 - Look out for big addons that download when joining your server. Downloading 4 GB of files just to join a DarkRP server is a bit much isn't it? Try to keep it as low as possible, because some players don't like waiting.
 - Try not to make too many rules (KISS principle, "Keep It Simple Stupid"). Try to configure restrictions ingame instead of writing them in your rules. (Example: Install an anti-bunnyhop addon instead of having a rule for it not to do that)
 - Don't add too many systems. Having a fine system, a radar station, a special "toll" addon, a special taser and cuff system for cops is a bit too much. The more addons you have, the higher is the possibility of 2 addons conflicting and creating errors. Same with "Logging" addons like bLogs: They use a lot of resources, so choose carefully if you **really** need them.
 - Enable (if you want) the default /afk and cooking addon in darkrpmodifications/ lua/ darkrp_config/ disabledDefaults.lua. With this people will automatically be moved AFK and people also get hungry ingame after a while.
 - Disable the "Context Menu" for players. If you hold C and right-click on a prop you can delete it or disable collision. Players shouldn't be able to disable collisions for their props.
 - Don't create too many Donator-Only Jobs. These don't make me want to donate, these make me want to leave the server.
 - Don't add too many cars at once. They add a lot of download size, and having a lot of cars at the beginning means you won't be able to add more later on without adding more downloadsize and lag to the server.
 - Don't forget to add an ATM system to your Server (Critzer ATMs or Blues ATMs)
 - A custom chat is always nice too, because the default Gmod chat is missing a lot of "comfortable" functions.
 - Try to make your money system fun and not too strict. Grinding 20 minutes for an AK47 is not fun. Also have a job where people can get money without spending anything.


## Server rules

Roleplay rules are difficult to get perfect, but they are easy to get right.  
The best tip would be: Keep them simple and clean. Try to disable things ingame and not just add rules to prohibit them.

Example: Install an anti-bhop addon instead of prohibiting people to bunnyhop via a rule.  
Here is a checklist of things to do better:

 - Even if many people will disagree: Try to look at other server's rules. It's not forbidden to get some kind of template from others on what you have to include in your own rules.
 - If you want a big and good server: Explain rules like RDM and VDM. When I first began playing DarkRP there was only "VDM is forbidden". What is VDM though? Explain unknown terms and wordings. Example: "RDM, the killing of players with no roleplay background, is forbidden."
 - Rules like "Be aware of NLR" or similar are not good. What is NLR? How long does it last? Explain it instead of simply saying "it exists".
 - Try to be consistent with your wording. "strictly forbidden", "prohibited" or "not allowed and will be punished" are all different wordings for the same purpose of "don't do that". Simply write "X is not allowed" and it's simple and easier to remember then "X is strictly prohibited and will be punished".
 - Try not to have any "wildcard rules" like: "Teammembers can still punish you if you did something wrong without breaking the rules". These rules are basically "Im an admin, i can do whatever i want and how i like it" and these make the server look worse.
 - Don't have your "punishments list" in your rules. Players shouldn't be able to see how long they get punished for breaking rule X.

