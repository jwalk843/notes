# Web Enumeration

### 
Dirb Directory Bruteforce:

```
dirb http://IP:PORT dirbuster-ng-master/wordlists/common.txt
```

### 
Nikto Scanner:

```
nikto -C all -h http://IP
```

⧉ Copied!

### WordPress Scanner:

```
wpscan –url http://IP/ –enumerate p
```

⧉ Copied!

### Uniscan Scanning:

```
uniscan.pl -u target -qweds
```

⧉ Copied!

### HTTP Enumeration:

```
httprint -h http://www.example.com -s signatures.txt
```

⧉ Copied!

### SKIP Fish Scanner:

```
skipfish -m 5 -LVY -W /usr/share/skipfish/dictionaries/complete.wl -u http://IP
-m  -time threads
-LVY donot update after result

```

⧉ Copied!

### Uniscan Scanning:

```
uniscan –u http://somesite.com –qweds
-q – Enable Directory checks
-w – Enable File Checks
-e – Enable robots.txt and sitemap.xml check
-d – Enable Dynamic checks
-s – Enable Static checks
```

⧉ Copied!