# SQL Intro

This is only a brief introduction, as it is too difficult to explain everything about SQL in a single page.


## About

Garry's Mod Servers use a "SQLite3" database which is stored in a single file named `sv.db`. This file is located at `garrysmod/sv.db`.  
SQLite3 is very similar to MySQL/MariaDB, the main difference is that it is a single file that stores the whole database instead of a system of files in a folder.  
The temporary files of this database are stored in memory, so it is extremely fast.  
Each client and server has their own database file. If you run sql queries on the client it will edit the `cl.db` instead of the server (`sv.db`) one. 

To read more about SQLite in gmod you can visit the official wiki page at [https://wiki.facepunch.com/gmod/sql](https://wiki.facepunch.com/gmod/sql)


## Creating structure

SQL databases require a structure for data.  
You can imagine it as an excel spreadsheet. One `database` is one excel file. A `table` is a sheet (=tab) in excel.  
A column in excel (A,B,C,...) is a `column` in a table, same as with the rows.

In SQL you define the data structure as columns.  
The data itself is a row in these columns.  

To repeat this:

 - A database can have multiple tables, e.g. `darkrp_player` and `darkrp_wallets`
 - Each table has multiple columns which can hold different type of data, e.g. `my_bank` could have `steamid, money` (steamid is text, money a number)
 - Each "dataset" you want to store is a row which supplies data for each column in a table, e.g. to save a player's bank into the `my_bank` table you would supply the player's steamid and bank-money and insert those into the table.
 - You can edit any row you want, nothing is "fixed" and everything can be changed with sql's `UPDATE` statement, e.g. to update a player's money in the `my_bank` table you can use `UPDATE my_bank SET money=9999 WHERE steamid=STEAM_0:0:12345;`


An example structure:

```
number: steamid64
text: nickname
```

This would be "displayed" as:

```
|steamid64|nickname|
-----------------
|123894214|BigBoy42|
|534894324|Another3|
```

To now create this as an SQL table you use the following sql statement.  
An sql statement is a text that interacts with the database, usually starting with a keyword of what it does (e.g. INSERT, SELECT, UPDATE).

```sql
CREATE TABLE IF NOT EXISTS nicknames(steamid64 INT, nickname TEXT)
```

As seen above, to create a table you use `CREATE TABLE`.  
You should always use it with `IF NOT EXISTS` after it or else you will delete your database everytime you create it (=start the application).  
A number is called an `INT` in sql, which stands for integer. Text is simply called `TEXT`, and with INT those 2 will probably be the only sql datatypes you are going to need.  
To now execute this in gmod lua (executed inside the sqlite3 sv.db) you can use the following lua code:

```lua
sql.Query("CREATE TABLE IF NOT EXISTS nicknames(steamid64 INT, nickname TEXT)");
```

You use the `sql.Query` function for "querying" the database. You do this for any operation, no matter if creation, insertion or selection.


This query function can return a value. This return value tells you if something went wrong.  
If you CREATE or INSERT data it will be `false` if an error occured and `nil` if everything went well.  
If you SELECT data it will be `nil` if no data was found and `false` if an error occured, and it will be "normal data" if everything went well.

If an error happens the function `sql.LastError()` will return the error text of the sql statement.  
An "error example" you can execute in gmod:

```lua
local err = sql.Query("CREATE TABLE IF NOT EXISTS nicknames(steamid64 INT, nickname TEXT")
if err == false then
    error(sql.LastError())
end
```

If you run the above code it will print the following to your console:

```
[ERROR] lua/autorun/server/sv_test.lua:3: incomplete input
  1. error - [C]:-1
   2. unknown - lua/autorun/server/sv_test.lua:3
```

This is because a `)` is missing behind the `TEXT` in the sql statement.  
**Warning:** It is very important to always check if an error occured in your sql query before continuing!


## Inserting data

We now want to insert data into the newly created table (named "nicknames").  
You have to insert data in the same order as the table created it.  
A sql statement for inserting data:

```sql
INSERT INTO nicknames VALUES(1234567890,"peter");
```

If you only want to insert data into some columns you can write in brackets the columns you want to fill with data like this:

```sql
INSERT INTO nicknames(steamid64) VALUES(12345567890);
```

It is recommended to always insert every value or else the column will be `NULL`.  

As seen in the above examples the steamid64 is without quotation (`"`) and the nickname is. This is because the steamid is a number, and numbers don't require any quotationmarks but text (=string) does.

To execute it in gmod lua we use the `sql.Query` function again.  
We will also check if an error happened during insertion. It is good practice to always check for errors.

```
local err = sql.Query("INSERT INTO nicknames VALUES(1234567890,'peter')")
if err == false then
    error(sql.LastError())
end
```

Warning: Please look out for the quotation marks (`"`,`'`) in the above example. You can not use the same type of quotation marks for the nickname and sql string, which is why peter is in `'` quotation marks and the whole sql statement is in `"` quotation marks.


## Getting data

To get data from our table we use the sql SELECT statement.  
You can either get all columns from the table or only specific ones. You can also limit the number of returned rows.

An example of selecting everything in a table (not recommended):

```sql
SELECT * FROM nicknames;
```

If you now run this in lua with error check:

```sql
local data = sql.Query("SELECT * FROM nicknames")
if data == false then
    error(sql.LastError())
end
```

The `data` variable now holds a list of rows returned.  
The structure looks as follows:

```lua
data[1] = { steamid64=1234567890, nickname="peter"}
data[2] = { steamid64=5467897634, nickname="chris"}
data[3] = ...
```

As seen above, data is a list of rows that the sql database returned. Each row has indexes (or attributes) of the columns and the value of those is the value that was inside the database.

An simple example to print every nickname saved:

```lua
local data = sql.Query("SELECT * FROM nicknames")
if data == false then
    error(sql.LastError())
end
if not data then return end --if no data inside table then do nothing
for row,value in ipairs(data) do
    print(value.nickname,"has the steamid64",value.steamid64)
end
```


## Getting specific data

To now get the data of only one player we add a `WHERE` clause in the sql SELECT statement.

An sql example that selects only one players nickname:

```sql
SELECT * FROM nicknames WHERE steamid64=1234567890;
```

This lets you select the row of a specific player of the nicknames table.  
We get the steamid64 and the nickname from the player with the steamid `1234567890`, but in reality we only need the nickname because we know the steamid already.  
To only get the nickname we can change the `*` asterisk in the front to only the column we want:

```sql
SELECT nickname FROM nicknames WHERE steamid64=1234567890;
```

Now we have optimized it to only return the column / data we want.  
In gmod lua there is a method for only getting one value named `sql.QueryValue`. This function only returns the first value found and not a row or list of rows.

An example of using this in lua to get a player's nickname:

```lua
local nickname = sql.QueryValue("SELECT nickname FROM nicknames WHERE steamid64=1234567890")
if nickname == false then
    error(sql.LastError())
end
if not nickname then
    print("No nickname found")
    return
end
print("Nickname found:",nickname)
```

Now we do not have to loop over any rows and can directly access the value.  
If no value was found or the value was `NULL` in the database then we get a `nil` returned, which we check with `if not nickname then`.


## Getting data based on dynamic input

Now we make it dynamic by adding a player's steamid dynamically to the SQL statement.  
In reality we do not hardcode SteamIDs into SQL strings, we create those dynamically based on a player's steamid.

Imagine we have a `ply` variable and want to get it's nickname.  
We would use this (shortened) lua code:

```lua
local nickname = sql.QueryValue("SELECT nickname FROM nicknames WHERE steamid64="..ply:SteamID64())
```

Instead of writing a steamid directly into the code we append the steamid of the player, which will return his nickname from the database if it exists.  

This is ok for numbers or strict functions like `:SteamID64()`, but:  
**!ALWAYS SANITIZE USER INPUT!**

Sanitizing in this context means "making sure that user input is not malicious".  
This is quite easily done with the following, very important statement:

**Always use sql.SQLStr for string input into a database**

The `sql.SQLStr(text)` function sanitizes input strings so that they are safe to use with databases.  
An example of using this to select the steamid of a player from his nickname:

```lua
local steamid64 = sql.QueryValue("SELECT steamid64 FROM nicknames WHERE nickname="..sql.SQLStr(ply:Nick()))
```

As you can see we use the `sql.SQLStr` function above to insert the player nickname into the sql statement.  
Never insert a string/text without using sql.SQLStr into an sql statement!


## Example: Saving points

Let's create an example code that saves a player's "points" over gameplay sessions.  
For this we save the points inside the sql database and load them when a player joins.

To create a table in the database I recommend to create it after a while, personally I use the InitPostEntity hook to do this.

```lua
hook.Add("InitPostEntity","example_points",function()
    local res = sql.Query("CREATE TABLE IF NOT EXISTS player_points(steamid TEXT, points INT)")
    if res == false then
        ErrorNoHaltWithStack(sql.LastError())
    end
end)
```

The above code creates the table for our points data when the server starts.  
We check for errors while executing this statement with `if res == false then`. If an error occured we use `ErrorNoHaltWithStack()` to print an error to console without actually stopping execution. We get the error of the sql execution with `sql.LastError()`.


Next we add the functions for loading a player's points.

```lua
function LoadPlayerPoints(ply)
    --Default:
    ply:SetNW2Int("points",0)
    local points = sql.QueryValue("SELECT points FROM player_points WHERE steamid="..sql.SQLStr(ply:SteamID()))
    if points == false then
        ErrorNoHaltWithStack(sql.LastError())
        return
    end
    if not points then
        local res = sql.Query("INSERT INTO player_points(steamid,points) VALUES("..sql.SQLStr(ply:SteamID())..",0)")
        if res == false then
            ErrorNoHaltWithStack(sql.LastError())
        end
        return
    end
    ply:SetNW2Int("points",tonumber(points))
end

hook.Add("PlayerInitialSpawn","example_points",LoadPlayerPoints)
```

The above code calls the `LoadPlayerPoints` function for every player that joins the server. This function sets the points for a player to 0 and then tries to load their actual points from the database.  
If the database returns something without creating errors we set the player's points to that.  
If no value and no error was returned then we create the value in the database table. We do this because we only want to update this value later on with the players new points.

The last code is to add points to a player and save those points.

```lua
function AddPlayerPoints(ply,amount)
    ply:SetNW2Int("points",ply:GetNW2Int("points")+amount)
    SavePlayerPoints(ply)
end

function SavePlayerPoints(ply)
    local res = sql.Query("UPDATE player_points SET points="..ply:GetNW2Int("points").." WHERE steamid="..sql.SQLStr(ply:SteamID()))
    if res == false then
        ErrorNoHaltWithStack(sql.LastError())
    end
end
```

The above code is saving a players points everytime he gains points. This is not a perfect solution for every situation, because if many people get many small points in a short amount of time then it is putting a lot of strain on the database.  
A different approach would be saving a players points in regular intervals and if a player disconnects. This way you have less database utilization, which prevents possible lags.


# Performance

There are a few important performance aspects when using gmod's sqlite database.

**Don't write long running statements**  
If an sql statement runs for a long time then the whole gmod server will be unresponsive until that statement finished.  
This means that players will see a red "Disconnect" message in the top right when a statement takes too long to run.

Make sure to not calculate large statistics or select a lot of data at once during gameplay.  
If you have to calculate statistics ingame (e.g. leaderboards) do those when e.g. starting the server or during the night when not many players are online.

**Never SELECT all data from a table**  
If you do `SELECT * FROM mytable` then the lua code will load the whole table into a variable, which takes time.  
During this time the server is unresponsive until all of the data is loaded.  
This is only acceptible for not-large tables when starting the server, e.g. for loading configuration from the database.

