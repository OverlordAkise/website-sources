# Intro into gLUA

First we create your first test addon to execute code.  
Close your game and create an addon folder named `testing`. Inside that create a `lua` folder, inside that `autorun` and inside that `server`. Inside that folder create a file named `sv_testing.lua`. Inside this lua file write `print("loaded")`. Without any code in a lua file (filesize = 0 bytes) it won't be loaded.
The full path to the file should look like this: `garrysmod/addons/testing/lua/autorun/server/sv_testing.lua`

Now you can start your Garry's Mod game locally with "Start a new game" and it should print out `loaded` in the console somewhere.  
The text is blue if it has been printed by the server and orange if client. Because we put our script into `autorun/server` we should see the loaded line in blue.


## Variables

Everything in LUA is a variable. A variable is like the `x` you learned in math class where for example `1*x = 2`.  
In this example x would be a `number`, also called `int` or `Integer`.  

To assign a variable you can ues the equal sign `=` like this:

```lua
age = 50
```

`age` is now a variable that holds the value/number `50`.  
If you now want to print your age you can use the following function:

```lua
print(age)
```

`print` is a function that simply puts text into your console. It can take any value and simply prints its textual form.

If you now want to assign your name to a variable you have to use quotation marks `"` like this:

```lua
name = "Chris"
```

Whenever you want to literally use the text you write you have to surround it with `"`. These variables are called `string`.  
A string is a "string of characters" aka. text.  

You can print it with `print(name)`, same as with age.  
The `print` function will print any value divided by `,` , for example `print(name,age)` will print your name and age divided by a tab (`\t`).

If you want to "put text together" you use two dots (`..`) like this:

```lua
print("Hello, my name is "..name)
```

(Mind the space that is after `is `)  
This will print out your name in a nice format. You can also do this with numbers but not with any other datatype.  
If you want to add 2 numbers together you would use `+`, like `print(3+3)` would print out `6`.


To "comment out" or simply "comment" a piece of code you can use `--`. If you put `--` infront of a line of code the engine will ignore that line.  
To ignore multiple lines you use `--[[` on the first line and `--]]` on the last one.  
Example:

```lua
-- I am a comment and am not executed!
-- print("Hi!")

--[[
I am also a comment!
print("Hello!")
--]]
```


The `print` function is a `function`. A function is a datatype beside string and int.  
Every function is also a variable.  
You can print the print function's memory address like this:

```lua
print(print)

-- output:
-- function: builtin#25
```

If you want to execute a variable (function) you have to use normal brackets `()`.  
In the print example above we printed the `print` function as a value. If we want to call it (=execute the function) we have to append `()` to it.

Lets create an example function:

```lua
function GetName()
    return "Chris"
end

-- This only prints the function address:
print(GetName)

-- This executes the function and prints the returned name:
print(GetName())
```

Every function can return values. For example the `print` function doesn't return any value, but our `GetName()` function does.  
For example if you call `PrintTable(player.GetAll())` it calls the `player.GetAll()` function and replaces itself with the result. This would result in `PrintTable(ALL_PLAYERS)` (not real code). Now the PrintTable function executes and prints out all the players one by one.

Each chain of functions executes from inside to outside.  
These 2 codeblocks do exactly the same:

```lua
-- Print directly
PrintTable(player.GetAll())

-- Save in variable and then print
AllPlayers = player.GetAll()
PrintTable(AllPlayers)
```

Notice how we didnt use `AllPlayers()` in the second example, because AllPlayers is a variable (the result from a function) and not a function itself.

These functions above have an empty bracket `()` behind it. This means we give no additional information (=variables) to these functions.  
With the `print` function we put things inside the brackets. These are called `arguments` or `parameters`.  
An example:

```lua
# argument 1: the string "ok"
# argument 2: the number 3

print("ok",3)
```

They are split up with a simple comma `,` and a function can have infinite arguments.  
For example we can create a function that takes 2 number arguments and returns the multiplied between them like this:

```lua
function Multiply(first,second)
    return first*second
end

print(Multiply(3,2))
```

The above example would print out `6` as we provided `3` and `2` as the arguments to the `Multiply` function.




---

The most important datatype besides `string` and `int` are `tables`.  
A table is a "list" or "key-value map". It can hold many strings or numbers.

For example, to print out all players currently on the server you can use the `PrintTable` function with the `player.GetAll()` function like so:

```lua
PrintTable(player.GetAll())
```

The `player.GetAll()` function returns a list (=table) of all players currently on the server.  
By default all lists are ordered, which means the first player is #1, the second player #2, etc..  

Every table (=list or map) has a key and a value.  
For a list the key is the place on the list and the value what is inside that place.  
Example:

```
Key | Value
1   | Player [1] Chris
2   | Player [2] Bot01
```

The above table is a list with 2 entries and both are players.  
To access these players one by one you can "index" the list.  

The `index` of a table is the Key. It can be any value, even a function.  
If you want to get the value of a specific key you can do the following:

```lua
-- Save players to variable
mylist = player.GetAll()

-- Print out the first player
print(mylist[1])
```

The above example uses square brackets `[]` to "index" a table and get the value.  
You can also do it directly on the result without saving it into a variable:

```lua
print(player.GetAll()[1])
```

This first gets all players in a list and then selects the first (index=1) from that list and prints it.

Lets create a table with `string` indexes. To initialize (=create) a table you use curly brackets `{}` like this:

```lua
MyTable = {}
```

There are 2 ways to initialize a table: Either directly or later via indexes.  
To show both methods:

```lua
-- Method 1, directly
MyTable = {
    ["Name"] = "Chris",
    ["Age"] = 50,
}

-- Method 2, afterwards
MyTable = {}
MyTable.Name = "Chris"
MyTable.Age = 50

-- Same as Method 2
MyTable = {}
MyTable["Name"] = "Chris"
MyTable["Age"] = 50
```

Please notice the comma `,` in the first method, they are important.  
I recommend to use Method 1 whenever possible, but there is not much difference between them.

With Method 2 we can see both ways to index a table: Either with a dot `.` or with square brackets `[]`.  
With square brackets you can use variables to dynamically get values out of a table.  
Example:

```lua
value1 = "Name"

print(MyTable[value1])
```

This would print `Chris`. You can NOT use `MyTable.value1` here, you have to use square brackets!

You can also create tables inside tables. Example:

```
Citizens = {
    ["#1"] = {
        ["Name"] = "Chris",
        ["Age"] = 50,
    },
    ["#2"] = {
        ["Name"] = "Luke",
        ["Age"] = 51,
    },
}
```

Please take notice of the commas `,`, without them it will create syntax errors.

For some "real life" examples: `player` is a table of multiple functions, and `player.GetAll()` is one of them.  
Same goes with `math` and `math.random()`, which is used to generate random numbers.  
You could also call it like `math["random"]`, but nobody does that with predefined functions as it looks ugly.  
These "indexes" of a table are also called `attributes` or `fields` if it is not a function. Example: `huge` is an attribute of `math` (variable = `math.huge`) which contains an insanely large number that is always larger than any other number.  
If it is a function it is called `method`. Example being `math.random()`, where `random` is a method of `math`.



Other datatypes besides string, int and tables are `userdata` (=player) and for example `Vector` or `Color`.  
These types are also called `object`. (And the "class" of an object defines how it works)  

To get a player for our examples we can simply get the first player returned by `player.GetAll()` like this:

```lua
ply = player.GetAll()
```

It is good practice to name your player variables `ply`, so that you easily know which variable is a player object.  

A player has many methods. These functions change the player, like for example `ply:Kill()`. These functions are called `methods` and use a colon `:` instead of a simple dot.  

The colon basically means "execute this function ON this object". In our `ply:Kill()` example it means: Kill this specific player.  
Behind the scenes it works by putting the player as the first input to the function, example:

```lua
# These 2 lines do the same thing:
ply:Kill()
ply.Kill(ply)
```

(If you read such `method` code you will see `self` a lot. In this case self means "the object this method has been called upon".)
