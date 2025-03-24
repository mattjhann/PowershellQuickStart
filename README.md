# Quick Start for PowerShell

Powershell is a shell scripting language created by Microsoft that was originally meant for server administration and as a competitor to Linux's Bourne Again SHell (BASH). Since then it has become a basic front-end for the .NET framework, WMI, and COM (the bits and pieces of windows) and the community has expanded it to include many new features. These features are released in what are called Modules - groups of commands called cmdlets all centered around one purpose. For example Active Directory has a Module, to use the cmdlets that are relevant to it you must first load the Active Directory Module.

It is also worth noting that most things in powershell are case-insensitive.

When running a script powershell will execute top-to-bottom unless flow controls (if, for, while) tell it to do something different.

## Syntax

## COMMANDS

Powershell follows what is called a verb-noun format for all its commands. The command tells powershell broadly what you would like to do.
```
Get-Process
Import-Csv
Set-ItemProperty
DoThis-ToThis
```
From there you have parameters. These further specify how you want the thing done. They generally start with a dash "-" and then a name.
```
Get-ADUser -Identity "username"
```
In the space after a parameter you put the argument, this is user input that further specifies what to do. In the above example it's the "username".

## VARIABLES

Variables are ways to hold data. In Powershell they adapt to what's inside them which means the type is automatically determined - this isn't important generally, but it is sometimes important to know. Variables are always referenced with a dollar sign and assigned with the = sign:
```
$variable
$variable = "this"
```
You can change the type of variable with square brackets:
```
[string]$variable = "this"
[int]$variable = 123
```
Arrays are groups of things and symbolised by an @ symbol and some brackets.
```
$group = @(1, 2, 3, 4, 5, 6)
```
To select a particular part of an array you index with square brackets. You can do this from the start with a positive number (0 for the first item) or a negative number (-1 for the last item). You can select more than one by separating with commas or use two dots to select all between two values.
```
$group[0] #The first value
$group[-1] #The last value
$group[2..4] #The third to the fifth value
```
The hash character makes everything after it a comment and powershell ignores it.

## THE PIPELINE

Some commands give an output. There are a couple of things you can do with this:

1.Send it to the console (this is the default): Get-Process

2.Send it to a variable: $processes = Get-Process

3.Send it to another command with a pipe "|" (next to Z on a UK keyboard, above Enter on a US keyboard):
```
Get-Process | Where-Object -Property "ProcessName" -eq -Value "svchost"
```

In example 3 I used a pipe. This takes the output from the previous command and uses it as the input for the second. Where-Object is a way of filtering an object depending on certain criteria. In this example it uses the output from Get-Process to find where the -Property called "ProcessName" equals (-eq) the -Value of "svchost".

Powershell comparisons are slightly different to other languages:
```
Equals: -eq
Not equals: -ne
Greater than: -gt
Less than: -lt
Like (allows wildcards like *): -like
Isn't like: -notlike
etc. (you can look these up online)
```

Using the pipeline efficiently is one of the challenges of powershell and can create some pretty powerful "One-Liners". While it's a nice challenge and sometimes a good exercise to see how much you can do in one line, your scripts for production should probably split the steps into variables to allow for easier debugging.

## ifs and loops

### IF STATEMENTS

Flow controls in the form of ifs and loops are incredibly useful tools and are required for most things. They are written with the condition in normal brackets and the thing you want it to do in curly brackets:
```
if ( This ) { Do-This }
```
I usually lay this out with tabs and line breaks for easier reading like so, but it's not required:
```
if ( This ) {
	Do-This
} else if ( That ) {
	Do-That
} else {
	Do-Other
}
```
In the above example I added the other parts of an if statement. "Else ifs" are important if you have a few different things that could happen and you wish to check for them. "Else" is used as a final case if none of the previous ifs are met. The entire bracket that contains the condition must evaluate to $true for the statement to run. If you want a lack of something to trigger the if statement you can do it a couple of different ways:
```
1. if (!$this) {
2. if ($this -eq $false) {
3. if ($this -ne $true) {
```
Notice I used $false and $true above? This is because they are the absolute (boolean) True and False to compare to, rather than a generic string.

You can also have multiple conditions all evaluated in one go:
```
-and : this returns true if both sides are true  
-or : this returns true if one or both sides are true  
-xor : this returns true if only one of the things are true
```
```
($true -and $true) returns $true
($true -and $false) returns $false
($true -or $false) returns $true
($true -or $true) returns $true
($true -xor $true) returns $false
```
These can be grouped together with brackets, evaluate the inner brackets before the outer. WHO SAID ALGEBRA WASN'T USEFUL!
```
($true -xor ($true -and $false)) returns $true
(($false -or $false) -xor ($true -and $false) returns $false
```
### FOR LOOPS
Powershell has a couple of different for loops. There's "foreach", and the more traditional "for". Generally you will use "foreach", but it's good to know that "for" exists. For-type loops go through code a specific number of times and then stop. Say you have an array with 10 things in it, and you want to do something for each one of these things:
```
foreach ( $thing in $10Things ) {
	Write-Host $thing
}
```
Notice the same format as the if statement. The part in the brackets gives the for loop its conditions. $10Things is an array with 10 entries, $thing is a placeholder for the entry that we are currently working with. Write-Host just puts the result of whatever comes after into the command line. In this example it runs through each of the $10Things one by one and writes out each $thing to the console.

### WHILE LOOPS

These are indefinite loops and will keep going until a condition is no longer true. Think of them like an if statement that just won't quit until it becomes $false. The syntax here is probably starting to become familiar:
```
while ( $thing ) {
	Do-This
}
```
This will keep looping until whatever is in $thing becomes false. It's probably worth mentioning that you can put whatever you want in these and the if's brackets and as long as it comes back to $true or $false you're golden. For example PowerShell’s ping "Test-Connection" has a parameter "-Quiet" that returns $true or $false depending on whether there's a connection or not. This is a common condition for *if* and *while* statements. In a while loop if the condition returns true then it executes the code and *loops* back to the top to check again and repeat until the condition is false.

## Useful cmdlets
```
Get-Help
```
Probably the command I use most often. Put the command you're struggling with after this command to get the built in instructions on how to use it.
```
Get-Command *service*
```
The second most used command. Know what you want to do, but don't know the command? Guess a word that's inside it and put that word after Get-Command. You might be surprised.
```
Get-Module -ListAvailable
Import-Module sqlserver
```
If you have custom modules, and need to load them in, this shows everything that powershell can detect. You can find which folders PS looks in by looking at the environment variable `$env:PSModulePath`.
```
Import-Csv $File
$input = Import-Csv $File
# or...
$input = Get-Content $File | ConvertFrom-Json
```
Grabs the data from a CSV and returns an object. Create a variable and make it equal to this for best usage. There's a similar ConvertTo-Json for json.
```
$output | Export-Csv $Directory -NoTypeInformation
```
Piping to Export-Csv or ConvertTo-Json is a quick way to save the information from a variable you're working on. -NoTypeInformation gets rid of an annoying powershell type it normally leaves in the top row.
```
Test-Path $Directory
```
This returns whether a directory exists in $true or $false format.
```
Get-ChildItem $Directory
gci $Directory
```
Like dir back in cmd or ls if you're a Linux head. It displays the directory underneath the directory you supply. -Recurse does all the directories below those as well. -File and -Directory say whether to only return files or folders respectively. gci is an alias (a shortened version of the command). So is ls. So is dir actually.
```
Where-Object
Get-Process | Where-Object -Property "ProcessName" -eq -Value "svchost"
Get-Process | Where-Object ProcessName -eq ‘SVCHost’
Get-Process | Where { $_.ProcessName -eq "svchost" }
```
A handy way of filtering an object. There are a couple of ways to do this, but the most common is to pipe an object to it. I've included a couple of different formats in the example, you can filter using a script block like the bottom one if you want to save space (I'll go over that in the Extra bits section). "Where" is an alias.
```
Select-Object Get-Process | Select ProcessName
```
In much the same way "Where-Object" filters the rows, "Select-Object" filters the columns by name. If that particular column contains nested objects you can use `-ExpandProperty` to show them properly.
```
Sort-Object
Get-Process | Sort ProcessName
```
This is your basic Sort A-Z on a column. You can do other things with the parameters. Try running it through Get-Help to find out what!
```
Out-GridView
Get-Process | ogv
Get-Service | ogv -PassThru | Start-Service
```
Out-GridView or ogv displays the data in a separate window with some basic filtering and sorting capabilities. `-PassThru` sends the selected rows in ogv on through the pipeline. I've included a useful snippet I've used a few times recently.

## Extra bits
When you use a pipe into a command you access the information in the pipeline using the variable $_

Similarly when piping to foreach you don’t specify the temporary variable name, instead you use $_. Notice it is also missing the first brackets ( ).
```
$10Things | foreach { Do-This -tothis $_ }
```
Should you need to access a particular column of a variable and you would like to avoid using select-object, you can use dot notation $Processes.ProcessName This will list all the process names in the object but nothing else.

If you want to use dot notation inside double quotes you need to create a container for them:
```
Write-Host "The process is called $($Process.ProcessName)"
```
You can put an entire command in there if you want:
```
Write-Host "The first process in the list is: `n $(Get-Process | Select -First 1 -ExpandProperty ProcessName)"
```
The backtick is the powershell escape character. Put it before a character you don't want powershell to evaluate as it normally would. You can also follow it with an n, r, or t to add a new line, carriage return, or tab respectively.

Single quotes will not evaluate anything and pass an exact string whether or not it has special characters in it.

### Creating Custom Objects
The pipeline is a good way to create custom objects without too much overhead. Hash tables are created similarly to arrays, except you define a property name. The syntax relies on new lines. Sometimes you will see people use the ; this is the PowerShell character for a new line.
```
	$results = foreach ($process in (Get-Process)){
		$htProcesses = @{
			"Process" = $process.name
			"Date" = (Get-Date).ToShortDateString()
		}
		New-Object PSObject -Property $htProcesses
	}
```
A quick explanation of what it’s doing: it goes through each of the processes, then creates a hashtable with one row and creates a new object (with one row) that then outputs to the pipeline. $results then updates with each of the foreach loops and adds the row in. For more information about the pipeline try this article: https://www.red-gate.com/simple-talk/sysadmin/powershell/ins-and-outs-of-the-powershell-pipeline/

