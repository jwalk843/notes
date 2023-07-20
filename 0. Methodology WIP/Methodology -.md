# Methodology

### ==Network Scanning==

- [ ] nmap -sn 10.11.1.*
- [ ] nmap -sL 10.11.1.*
- [ ] nbtscan -r 10.11.1.0/24
- [ ] smbtree

### ==Individual Scan==

- [ ] nmap --top-ports 20 --open -iL iplist.txt
- [ ] nmap -sS -A -sV -O -p- ipaddress
- [ ] nmap -sU ipaddress
- [ ] masscan -p1-65535
- [ ] unicornscan -mT -Iv ipaddress -p 1-65535

### Scripts and Checks

#### NMAP Scripts
- [ ] nmap --script broadcast
- [ ] nmap --script default,safe,discovery,version
- [ ] nmap --script vuln,malware,exploit
- [ ] nmap --script instrusive
- [ ] nmap --script default, fuzzer
- [ ] nmap --script brute,auth
- [ ] nmap --script broadcast
-
#### Nuclei 
- [ ] nuclei -u https://my.target.site
- [ ] nuclei -l /path/to/list-of-targets.txt
- [ ] nuclei -u https:// my.target.site -as (automatic selection)
- [ ] nuclei -l list-of-targets.txt -retries 3 (default they nuclei will not retry)
- [ ] nuclei -u https://api.target.site -tags php,apache -H “Cookie:sc_=BPGFJcNgMwfePZBeJqoC838j8Mv4”
# Service Scanning

## ==WebApp==
- [ ] Nikto
- [ ] Feroxbuster
- [ ] dirb
- [ ] dirbuster
- [ ] wpscan
- [ ] dotdotpwn
- [ ] view source
- [ ] davtest\\cadevar
- [ ] droopscan
- [ ] joomscan
- [ ] wapiti
- [ ] feroxbuster

## ==LFI\\RFI Test==

## ==Linux\\Windows==

- [ ] snmpwalk -c public -v1 ipaddress 1
- [ ] smbclient -L //ipaddress
- [ ] showmount -e ipaddress port
- [ ] rpcinfo
- [ ] Enum4Linux

## ==Anything Else==

- [ ] nmap scripts (locate *nse* | grep servicename)
- [ ] hydra
- [ ] MSF Aux Modules
- [ ] Download the softward

## ==Exploitation==

- [ ] Gather Version Numbes
- [ ] Searchsploit
- [ ] Default Creds
- [ ] Creds Previously Gathered
- [ ] Download the software

# Post Exploitation

## ==Linux==

- [ ] linux-local-enum.sh
- [ ] linuxprivchecker.py
- [ ] linux-exploit-suggestor.sh
- [ ] unix-privesc-check.py

## ==Windows==

- [ ] wpc.exe
- [ ] windows-exploit-suggestor.py
- [ ] windows\_privesc\_check.py
- [ ] windows-privesc-check2.exe

# ==Priv Escalation==

- [ ] acesss internal services (portfwd)
- [ ] add account

## ==Windows==

- [ ] List of exploits

## ==Linux==

- [ ] sudo su
- [ ] KernelDB
- [ ] Searchsploit

# ==Final==

- [ ] Screenshot of IPConfig\\WhoamI
- [ ] Copy proof.txt
- [ ] Dump hashes
- [ ] Dump SSH Keys
- [ ] Delete files
- [ ] Shut ports / services down
- [ ] Clear history
- [ ] Clear logs