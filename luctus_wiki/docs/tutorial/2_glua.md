# Intro into gLUA

First we create your first test addon to execute code.  
Close your game and create an addon folder named `testing`. Inside that create a `lua` folder, inside that `autorun` and inside that `server`. Inside that folder create a file named `sv_testing.lua`. Inside this lua file write `print("loaded")`. Without any code in a lua file (filesize = 0 bytes) it won't be loaded.
The full path to the file should look like this: `garrysmod/addons/testing/lua/autorun/server/sv_testing.lua`

Now you can start your Garry's Mod game locally with "Start a new game" and it should print out `loaded` in the console somewhere.  
The text is blue if it has been printed by the server and orange if client. Because we put our script into `autorun/server` we should see the loaded line in blue.


# General

LUA does not use semicolons `;` at the end of lines. You can use them but they are not needed.  
Functions start after the closing bracket `)` of the name definition and end with the keyword `end`.  
Every logical codeblock stops with an `end`, there are no curly brackets.
You do not have to use brackets around if's (`if a then a() end` is the same as `if (a) then a() end`).



# Variables

Everything in LUA is a variable. A variable is like the `x` you learned in math class where for example `1*x = 2`.  
In this example x would be a `number`, also called `int` or `Integer`.  

To assign a variable you can ues the equal sign `=` like this:

```lua
age = 50
```

`age` is now a variable that holds the value/number `50`.  
We call this "declaration", as in "we declare the variable".  
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


# Validity and code blocks

Lua docs page for this topic: [https://www.lua.org/manual/5.3/manual.html#3.5](https://www.lua.org/manual/5.3/manual.html#3.5)

Not every variable is always "valid" (=exists).  
Some variables are only valid for "this execution" and others are only valid in a function.  
An example:

```lua
function Greet(name)
    print("Hi, my name is "..name)
end

Greet("Chris")

print(name)
```
In the above example the variable `name` only exists inside the function.  
We get an error in the `print(name)` line because the variable `name` is not set.

If a variable deosn't have a value it has the value `nil`. In the above example, the variable `name` in the last row has the value `nil`, meaning "nothing" or "empty".  
Important note: An empty string (e.g. `name = ""`) is NOT the same as an empty variable like `age = nil`. One is of datatype string, the other is "nil".

In the above example we also see the "area of validity" for the variable name.  
The code "indentation" (=how many spaces prepend a line) is showing the so called "visibility" of the variable.  
In the above example we have the function `Greet` which has only one line of code inside it. This line of code is put 4 spaces to the right, which indicates a "code block". Inside this code block we have one new variable declared: `name`, which is provided by the function call `Greet("Chris")`. This variable is only valid inside this codeblock! (=inside the indented codelines)

By default every variable you create is global. Example:

```lua
function Greet()
    print(name)
end

name = "Chris"

Greet()
```

This code example would print out `Chris`. The variable `name` is globally accessable (=global) and can be used in other lua files too.  
**This is considered bad!**

To create a variable that is only valid in this local file (or function/codeblock) we use the `local` word infront of the declaration.

An example of creating a local variable:

```lua
local WindowHeight = 900
```

If this is inside a lua file and not in a function it will be local to the lua file. If you put this statement in a function it will be local to that function only.

An example:

```lua

function Greet()
    local name = "Chris"
    print("Hi, "..name)
end

Greet()
-- Prints out Chris

print(name)
-- Prints nothing as name is nil
```

In the above example we can see: The `name` variable is only valid inside the `Greet()` function and can not be used or accessed outside of it.

This is called "scoping" or "the scope of a variable".  

The scope of variables is always local to the function being executed.  
An example:

```lua
local name = "Chris"

function Greet()
    print(name)
end


-- In another lua file:

function CallGreet()
    Greet()
end

CallGreet()
-- ^ This will print out "Chris"
```

In the above example we can see that even though we call another function in another lua file we can still access the local variable. This is because we use this variable in a function that is in the same file.

---

To verify if a variable is "valid" (which means "not nil") we can either check with `not` or with the `IsValid` function.  

To check if a variable has a value we can use the following code:

```
local name = "Chris"

if not name then
    print("ERROR: No name set!")
end

if not age then
    print("ERROR: No age set!")
end
```

If you execute the code above you should see only the `No age set!` error message.  
With `not` you invert the value behind it. You invert it to a "boolean" value.

Gmod wiki boolean page: [https://wiki.facepunch.com/gmod/boolean](https://wiki.facepunch.com/gmod/boolean)

The boolean (or `bool` for short) datatype is a simple 2-value-only datatype: It can either be `true` or `false`. Booleans are mainly used for comparisons.

The `not` prefix turns a value to its inverted boolean value.  
If a variable is `nil` it will be `false`. If a variable is not nil it will be `true`. An example:

```lua

local name = "Chris"

if not name then
--^this translates to:
if not "Chris" then
if not true then
if false then
--^This will not execute the if block

print(not name) --will print `false`
print(not mariasAge) --will print `true`, because variable "mariasAge" doesn't exist
```

But what if a variable exists but is not "valid"?  
This can happen if, for example, you do the following:

```lua
--Invite a bot to your server with the console command "bot"
RunConsoleCommand("bot")
--Save the player object in a variable
myplayer = player.GetAll()[2]
--Kick the bot again
player.GetAll()[2]:Kick()
```

After this above code example you have an "invalid" player in the `myplayer` variable. It is a player that has already left the server, which means you can't spawn it or use it for anything else. The `not` prefix will still return true, because the variable `myplayer` is not nil but a `nil player object`.  

To check if a player is Valid you use the `IsValid()` function.  
An example:

```lua
-- execute the above code example

if not IsValid(myplayer) then
    print("ERROR: player is not valid!")
end
```

This way you can verify the object variables before using them.  
IsValid does not work with the basic datatypes that are not objects (boolean, int, string, table) but works with objects (vector,angle,color,player,entity).

If you use the `player.GetAll()` function you do not have to verify every player with `IsValid`. This function will already return only valid players and they are valid in the current execution context.  
If you wait a second and use a player again (e.g. after a timer) you have to check the player object with IsValid.  

An example: If you want to respawn a player 2 seconds after he died then you have to use `IsValid` in the `timer`, because what if the player left in those 2 seconds? You would try to spawn an invalid player and get a lua error.


# Loops

Sometimes you want to go over all thing inside a list. This can be accomplished with the `pair` function.  

An example:

```lua
for key,value in pairs(player.GetAll()) do
    print(key,value)
end

--This would print out:
--1     Player [1] Chris
```

The example above will print the index/key/place-in-the-list and playerobject of each player currently on the server.  

The `pair` function returns the key and value of the table provided in a loop and executes the codeblock for each entry.  
This means the `key` and `value` variables get a different variable value each run. With 20 players the `print` function inside will get executed 20 times, once for each player.  
To now, for example, kill all players we can do the following:

```lua
for k,ply in pairs(player.GetAll()) do
    ply:Kill()
end
```

In the above example we go through all the players and kill them.  
We renamed the `value` variable to `ply` to better show what the variable is.  
This is only done for readability, you could name your variables whatever you want.




# Code format

Formatting your code correctly is very important for readability and maintainability.

Always indent codeblocks. Use 4 spaces and watch out for correct usage.  
A **bad** example:

```lua
--this is a bad example!

function killall()
for k,v in pairs(player.GetAll()) do
v:Kill()
end
end
```

The above example may work but looks very unreadable.  
The good example would be:

```lua
--this is better!

function KillAllPlayers()
    for k,ply in pairs(player.GetAll()) do
        ply:Kill()
    end
    -- variable `ply` is not valid here anymore, easily visible now
end
```

Lets go over the above example:

 - We renamed the function. It is important to name your functions after what they do. `KillAll` doesnt say what it kills exactly, but `KillAllPlayers` is instantly understandable.
 - We indented the codeblocks with 4 spaces. This is now more readable and also easier to understand. Now we also see that the `ply` variable is only valid in the middle part of the function where 2 indents (=8 spaces) are.
 - We renamed the `v` variable to `ply`, which instantly shows what this variable is about. This helps with readability and ease of programming, because now you don't have to wonder what the `v` stands for in this context.


Another important thing with code format would be: Naming your global functions and variables uniquely.  
Please do NOT cut your variable names short and name them things like `nd = 300` (for "NLR Duration"). Variable names should be self explanatory, for example `LUCTUS_MOTD_COMMAND` doesn't need a comment explaining what it does because the variable does that job for us.

In LUA variables overwrite each other if they have the exact name. Example:

```lua
name = "Chris"
function ChangeName()
    name = "Luke"
end

print(name)
ChangeName()
print(name)
```

The above code example will first print out Chris and then Luke.  
Imagine that your addon and another addon have the same config variable named `CONFIG_HEALTH`. This means that the second addon could overwrite your own variable.

It is important to name your variables and functions uniquely.  
This is not important if you have local variables for e.g. colors. Example: 

```lua
name = "Chris"
function Greet()
    local name = "Luke"
    print("Hi, "..name)
end

print(name)
Greet()
print(name)
```

In the above example the local `name` variable in the `Greet()` function doesn't change the one outside of it.  
This means you can name your local variables short and sweet without worrying of overwriting anything.

A good way to handle this is to prefix your variables with your "developer name" and addon name. This way nothing should collide, even if 2 developers create an NLR addon or one developer creates many addons.  
Example:

```lua
LUCTUS_NLR_DURATION = 300
LUCTUS_NLR_TEXT = "Stay out of your NLR!"

function LuctusNlrReset(ply)
    --[[ code here ]]--
end
```

This way you can install 2 NLR addons and nothing would be overriden or buggy.

In the above example my global configuration variables are all upper case.  
This is because of other languages doing similar things: Constants (=variables who don't change) should be all uppercase.  
To easily differentiate between config variables and other variables I make them all uppercase. Some people use a table for configs (=`Config.Luctus.NLR.Duration`) but I personally dislike this and would not recommend it.  
For more details on this read: [https://github.com/OverlordAkise/gmod-lua-performance#config-table-vs-variable](https://github.com/OverlordAkise/gmod-lua-performance#config-table-vs-variable)  
Short summary: It is slower if you do a table of your config.

In general I recommend you to create your own "coding style" and stick with it. This means "spaces or tabs for indentation", "brackets or none" for `if`s, etc..

This includes variable names. There are many ways to name a multi-word variable, for example with the function name `Create new world`:

 - createNewWorld (camel case)
 - CreateNewWorld (capital camel case)
 - CREATE_NEW_WORLD (upper case)
 - my_identifier (snake case)
 - create-new-world (kebab case)
 - createnewworld (flat case)

I personally use the first 3 together:

 - FunctionName in capital camel case
 - variableNames in camel case
 - CONFIG_VALUES in upper case

This makes it very easy to read and understand. For example the "flat case" is difficult to read with many words.
