# Protect database

Database security is a big concern for everyone.

The main threat for this is called:


## SQL Injections

SQL Injections are not as common as before anymore because they are the easiest to detect and fix.  

An SQL Injection is a way for a user to write, read or delete your database without having the permission to do so.  
This is done by injecting code into SQL statements. This happens if user input (e.g. a username) is simply inserted in an SQL statement without escaping / cleaning it. (An SQL statement is, simply said, text that tells the database what to do.)

Let's create an example to show an SQL Injection and how easy it is to fix it. The following is a function that updates the players character name in the database:  
Warning: This is a **BAD** example on how to do it!

```lua
[...]
sql.Query("UPDATE darkrp_player SET rpname = '"..name.."' WHERE uid = '"..ply:SteamID64().."'")
[...]
```

This function runs an SQL Query that updates the database with the new name of the player. The uid for the player is his SteamID 64, which only consists of numbers.

The above example has one major flaw: It saves the username without any escaping or cleaning. This means that, whatever the username of the player is, simply gets inserted 1:1 into the above SQL statement.  
An example SQL Injection for this would be:

```sql
a'); DROP TABLE darkrp_player; --
```

The "DROP TABLE" SQL statement deletes everything inside and the table itself. A table in an SQL database is basically an Microsoft Excel Spreadsheet, containing rows and columns which, in this case, contains various data about the players on a darkrp server.

Using this SQL Injection (by setting it as your name) would turn this:

```sql
UPDATE darkrp_player SET rpname = 'myself' WHERE uid = '123'
```

into this:

```sql
UPDATE darkrp_player SET rpname = 'a'); DROP TABLE darkrp_player; --' WHERE uid = '123'
```

You can see by the code highlighting: The last statement was commented out and another one was inserted instead: A `DROP TABLE` command, which deletes all information of the players. The drop table command is neither greyed out (=commented) nor is it red (=a string, taken literally), which means it is executed by the database.

&nbsp;

To secure your SQL code from such Injections you only have to do one thing: Use the function `sql.SQLStr`.  

Instead of writing your SQL code with self-inserted simple apostrophes (`'`) you should use the function `sql.SQLStr` to insert them for you.  
This function escapes any single apostrophes and "cleans" them by placing another one behind it.  
An example:

```lua
print(sql.SQLStr("test")) -- 'test'
print(sql.SQLStr("test'")) -- 'test'''
print(sql.SQLStr("test''")) -- 'test'''''
```

This means that you can not use a `'` to inject SQL.  
With this the above code for updating a playername would turn into this:

```lua
[...]
sql.Query("UPDATE darkrp_player SET rpname = "..sql.SQLStr(name).." WHERE uid = '"..ply:SteamID64().."'")
[...]
```

This would then make the SQL Injection look like the following SQL statement, which is completely fine and does not delete anything:

```sql
UPDATE darkrp_player SET rpname = 'a''); DROP TABLE darkrp_player; --' WHERE uid = '123'
```

As you can see by the code highlighting, the single apostrophe was cancelled out with another one and the whole SQL injection was saved as the playername. In comparison to above, the drop table command is red which means it is taken as a literal string and saved like this into the database without deleting anything.


## Summary

Always use `sql.SQLStr` on user input in SQL statements.  
There is currently no known way to circumvent this and thus makes it impossible to use SQL Injection.
