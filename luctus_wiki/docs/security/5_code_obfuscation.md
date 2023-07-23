# Deobfuscating lua code

To obfuscate means to make your code unreadable but still runable.  
This makes it difficult for other people to steal your code and change it, because all of it is "mangled" into a different form.

There are many ways to obfuscate lua code, but most simply use a bit.bxor (xor gate) method of making it unreadable.

This method of obfuscation is not recommended as it can very easily be reversed, as I will show below with a real example a friend sent me.

## Input code

The following code is the obfuscated clientside file:

```lua
RunString([[ local AE = {12,15,3,1,12,64,42,50,35,15,14,6,64,93,64,42,15,2,50,1,14,11,19,35,15,14,6,9,7,106,9,6,64,42,50,35,15,14,6,78,40,53,36,64,20,8,5,14,106,105,19,21,18,6,1,3,5,78,35,18,5,1,20,5,38,15,14,20,72,66,10,18,63,4,9,19,16,12,1,25,66,76,64,27,6,15,14,20,64,93,64,66,36,5,6,1,21,12,20,66,76,64,19,9,26,5,64,93,64,81,86,76,64,19,8,1,4,15,23,64,93,64,20,18,21,5,76,64,23,5,9,7,8,20,64,93,64,81,80,80,80,29,73,106,106,105,12,15,3,1,12,64,2,12,1,3,11,63,20,18,1,14,19,64,93,64,35,15,12,15,18,72,85,80,76,64,85,80,76,64,85,80,76,64,82,80,80,73,106,105,12,15,3,1,12,64,23,8,9,20,5,63,15,21,20,64,93,64,35,15,12,15,18,72,82,85,85,76,64,82,85,85,76,64,82,85,85,76,64,85,80,73,106,105,12,15,3,1,12,64,23,8,9,20,5,63,3,15,18,14,5,18,64,93,64,35,15,12,15,18,72,82,85,85,76,64,82,85,85,76,64,82,85,85,76,64,81,82,80,73,106,106,105,12,15,3,1,12,64,5,4,7,5,63,23,4,64,93,64,82,106,105,12,15,3,1,12,64,6,21,14,3,20,9,15,14,64,36,18,1,23,37,4,7,5,19,72,24,76,64,25,76,64,23,9,4,20,8,76,64,8,5,9,7,8,20,76,64,19,9,26,5,73,106,105,105,19,21,18,6,1,3,5,78,36,18,1,23,50,5,3,20,72,24,76,64,25,76,64,19,9,26,5,76,64,5,4,7,5,63,23,4,73,106,105,105,19,21,18,6,1,3,5,78,36,18,1,23,50,5,3,20,72,24,76,64,25,64,75,64,5,4,7,5,63,23,4,76,64,5,4,7,5,63,23,4,76,64,19,9,26,5,64,77,64,5,4,7,5,63,23,4,73,106,105,105,12,15,3,1,12,64,24,18,9,7,8,20,64,93,64,24,64,75,64,23,9,4,20,8,106,105,105,19,21,18,6,1,3,5,78,36,18,1,23,50,5,3,20,72,24,18,9,7,8,20,64,77,64,19,9,26,5,76,64,25,76,64,19,9,26,5,76,64,5,4,7,5,63,23,4,73,106,105,105,19,21,18,6,1,3,5,78,36,18,1,23,50,5,3,20,72,24,18,9,7,8,20,64,77,64,5,4,7,5,63,23,4,76,64,25,64,75,64,5,4,7,5,63,23,4,76,64,5,4,7,5,63,23,4,76,19,9,26,5,64,77,64,5,4,7,5,63,23,4,73,106,105,105,12,15,3,1,12,64,25,2,15,20,20,15,13,64,93,64,25,64,75,64,8,5,9,7,8,20,106,105,105,19,21,18,6,1,3,5,78,36,18,1,23,50,5,3,20,72,24,18,9,7,8,20,64,77,64,19,9,26,5,76,64,25,2,15,20,20,15,13,64,77,64,5,4,7,5,63,23,4,76,64,19,9,26,5,76,64,5,4,7,5,63,23,4,73,106,105,105,19,21,18,6,1,3,5,78,36,18,1,23,50,5,3,20,72,24,18,9,7,8,20,64,77,64,5,4,7,5,63,23,4,76,64,25,2,15,20,20,15,13,64,77,64,19,9,26,5,76,64,5,4,7,5,63,23,4,76,64,19,9,26,5,64,77,64,5,4,7,5,63,23,4,73,106,105,105,19,21,18,6,1,3,5,78,36,18,1,23,50,5,3,20,72,24,76,64,25,2,15,20,20,15,13,64,77,64,5,4,7,5,63,23,4,76,64,19,9,26,5,76,64,5,4,7,5,63,23,4,73,106,105,105,19,21,18,6,1,3,5,78,36,18,1,23,50,5,3,20,72,24,76,64,25,2,15,20,20,15,13,64,77,64,19,9,26,5,76,64,5,4,7,5,63,23,4,76,64,19,9,26,5,64,77,64,5,4,7,5,63,23,4,73,106,105,5,14,4,106,106,105,12,15,3,1,12,64,48,12,1,25,5,18,76,64,42,15,2,50,1,14,11,76,64,42,15,2,76,64,42,15,2,50,1,14,11,52,2,12,106,105,12,15,3,1,12,64,6,21,14,3,20,9,15,14,64,36,18,1,23,50,1,14,11,41,14,6,15,72,73,106,105,105,48,12,1,25,5,18,64,93,64,48,12,1,25,5,18,64,15,18,64,44,15,3,1,12,48,12,1,25,5,18,72,73,106,105,105,42,15,2,50,1,14,11,64,93,64,48,12,1,25,5,18,90,39,5,20,42,15,2,50,1,14,11,72,73,106,106,105,105,9,6,64,42,15,2,50,1,14,11,64,94,64,80,64,20,8,5,14,106,105,105,105,42,15,2,64,93,64,48,12,1,25,5,18,90,52,5,1,13,72,73,106,105,105,105,42,15,2,50,1,14,11,52,2,12,64,93,64,42,15,2,50,1,14,11,19,59,42,15,2,61,106,105,105,105,9,6,64,42,15,2,50,1,14,11,52,2,12,64,20,8,5,14,106,105,105,105,105,12,15,3,1,12,64,55,76,64,40,64,93,64,82,82,80,76,64,84,80,106,105,105,105,105,12,15,3,1,12,64,34,15,18,4,5,18,64,93,64,85,106,105,105,105,105,12,15,3,1,12,64,55,48,15,19,64,93,64,51,3,18,55,72,73,64,77,64,55,64,77,64,34,15,18,4,5,18,106,105,105,105,105,12,15,3,1,12,64,40,48,15,19,64,93,64,34,15,18,4,5,18,106,106,105,105,105,105,19,21,18,6,1,3,5,78,51,5,20,36,18,1,23,35,15,12,15,18,72,2,12,1,3,11,63,20,18,1,14,19,73,106,105,105,105,105,19,21,18,6,1,3,5,78,36,18,1,23,50,5,3,20,72,55,48,15,19,76,64,40,48,15,19,76,64,55,76,64,40,73,106,105,105,105,105,19,21,18,6,1,3,5,78,51,5,20,36,18,1,23,35,15,12,15,18,72,23,8,9,20,5,63,15,21,20,73,106,105,105,105,105,19,21,18,6,1,3,5,78,36,18,1,23,47,21,20,12,9,14,5,4,50,5,3,20,72,55,48,15,19,76,64,40,48,15,19,76,64,55,76,64,40,73,106,106,105,105,105,105,19,21,18,6,1,3,5,78,51,5,20,36,18,1,23,35,15,12,15,18,72,23,8,9,20,5,63,3,15,18,14,5,18,73,106,105,105,105,105,36,18,1,23,37,4,7,5,19,72,55,48,15,19,76,64,40,48,15,19,76,64,55,76,64,40,76,64,88,73,106,106,105,105,105,105,4,18,1,23,78,51,9,13,16,12,5,52,5,24,20,72,66,50,1,14,7,90,64,66,78,78,48,12,1,25,5,18,90,39,5,20,42,15,2,50,1,14,11,46,1,13,5,72,73,76,64,66,10,18,63,4,9,19,16,12,1,25,66,76,64,55,48,15,19,64,75,64,55,64,79,64,82,76,64,40,48,15,19,64,75,64,84,76,64,3,15,12,15,18,63,23,8,9,20,5,76,64,52,37,56,52,63,33,44,41,39,46,63,35,37,46,52,37,50,76,64,52,37,56,52,63,33,44,41,39,46,63,52,47,48,73,106,105,105,105,105,12,15,3,1,12,64,45,1,24,64,93,64,42,15,2,50,1,14,11,19,59,42,15,2,61,78,45,1,24,42,15,2,50,1,14,11,106,105,105,105,105,12,15,3,1,12,64,52,5,24,20,64,93,64,66,40,9,5,18,1,18,3,8,9,5,90,64,66,78,78,42,15,2,50,1,14,11,78,78,66,79,66,78,78,45,1,24,106,105,105,105,105,4,18,1,23,78,51,9,13,16,12,5,52,5,24,20,72,52,5,24,20,76,64,66,10,18,63,4,9,19,16,12,1,25,66,76,64,55,48,15,19,64,75,64,55,64,79,64,82,76,64,40,48,15,19,64,75,64,82,80,76,64,3,15,12,15,18,63,23,8,9,20,5,76,64,52,37,56,52,63,33,44,41,39,46,63,35,37,46,52,37,50,76,64,52,37,56,52,63,33,44,41,39,46,63,52,47,48,73,106,105,105,105,5,14,4,106,105,105,5,14,4,106,105,5,14,4,106,105,8,15,15,11,78,33,4,4,72,66,40,53,36,48,1,9,14,20,66,76,64,66,42,15,2,50,1,14,11,19,63,36,18,1,23,50,1,14,11,41,14,6,15,66,76,64,36,18,1,23,50,1,14,11,41,14,6,15,73,106,5,14,4,64,77,77,64,0}
local function RunningDRMe()if (debug.getinfo(function()end).short_src~="tenjznj")then return end for o=500,10000 do local t=0 if t==1 then return end  if o~=string.len(string.dump(RunningDRMe))then  AZE=10  CompileString("for i=1,40 do AZE = AZE + 1 end","RunString")()  if AZE<40 then return end continue  else  local pdata=""  xpcall(function()  for i=1,#AE do  pdata=pdata..string.char(bit.bxor(AE[i],o%150))  end  for i=1,string.len(string.dump(CompileString)) do  while o==1 do  o=o+1  end  end  end,function()  xpcall(function()  local debug_inject=CompileString(pdata,"DRME")  pcall(debug_inject,"stat")  pdata="F"  t=1  end,function()  print("error")  end)  end)  end  end end RunningDRMe() ]],"tenjznj")
```

Attention: All of this code was in one line, but for better readability I split the code and executor into 2 lines.

## Obfuscated Analysis

As you can see by the above input code everything is in a single line and the first half of the whole code is a table of numbers.

If you haven't known this already: All "normal" characters of the alphabet can be displayed in numbers.  
You can search "ASCII Table" on the internet to see the whole representation. For example, 65 is a capital A.

To get the character from an ASCII number you can use `string.char`.

```lua
> string.char(65)
A
```


This means that the obfuscated code immediately looks like such an obfuscation method, using ASCII number representation of characters to hide the code.

The bottom part of the code shows that this is true by a call to `bit.bxor`. This xor-gate turns, simply speaking, an input number into a different number, toggling between the 2 possible numbers with the same xor comparison. This means the obfuscate and deobfuscate process can be done with the same number.

So in summary: The code has been turned from alphabet characters into ASCII numbers presenting those characters, then put into a table and each number was changed with an xor-gate. The code at the bottom of the file simply reverts this xor-gate change by running the same operation again, then adding all the characters of the code back into a string and running it with `CompileString`.


## Cleaning up the code

To make this method clear we can prettify the code used and shrink it to the main part. The code (shortened for better display) with indents looks like this:

```lua
RunString([[
	local AE = {12,15,3,1,12,64,42,50,35,15,14,6,64,93,64,42,15,2,50,1,14,11,19,35,15,14,6,9,7}
	local function RunningDRMe()
		if (debug.getinfo(function()end).short_src~="tenjznj")then return end
		for o=500,10000 do
			local t=0
			if t==1 then return end
			if o~=string.len(string.dump(RunningDRMe))then
				AZE=10
				CompileString("for i=1,40 do AZE = AZE + 1 end","RunString")()
				if AZE<40 then return end
				continue
			else
				local pdata=""
				xpcall(function()
					for i=1,#AE do
						pdata=pdata..string.char(bit.bxor(AE[i],o%150))
					end
					for i=1,string.len(string.dump(CompileString)) do
						while o==1 do
							o=o+1
						end
					end
				end,function()
					xpcall(function()
						local debug_inject=CompileString(pdata,"DRME")
						pcall(debug_inject,"stat")
						pdata="F"
						t=1
					end,function()
						print("error")
					end)
				end)
			end
		end 
	end
	RunningDRMe() 
]],"tenjznj")
```

To start with line 1: All of the code is being run with RunString, even though the code could also be run without it.  
The source of the RunString at the end, "tenjznj", will be checked inside the local function "RunningDRMe", but this could also be removed together with the RunString. At the same time we could also remove the local function and just run the code directly, as neither RunString nor a function is not needed to run the code.

This would turn the above code into this:

```lua
1.	local AE = {12,15,3,1,12,64,42,50,35,15,14,6,64,93,64,42,15,2,50,1,14,11,19,35,15,14,6,9,7}
2.	for o=500,10000 do
3.		local t=0
4.		if t==1 then return end
5.		if o~=string.len(string.dump(RunningDRMe))then
6.			AZE=10
7.			CompileString("for i=1,40 do AZE = AZE + 1 end","RunString")()
8.			if AZE<40 then return end
9.			continue
10.		else
11.			local pdata=""
12.			xpcall(function()
13.				for i=1,#AE do
14.					pdata=pdata..string.char(bit.bxor(AE[i],o%150))
15.				end
16.				for i=1,string.len(string.dump(CompileString)) do
17.					while o==1 do
18.						o=o+1
19.					end
20.				end
21.			end,function()
22.				xpcall(function()
23.					local debug_inject=CompileString(pdata,"DRME")
24.					pcall(debug_inject,"stat")
25.					pdata="F"
26.					t=1
27.				end,function()
28.					print("error")
29.				end)
30.			end)
31.		end
32.	end
```

In the above code example we can now see that the whole code is only 32 lines long. But this can be shorted even more.

In line 2 a loop from 500 to 10.000 starts. The loop variable, o, is only used in 2 spots:

 - Compare it to the string.dump length of the whole function (RunningDRMe) at line 5
 - If it is the length, then use it mod150 for the bit xor comparison at line 14

This means that the whole loop can be removed if we find out the length of the function.  
If you run this code it turns out this is 996, constantly as the function length never changes in any of the obfuscated code examples.
If we remove the loop we can also remove the comparison at line 5 and thus the first part of the if block afterwards, if the comparison fails, aswell. (Line 6-9)

If we check the variable t from line 3 we can also see that this was just an "anti-double-execute" check, which means we can remove this too because the loop was removed and thus no double-execution of code could happen.

Now all that remains is the main code from Line 11-30. This, via safe pcalls, uses the bit.bxor function to change the numbers back into their ASCII representation (Line 14) and then execute it (Line 23/24).  
The Lines 16-20 can be removed aswell as they are useless. The pcalls and other fancy lines can be removed aswell (e.g. Line 25-29, 12). We can also do the mod at Line 14 hardcoded with 996, which leaves us with 96 as the second argument to the bit.bxor function.

After all the changes the code now looks like this:

```lua
local AE = {12,15,3,1,12,64,42,50,35,15,14,6,64,93,64,42,15,2,50,1,14,11,19,35,15,14,6,9,7}
local pdata = ""
for i=1,#AE do
	pdata=pdata..string.char(bit.bxor(AE[i],96))
end
RunString(pdata,"luctus")
```

If we replace the the RunString with print and execute it with luajit we get the first line of the original script:

```lua
--$ luajit test.lua
local JRConf = JobRanksConfig
```

With these 6 lines of code you can now decode all of these RunningDRMe scripts and get back their original files.

## Conclusion

Because of the simplicity of this obfuscator it was really easy to understand and deobfuscate it.  
This obfuscation method is only used to deter so-called "scriptkiddies" from reading or stealing lua code. But if someone knows that they are doing then the whole process can be simplified and streamlined, which results in no extra protection.

I personally do not recommend to use any sort of obfuscation. It is always easy to turn back into readable code and thus only makes the debugging process on servers more difficult with all of their RunStrings.

For example, if a config file is obfuscated then you don't even have to deobfuscate the lua file. You can simply join the server and get the config via, for example:

```lua
file.Write("jobranks.txt",util.TableToJSON(JobRanksConfig))
```

This way the entire config of e.g. Jobranks will be written into your data folder, completely skipping any lua file deobfuscation.

