LDAP & DNS
==========

### LDAP Null Bind Enumeration


# Syntax
ldapsearch -h "new" -p "pass" -x -b "ou=Group,DC=dc2,DC=xxx" -v

### DNS Zone Transfer


# Zone transfer using dig
dig @<ns> -p 54011 AXFR <host>


# Zone transfer using nslookup
nslookup -type=any
> set port <port>
> server <ns>   
> <host>

### DNS Spoofing


# Using bettercap
# In a local network, you can DNS queries using ARP spoofing

# Activate router mode on attacker machine
echo 1 > /proc/sys/net/ipv4/ip_forward

# Show machines in the LAN
> net.show

# Set target for ARP spoofing
> set arp.spoof.targets 192.168.5.99

# Launch attack
> arp.spoof on

# Set domain to spoof, spoof.all allow to also consider external requests and not only local ones
> set dns.spoof.domains apache.org
> set dns.spoof.all true
> dns.spoof on

# You can then create a local web server
> set http.server.path /var/www/html
> http.server on
