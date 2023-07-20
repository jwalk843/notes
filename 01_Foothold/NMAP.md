## NMAP

### Nmap

Full Web Vulnerable Scan:

```
mkdir /usr/share/nmap/scripts/vulscan
cd /usr/share/nmap/scrripts/vulscan
wget http://www.computec.ch/projekte/vulscan/download/nmap_nse_vulscan-2.0.tar.gz && tar xzf nmap\_nse\_vulscan-2.0.tar.gz

nmap -sS -sV –script=vulscan/vulscan.nse target
nmap -sS -sV –script=vulscan/vulscan.nse –script-args vulscandb=scipvuldb.csv target
nmap -sS -sV –script=vulscan/vulscan.nse –script-args vulscandb=scipvuldb.csv -p80 target
nmap -PN -sS -sV –script=vulscan –script-args vulscancorrelation=1 -p80 target
nmap -sV –script=vuln target
nmap -PN -sS -sV –script=all –script-args vulscancorrelation=1 target
```

Nmap Ports Scan:

decoy- 

```
masqurade nmap -D RND:10 \[target\] (Generates a random number of decoys)
masqurade nmap -D RND:10 \[target\] (Generates a random number of decoys)
```

fraement
```
# data packed – like orginal one not scan packet

# use auxiliary/scanner/ip/ipidseq for find zombie ip in network to use them to scan — 
  nmap -sI ip target

nmap –source-port 53 target
nmap -sS -sV -D IP1,IP2,IP3,IP4,IP5 -f –mtu=24 –data-length=1337 -T2 target ( Randomize scan form diff IP)
nmap -Pn -T2 -sV –randomize-hosts IP1,IP2
nmap –script smb-check-vulns.nse -p445 target (using NSE scripts)
nmap -sU -P0 -T Aggressive -p123 target (Aggresive Scan T1-T5)
nmap -sA -PN -sN target
nmap -sS -sV -T5 -F -A -O target (version detection)
nmap -sU -v target (Udp)
nmap -sU -P0 (Udp)
nmap -sC 192.168.31.10-12 (all scan default)
```