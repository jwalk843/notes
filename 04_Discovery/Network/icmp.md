ICMP Tricks
===========

### ICMP exfiltration


# Using scapy
```
$ scapy
Welcome to Scapy (2.2.0)
>>> pkts = rdpcap("ch6.pcap")
>>> payload_echo_request = [ p[ICMP].load for p in pkts if (p[ICMP].type == 8 and p[ICMP].code == 0)]
>>> outfile = open('ICMP_payload.dump', 'wt')
>>> outfile.write("".join(payload_echo_request))
>>> outfile.close()
```

# Using Python
```
#!/usr/bin/python

from scapy.all import *
import yenc

pkts = rdpcap("./echo_request.pcap")
fichier = open("./donnees","w")

for i in range(0,len(pkts)):
    fichier.write(pkts[i].load)
    print yenc.decode("./donnees","./résultat")
```
### ICMP Exfiltration 2
```
#!/usr/bin/env python

from scapy.all import *

pcap_file = "challenge.pcapng"

data = rdpcap(pcap_file)

output = ""

for i in range(0, len(data)):
    if data[i].getlayer(IP).dst == "192.168.1.33":
        payload = data[i].load.decode("utf-8")
        print(payload)
        output += payload

output_file = open("result.b64", "w")
output_file.write(output)
output_file.close()
```
