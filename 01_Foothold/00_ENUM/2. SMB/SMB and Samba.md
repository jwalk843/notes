## Samba, SMB enumeration

Samba Enumeration:

	nmblookup -A target

	smbclient //MOUNT/share -I target -N

	rpcclient -U “” target

	enum4linux target

Mount Remote Windows Share:
	
    smbmount //X.X.X.X/c$ /mnt/remote/ -o username=user,password=pass,rw

WiSAMBA Enumeration:

http://www.iodigitalsec.com/windows-null-session-enumeration/
http://pen-testing.sans.org/blog/2013/07/24/plundering-windows-account-info-via-authenticated-smb-sessions
http://carnal0wnage.attackresearch.com/2007/07/enumerating-user-accounts-on-linux-and.html



