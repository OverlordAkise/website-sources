# Server Backups

Backing up a gmod server is really easy and the resulting backup is really small if you know what you need.

The worst thing you could do is: Backing up the whole GarrysMod folder.  
It contains a lot of big, not-needed files and the workshop cache. You don't need to back that up.

To correctly do a backup think about what you changed and what needs to be saved.  
The most important folders are (in relation to the parent folder of garrysmod):

 - garrysmod/addons
 - garrysmod/cfg
 - garrysmod/data
 - garrysmod/gamemodes
 - garrysmod/lua
 - garrysmod/maps
 - garrysmod/sv.db
 - lgsm folder OR start.sh file if not linuxgsm based

These are the only important folders that are changed on a server. You don't need to backup anything else.  

*WARNING*: The following command expects you to have a LinuxGSM installed gmodserver.

To back it all up you can use the following command:

```bash
cd && zip -r9 bak_gmodserver_$(date +\%Y\%m\%d\%H\%M).zip lgsm/config-lgsm/gmodserver/ serverfiles/garrysmod/addons/ serverfiles/garrysmod/cfg/ serverfiles/garrysmod/data/ serverfiles/garrysmod/gamemodes/ serverfiles/garrysmod/lua/ serverfiles/garrysmod/sv.db
```

The resulting backup is most of the time smaller than 1GB. If you *stupidly* zip the whole garrysmod folder the backup size is over 10GB most of the time.

You should create automatic backups, either of your whole server or atleast of your sv.db / data folder / mysql database.
This can easily be done with cronjobs.

These backups should be created as the root user, because - as previously stated in the linux setup section - the root user should only be available to the owner.
This way nobody can delete or modify these backups.


## automatic full backups

To automatically backup your server you can use cronjobs.

This example expects:

 - a user named `drp` with a home folder in `/home/drp`
 - a linuxgsm installed gmodserver in that home folder
 - you are logged in as root and execute `crontab -e` as root

First create your backup folder with

    mkdir /root/drp_backups

Then edit your cronjobs with the command

    crontab -e

If asked what editor to use select "nano".

Enter the following line at the bottom to automatically backup your server everyday at 5:02am :

```bash
02 5 * * * cd /home/drp/ && zip -r9 /root/drp_backups/bak_gmodserver_$(date +\%Y\%m\%d\%H\%M).zip lgsm/config-lgsm/gmodserver/ serverfiles/garrysmod/addons/ serverfiles/garrysmod/cfg/ serverfiles/garrysmod/data/ serverfiles/garrysmod/gamemodes/ serverfiles/garrysmod/lua/ serverfiles/garrysmod/sv.db
```

To automatically remove old backups you can also enter the following line aswell, which will delete backups older than 7 days everyday at 6:02am :

```bash
02 6 * * * find /root/drp_backups/* -mtime +7 -exec rm {} \;
```

## automatic data-only backups

If your server doesn't change much between updates then it's a good idea to only backup the player data regularly and a complete backup every update.

Player data includes:

 - data folder
 - sv.db (sqlite database)
 - mysql database (if applicable)

This would mean you could store backups for longer than 7 days because they are WAY smaller, changing the above cronjobs to the following:

    02 5 * * * cd /home/drp/ && zip -r9 /root/drp_backups/bak_gmodserver_$(date +\%Y\%m\%d\%H\%M).zip serverfiles/garrysmod/sv.db serverfiles/garrysmod/data
    02 6 * * * find /root/drp_backups/* -mtime +21 -exec rm {} \;


## backup mysql

To backup a mysql database you can use the `mysqldump` command.

An example: To backup a local mysql (or mariadb) database you can use the following command as root user

```bash
mysqldump -u root --all-databases > bak_mysql_20220721.sql
```

If the file is larger than expected you can also compress the backup on-the-fly, with for example gzip:

```bash
mysqldump -u root --all-databases | gzip > bak_mysql_20220721.sql.gz
```

To now automatically backup your database you can use the following cronjob entry

    02 6 * * * mysqldump -u root --all-databases | gzip > /root/drp_backups/bak_mysql_$(date +\%Y\%m\%d\%H\%M).sql.gz
