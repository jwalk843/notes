### Network Scanning

```
nmap -sn 10.11.1.*
```

```
nmap -sL 10.11.1.*
```

```
nbtscan -r 10.11.1.0/24
```

```
smbtree
```

### HOST SCAN

```
nmap --top-ports 20 --open -iL iplist.txt
```

```
nmap -sS -A -sV -O -p- ipaddress
```

```
nmap -sU ipaddress
```

```
masscan -p1-65535
```

```
unicornscan -mT -Iv ipaddress -p 1-65535
```

```
Nuclei
```

### **Netcat Scanning:**

```
 nc -v -w 1 target -z 1-1000
	for i in {10..12}; do nc -vv -n -w 1 192.168.34.$i 21-25 -z; done
```

```
US Scanning:
```

```
us -H -msf -Iv 192.168.31.20 -p 1-65535 && us -H -mU -Iv 192.168.31.20 -p 1-65535
```

### Unicornscan Scanning:

```
 unicornscan X.X.X.X:a -r10000 -v
```

### Kernel Scanning:

```
 xprobe2 -v -p tcp:80:open 192.168.6.66
```

```
 nc -v -w 1 target -z 1-1000
	for i in {10..12}; do nc -vv -n -w 1 192.168.34.$i 21-25 -z; done
```

### US Scanning:

```
us -H -msf -Iv 192.168.31.20 -p 1-65535 && us -H -mU -Iv 192.168.31.20 -p 1-65535
```

### Unicornscan Scanning:

```
 unicornscan X.X.X.X:a -r10000 -v
```

### Kernel Scanning:

```
 xprobe2 -v -p tcp:80:open 192.168.6.66
```