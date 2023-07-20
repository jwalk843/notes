
# Registry Stuff

| Registry hive | 	Supporting files  |
|:--------------:|:-------------------:|
HKEY_CURRENT_CONFIG         | 	System, System.alt, System.log, System.sav
HKEY_CURRENT_USER           |	Ntuser.dat, Ntuser.dat.log
HKEY_LOCAL_MACHINE\SAM      |	Sam, Sam.log, Sam.sav
HKEY_LOCAL_MACHINE\Security |	Security, Security.log, Security.sav
HKEY_LOCAL_MACHINE\Software |	Software, Software.log, Software.sav
HKEY_LOCAL_MACHINE\System 	|   System, System.alt, System.log, System.sav
HKEY_USERS\.DEFAULT 	    |   Default, Default.log, Default.sav




# Reg Syntax, Help and Switches

Syntax
```batch
REG QUERY KeyName [/v [ValueName] | /ve] [/s]
          [/f Data [/k] [/d] [/c] [/e]] [/t Type] [/z] [/se Separator]
          [/reg:32 | /reg:64]

  KeyName  [\\Machine\]FullKey
           Machine - Name of remote machine, omitting defaults to the
                     current machine. Only HKLM and HKU are available on
                     remote machines
           FullKey - in the form of ROOTKEY\SubKey name
                ROOTKEY - [ HKLM | HKCU | HKCR | HKU | HKCC ]
                SubKey  - The full name of a registry key under the
                          selected **ROOTKEY**
```

<br>

## Help Output

```batch
C:\>reg /?

REG Operation [Parameter List]

  Operation  [ QUERY   | ADD    | DELETE  | COPY    |
               SAVE    | LOAD   | UNLOAD  | RESTORE |
               COMPARE | EXPORT | IMPORT  | FLAGS ]

Return Code: (Except for REG COMPARE)

  0 - Successful
  1 - Failed

For help on a specific operation type:

  REG Operation /?

Examples:

  REG QUERY /?
  REG ADD /?
  REG DELETE /?
  REG COPY /?
  REG SAVE /?
  REG RESTORE /?
  REG LOAD /?
  REG UNLOAD /?
  REG COMPARE /?
  REG EXPORT /?
  REG IMPORT /?
  REG FLAGS /?
```
<br>

## Switches

```batch

  /v       Queries for a specific registry key values.
           If omitted, all values for the key are queried.

           Argument to this switch can be optional only when specified
           along with /f switch. This specifies to search in valuenames only.

  /ve      Queries for the default value or empty value name (Default).

  /s       Queries all subkeys and values recursively (like dir /s).

  /se      Specifies the separator (length of 1 character only) in
           data string for REG_MULTI_SZ. Defaults to "\0" as the separator.

  /f       Specifies the data or pattern to search for.
           Use double quotes if a string contains spaces. Default is "*".

  /k       Specifies to search in key names only.

  /d       Specifies the search in data only.

  /c       Specifies that the search is case sensitive.
           The default search is case insensitive.

  /e       Specifies to return only exact matches.
           By default all the matches are returned.

  /t       Specifies registry value data type.
           Valid types are:
             REG_SZ, REG_MULTI_SZ, REG_EXPAND_SZ,
             REG_DWORD, REG_QWORD, REG_BINARY, REG_NONE
           Defaults to all types.

  /z       Verbose: Shows the numeric equivalent for the type of the valuename.

  /reg:32  Specifies the key should be accessed using the 32-bit registry view.

  /reg:64  Specifies the key should be accessed using the 64-bit registry view.

```
