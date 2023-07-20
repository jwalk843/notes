# SMTP enumeration

find existing users
port 25, udp

```
nc -nv 10.11.1.217 25
VRFY idontexist
```

# SNMP Enumeration

## SNMP MIB Tree

## Scan for SNMP

`sudo nmap -sU --open -p 161 10.11.1.1-254 -oG open-snmp.txt`
--open only display open ports
`onesixtyone -c community -i ips`
`onesixtyone -c /usr/share/doc/onesixtyone/dict.txt 192.168.119.133`

## example

enumerating the entire MIB tree:
`snmpwalk -c public -v1 -t 10 10.11.1.14`
-v snmp version number
-t increase timeout to 10s

SNMP Walk:

- snmpwalk -c public -v1 ipaddress 1
- snmpwalk -c private -v1 ipaddress 1
- snmpwalk -c manager -v1 ipaddress 1

Nmap:

- nmap 172.21.0.0 -Pn -sU -p 161 --script=

/usr/share/nmap/scripts/snmp-brute.nse
/usr/share/nmap/scripts/snmp-hh3c-logins.nse
/usr/share/nmap/scripts/snmp-info.nse
/usr/share/nmap/scripts/snmp-interfaces.nse
/usr/share/nmap/scripts/snmp-ios-config.nse
/usr/share/nmap/scripts/snmp-netstat.nse
/usr/share/nmap/scripts/snmp-processes.nse
/usr/share/nmap/scripts/snmp-sysdescr.nse
/usr/share/nmap/scripts/snmp-win32-services.nse
/usr/share/nmap/scripts/snmp-win32-shares.nse
/usr/share/nmap/scripts/snmp-win32-software.nse
/usr/share/nmap/scripts/snmp-win32-users.nse

Metasploit aux modules:

auxiliary/scanner/misc/oki_scanner
auxiliary/scanner/snmp/aix_version
auxiliary/scanner/snmp/arris_dg950
auxiliary/scanner/snmp/brocade_enumhash
auxiliary/scanner/snmp/cisco\_config\_tftp
auxiliary/scanner/snmp/cisco\_upload\_file
auxiliary/scanner/snmp/cnpilot\_r\_snmp_loot
auxiliary/scanner/snmp/epmp1000\_snmp\_loot
auxiliary/scanner/snmp/netopia_enum
auxiliary/scanner/snmp/sbg6580_enum
auxiliary/scanner/snmp/snmp_enum
auxiliary/scanner/snmp/snmp\_enum\_hp_laserjet
auxiliary/scanner/snmp/snmp_enumshares
auxiliary/scanner/snmp/snmp_enumusers
auxiliary/scanner/snmp/snmp_login

Onesixtyone:

- onesixtyone -c /usr/share/doc/onesixtyone/dict.txt 172.21.0.X

Snmp-check

- snmp-check 172.21.0.0 -c public

Impacket:

- python3 samdump.py SNMP 172.21.0.0