# NFS Enumeration

port 111
`nmap -sV -p 111 --script=rpcinfo 10.11.1.1-254`

## nmap nfs nse script

`nmap -p 111 --script nfs* 10.11.1.72`

```
PORT    STATE SERVICE
111/tcp open  rpcbind
| nfs-showmount: 
|_  /home 10.11.0.0/255.255.0.0
```

⧉ Copied!

mount it to our machine:

```
mkdir home_72
sudo mount -o nolock 10.11.1.72:/home home_72
```

⧉ Copied!

-o nolock disable filelocking, need for old NFS servers

```
drwxr-xr-x 2 nobody 4294967294 4096 Oct 272019 .
drwxr-xr-x 7 root   root      4096 Sep 172015 ..
-rwx------ 1 nobody 4294967294 48 Oct 272019 creds.txt
```

shows nobody instead of userid, can not create a user id as guide shown