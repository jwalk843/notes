## Scanning

##### Netcat Scanning:

nc -v -w 1 target -z 1-1000
for i in {10..12}; do nc -vv -n -w 1 192.168.34.$i 21-25 -z; done

##### US Scanning:

us -H -msf -Iv 192.168.31.20 -p 1-65535 && us -H -mU -Iv 192.168.31.20 -p 1-65535

##### Unicornscan Scanning:

unicornscan X.X.X.X:a -r10000 -v

#### <ins>Kernel Scanning:</ins>

xprobe2 -v -p tcp:80:open 192.168.6.66

<ins>**Naabu**</ins>

Naabu is a port scanning tool written in Go that allows you to enumerate valid ports for hosts in a fast and reliable manner. It is a really simple tool that does fast SYN/CONNECT/UDP scans on the host/list of hosts and lists all ports that return a reply.

Benefits from the Gos high performance and concurrency to ensure speed.

```sh
# Naabu
https://github.com/projectdiscovery/naabu

# Install
sudo apt|yum install -y libpcap-dev

# Need to ensure go is installed and configured
go install -v github.com/projectdiscovery/naabu/v2/cmd/naabu@latest

naabu -p 80,443,21-23,u:53 -host example.com
naabu -p 80,443,21-23,u:53 -host 123.45.67.89

#To run the naabu on a list of hosts, -list option can be used.

naabu -list hosts.txt

# To run the naabu on a ASN, AS input can be used. 
# It takes the IP address available for 
# given ASN and runs the enumeration on them.

echo AS14421 | naabu -p 80,443

##       216.101.12.25:443
##       216.101.17.220:80

#You can also get output in json format using -json switch. 
# This switch saves the output in the JSON lines format.

naabu -host 104.16.99.52 -json

##      {"ip":"104.16.99.52","port":443}
##      {"ip":"104.16.99.52","port":80}


# The ports discovered can be piped to other tools too. 
# For example, you can pipe the ports discovered by naabu
# to httpx which will then find running http servers on the host.

echo hackerone.com | naabu -silent | httpx -silent
for i in `cat list.txt`; do echo $i | sudo naabu -nmap-cli 'nmap -sV -oX nmap-output' | tee -a output.txt;done

sudo naabu -host 10.11.1.0/24 -p 80 -nmap-cli 'nmap --script /usr/share/nmap/scripts/http-title.nse' -o output.txt
```

**
Powershell**

```
1..1024 | % {echo ((New-Object Net.Sockets.TcpClient).Connect("192.168.50.151", $_)) "TCP port $_ is open"} 2>$null
```