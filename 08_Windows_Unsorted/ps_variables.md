Variables - Powershell
------------------

## Variables

### Environment

```ps1
write-host "version of powershell"
$PSVersionTable
write-host "edition of powershell"
$PSEdition
write-host "location of powershell" 
$PSHOME             
write-host "location of powershell command"
$PSCommandPath
# powershell env session variables
$PSModulePath
$PSVersionTable
```
<hr \>

### Types
```ps1
x  = [double]1.3            # double
x  = "1.3"                  # Common String
x  = [int]1.3               # Will round
x  = [int16]1.3             # Will round
x  = [byte]1.3              # Also will round
x  = [short]1.3
x  = (1gb + 250mb)/120kb    # returns x=10817.4666666667 administrative constant number
x = 0xF # Returns 15
x = [BigInt]::Pow(111,222)
x  = @" 1.3 "@              # here string can contain " and multiple lines 
$x.GetType()

Clear-Variable -Name x
```

Reserved Vars:

    $Error contains an array of error objects.
    $Host contains information about the current hosting application.
    $Profile contains the path to the current user profile for PowerShell.
    $PID contains the process ID of the current PowerShell session.
    $PSUICulture contains the UI culture or the regional language of the user interface.
    $NULL contains the value of NULL.
    $False contains the value of False.
    $True contains the value of True.

> Source:
(Wikipedia, 2022), https://docs.microsoft.com/en-us/powershell/module/microsoft.powershell.core/about/about_arrays ↩︎

