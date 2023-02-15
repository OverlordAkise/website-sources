# Database and Storage

In gmod there are 3 main ways of storing information about players:

 - SQLite database (sql, the gmod internal db, in `garrysmod/sv.db`)
 - data folder (file based, in `garrysmod/data folder`)
 - MySQL database (sql, `external database`, via mysqloo in garrysmod/lua/bin/gmsv_mysqloo.dll)

Recommendation: SQLite

## SQLite

This is the fastest way to save information.  
The database is available in vanilla gmod and doesn't need extra configuration. It is saved in RAM and is thus way faster than the data folder or a mysql database.  
For example: DarkRP uses, by default, only SQLite to save data.  

The only downside of sqlite: It uses table locking. This means that it is worse in performance compared to mysql if you perform a ton (100+) writes at once.  
But it is very uncommon to have a large amount of concurrent writes that make SQLite slow.  
If you really need to write a lot of data in singular queries use sql.Begin and sql.Commit to fasten up queries via transactions. This makes sqlite able to perform similar to mysql in terms of performance.

Example of using sqlite:

```lua
local res = sql.Query("SELECT * FROM darkrp_players")
PrintTable(res)
```

I personally recommend using sqlite for storing any and all data that could change often.  
If you only need to save it rarely or if the data only changes if edited by admins then you could use the `garrysmod/data` folder to store it aswell.


## Data folder

In garrysmod you can only write to files in the `garrysmod/data` folder.  

Writing to files on a filesystem is way slower than inserting data into the RAM-loaded sqlite database.  
The data folder is fine for rarely changing data but I wouldn't recommend it for player data.

You can only save specific filetypes, e.g. .json, .txt, .dat, .png, .mp3. This means you can *NOT* save an .exe file into a players data folder.

To write a table or any information to a file I strongly recommend using JSON as the data format.  
You can easily format a table to JSON and back with `util.TableToJSON` and `util.JSONToTable`.

Example:
```lua
--write text to a file:
file.Write("motd.txt","Enjoy and have fun!")
--read it back:
serverMOTD = file.Read("motd.txt","DATA")
```

## MySQL

Warning: To be able to use mysql databases with gmod you need an external library like mysqloo. Mysql is NOT a default storage solution in gmod!  

MySQL is good for connecting a website with your server (e.g. donation system that gives a user ingame a custom rank after he bought it from the website).  
MySQL is also good for connecting the data of multiple servers onto a single database.

DarkRP has a custom, so called "MySQLite" library that lets you use a single function for both sqlite and mysql (if configured).  
It is recommended to do it this way, because it abstracts the data storage behind it via a single function, which makes it easier to maintain.

BUT: **MySQL is NOT faster than the default SQLite database.**

I would only recommend MySQL if you have a donation system that needs it or if you have multiple servers that are the same and should thus use the same database.  
I would also recommend to install the mysql server on the same machine as the gmod server. If not the network delay could make it even slower than it already is compared to sqlite.

For a more thorough comparison between MySQL and SQLite in GarrysMod, with benchmarks, visit [https://github.com/OverlordAkise/gmod-lua-performance#mysql-vs-sqlite](https://github.com/OverlordAkise/gmod-lua-performance#mysql-vs-sqlite)
