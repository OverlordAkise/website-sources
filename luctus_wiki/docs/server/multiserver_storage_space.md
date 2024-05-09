# Saving storage space with multiple gmod servers

This page explains how to save storage space when having multiple gmodservers.

## When to use this

This only works if you have multiple gmodservers on one linux server.  
The servers have to be installed in a non-containerized way, e.g. steamcmd or linuxgsm. (Pterodactyl won't work)  
Example: You have 2 "Fun-Gamemode" gmodserver with nearly identical workshop collection and addons, installed with linuxgsm and running as users fun1 and fun2.


## Overview

If you have many gmodserver on the same linux server then they take up a lot of storage space.  
Each server has their own workshop collection, which means you have (servers*workshopsize) storage usage.

This can be summarized / united into a single folder, which means you save many GBs of storage space.  
For example: You have 3 nearly identical servers with a workshop collection size of 8GB. This means you have 24GB of only workshop content, instead of the de-duplicated 8GB only.

Advantages of this solution:

 - Less storage space used
 - If one server downloads the addon then others could have it too

Disadvantages of this solution:

 - It could create permission problems, because the test-server accesses the same workshop files as the production-server
 - Maybe permission problems because of new file ownerships inside the global cache


# Solution

You can use linux symlinks (`ln -s <src> <dst>`) to link all your server's workshop folders into a central, united workshop folder.  
This means every server accesses the same directory, which means that only one "workshop cache" exists.

If you have, for example, the gmodserver installed as `/home/fun1` and `/home/fun2` with their respective users `fun1` and `fun2` , then you could for example create a new folder in /home named `/home/gmod_cache`. This new folder will have all your workshop files.  

The following example shows the commands if you have 2 servers and want to unite their workshop cache into one.

First we copy the workshop files into the central directory.  
The following commands are executed as root:

```bash
# Create new workshop cache directory
mkdir /home/gmod_cache
# Copy the workshop files of the first server into the new cache
cp -r /home/fun1/serverfiles/garrysmod/cache/* /home/gmod_cache
# Copy the second server's files too
cp -r /home/fun2/serverfiles/garrysmod/cache/* /home/gmod_cache
# Now remove the old cache folder of the single servers
# WARNING: The next 2 commands delete stuff, use with caution!
rm -fr /home/fun1/serverfiles/garrysmod/cache
rm -fr /home/fun2/serverfiles/garrysmod/cache
```

After creating the new caches you have to link them as their respective users.  

```bash
# Login as user "fun1" and execute the following:
cd serverfiles/garrysmod/
ln -s /home/gmod_cache/ cache
# Login as user "fun2" and execute the following:
cd serverfiles/garrysmod/
ln -s /home/gmod_cache/ cache
```

You have to execute the symlink commands as the users because the ownership of the links have to belong to the user that starts the server, or else LinuxGSM will complain and not start the server.

Now you have to set the permissions correctly: The `gmod_cache` directory should be owned by root and writeable/readable for everyone.  
Execute the following as root to do just that:

```bash
chown -R root:root /home/gmod_cache
chmod -R ugo+rwx /home/gmod_cache
```

After this you can start the servers again and verify that everything still works.

To save even more storage space (and unite the full workshop content of the servers) you can also symlink the steam_cache/content folder, which also contains workshop files.  
To do this we use the same flow of commands as above, but this time we use them for the `serverfiles/steam_cache/content` folder instead of `serverfiles/garrysmod/cache`.

```bash
# Create new cache folder
mkdir /home/gmod_content
# Copy the workshop files of the first server into the new cache
cp -r /home/fun1/serverfiles/steam_cache/content/* /home/gmod_content
# Copy the second server's files too
cp -r /home/fun2/serverfiles/steam_cache/content/* /home/gmod_content
# Now remove the old cache folder of the single servers
# WARNING: The next 2 commands delete stuff, use with caution!
rm -fr /home/fun1/serverfiles/steam_cache/content
rm -fr /home/fun2/serverfiles/steam_cache/content
# Now login as the 2 users and create the new symlinks
su - fun1
cd serverfiles/steam_cache
ln -s /home/gmod_content/ content
exit
su - fun2
cd serverfiles/steam_cache
ln -s /home/gmod_content/ content
exit
# Lastly set the permissions for the central cache again
chown -R root:root /home/gmod_content
chmod -R ugo+rwx /home/gmod_content
```

# Security concerns

The above solution is viable for gmodservers who all run on a single linux server and belong to a single "owner".  
If your linux server hosts gmodserver or gameserver for different projects (projects who should not interfere with each other) then you have to change the permissions of the above solution.

The problem with the solution above: Every user on the system could write/change/delete the gmod workshop files. This is not a problem if all your servers belong to you, but if others use the linux server too then we have to prevent unauthorized access.

In the above solution we create 2 "world-writeable" cache directories.  
To make them more secure we change the permission by creating a new "gmodcache" group that handles the permissions for writing.  
Execute the following commands as root:

```bash
# Create the new group
addgroup gmodcache
# Add the users fun1 and fun2 to this group
usermod -aG gmodache fun1
usermod -aG gmodache fun2
# Change the permission of the central cache directories to the group
chown -R root:gmodcache /home/gmod_cache
chown -R root:gmodcache /home/gmod_content
```

Now only the allowed users can access the central gmod cache, which should make it safe from unauthorized tampering.
