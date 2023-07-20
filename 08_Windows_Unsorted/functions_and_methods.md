Functions and Methods - Powershell
----------------------------------

A function can be executed multiple times, while a method can only be executed once.<br />
Each Object has a set of properties and methods

Get-Member and pipelining will show you the properties and methods of an object

```ps1
Get-Process | Get-Member
```
## Functions

```ps1
function Get-Process {
    Get-Process
}
```

## Methods

```ps1
Get-Process | Stop-Process
```


