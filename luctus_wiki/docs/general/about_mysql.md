# About MySQL

First and foremost: **MySQL is not better!**

Many people simply say "you have to get mysql for good performance" or similar stuff, but that is not true!  
MySQL is actually slower.  
This page exists to better explain this and why the inbuilt sqlite3 database is more than enough most of the time.


## What is SQLite

Garry's Mod has - by default - an inbuilt sqlite database. This database is located at <span>garrysmod/sv.db</span> and uses sqlite3 with in-memory temp-files.  
This database is quite fast and easy to use in LUA, but there is also an alternative to sqlite: MySQL.

With extra modules like mysqloo or tmysql4 you can have MySQL support in Garry's Mod. These modules are not provided by the game and need to be installed manually. Not every addon provides support for MySQL natively, which means that some addons will still use the local sv.db instead of MySQL even if mysqloo is installed.


## MySQL test setup

All these tests have been made on a 16tick DarkRP server. The MySQL database is a MariaDB which is hosted on the same server as the Garry's Mod server. The mysql module used is [https://github.com/FredyH/MySQLOO](https://github.com/FredyH/MySQLOO). The lua implementation for mysqloo used is DarkRP's MySQLite.  
All times are measured in seconds. The time is measured from first wanting the data until we can use the data.

The code for testing:

```lua
--SQLite Code
local liteTime = SysTime()
local res = sql.QueryValue("SQL_STATEMENT")
if res == false then
  print("SQLITE ERROR DURING SELECT!")
  print(sql.LastError())
end
if res then
  local endTime = SysTime()
  print("SQLite Read Response: "..res)
  print("SQLite Read Time: "..(endTime-liteTime))
else
  print("SQLITE NO RESPONSE")
end


--DarkRP's MySQL Code
local looTime = SysTime()
res = MySQLite.queryValue("SQL_STATEMENT",function(data)
  local endTime = SysTime()
  print("DRPMySQL Read Response: "..data)
  print("DRPMySQL Read Time: "..(endTime-looTime))
end, function(data)
  print("DRPMySQL ERROR DURING SELECT!")
end)
```

## MySQL test result

First we test the delay of the database system itself. We can do this by querying `SELECT 1+1` which doesn't need to get any data from the database itself.  
Result:

    -- SELECT 1+1
    SQLite   ResponseTime:   0.000027909001801163s
    DRPMySQL ResponseTime:   0.020215623022523s

Next up is a simple SELECT test for a specific row with `SELECT rpname FROM darkrp_player WHERE uid = 76561198071737444`.  
Result:

    -- SELECT rpname FROM darkrp_player WHERE uid = 76561198071737444
    SQLite   ReadTime:   0.00016302999938489s
    DRPMySQL ReadTime:   0.048014674001024s

Last for the simple tests is an INSERT with `INSERT INTO darkrp_player VALUES(123123,'Test User',1,2)`.  
Result:
    
    -- INSERT INTO darkrp_player VALUES(123123,'Test User',1,2)
    SQLite   WriteTime:   0.00020395400133566s
    DRPMySQL WriteTime:   0.06270497100013s


The final test was supposed to be a mass INSERT of 1000 rows.  
The DarkRP implementation of mysql is bad though and only does simple inserts one after each other instead of actually doing what sqlite does: Use transactions. In sqlite you can use `sql.Begin()` and `sql.Commit()` to create and finish a transaction. A transaction is an atomic operation, which, instead of only a single row, can insert thousands of rows instantly at once.  
But because DarkRP's MySQLite doesn't support this for MySQL databases this test is lost for mysql.  
The calculated time for randomly inserting 1000 user-rows would be (if using sqlite transactions):

    SQlite   1000 Inserts:   0.004849937000472s
    DRPMySQL 1000 Inserts:  ~30s



## Conclusion

As you can see above with the test results:  

*DarkRP's MySQL is not faster than Garry's Mod's inbuilt SQLite3 database.*

Also, you shouldn't use DarkRP's MySQL Transactions as it's really slow.

Simply adding MySQL doesn't fully synchronize 2 servers. You need to either rewrite addons and change those that use the data folder to use MySQL or find another way to synchronize that.

A final summary:

Advantages of using MySQL:

 - Size doesn't become a problem, as MySQL scales better with millions of records
 - You can synchronize the database between multiple garry's mod servers
 - You can access data more easily from a webserver

Disadvantages of using MySQL:

 - It is slower than SQLite3 because of network usage and network latency
 - You need an extra module for it with custom MySQL functions
 - Depending on the implementation of the MySQL LUA functions the speed of the SQL queries can vary
 - MySQL needs extra code, configuration and maintenance (and it's an extra source for problems)
 - Security risk of leaking MySQL login informations to players via shared lua files


I recommend to only use MySQL if you need to synchronize data between multiple servers.
