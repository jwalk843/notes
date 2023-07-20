# Chisel

## [**GitHub**](https://github.com/jpillora/chisel)

Download from the **[Releases Page](https://github.com/jpillora/chisel/releases/latest)**

## **Usage**

- The target host
- The attacker's host.

Remote static tunnels "port to port":
```
./chisel server -p 8000 -reverse
./chisel client x.x.x.x:8000 R:127.0.0.1:8001:x.x.x.x:80

```
listen on the target for something, and send it to us
Remote
` ./chisel client 10.1.1.1:8000 9001:127.0.0.1:8003`

Remote tunnels
```
./chisel client x.x.x.x:8000 R:127.0.0.1:8001:172.19.0.3:80
127.0.0.1:1080 Or set up socks5 proxy on firefox
```
For nmap use -Pn -sT 
