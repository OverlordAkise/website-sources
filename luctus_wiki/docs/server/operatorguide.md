# Server Operator Guide

This page serves as a quick showcase on how to maintain and work with your gmod server on a linux machine.

## Commands

This list assumes you have a LinuxGSM gmodserver installed.

```bash
# Restart Server
~/gmodserver r
# Open logfile
less ~/log/console/gmodserver-console.log
# See live log of server without opening gmodserver console
tail -f ~/log/console/gmodserver-console.log
# Search through all lua files for a specific word
grep -rin "MyVariable" --include="*.lua"
# Open server console
~/gmodserver c
```

If you have installed Luctus' short commands you can also use the abbreviated versions (like only typing `r` to restart the server).

## Troubleshooting

If the server doesn't start or is not joinable check the following:

 - Is the server running? (E.g. via `htop` command)
 - Did the `~/gmodserver start` command give you errors?
 - Check the logs (E.g. is the map not found? Did the server crash on startup?)
 - Is the IP configuration in lgsm wrong?
 - Is there a firewall blocking access? (E.g. `iptables -S` as root on linux)
 - Are the permissions correctly set? (Should throw error on startup, if yes run `chown -R`)

