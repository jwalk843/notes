#### Banner Grabbing HTTP:
GET / HTTP/1.1
Host: 192.168.0.10
User-Agent: SPOOFED-BROWSER
Referrer: 255S-wolf.com

##### Python:
python -c ‘import pty;pty.spawn(“/bin/bash”)’
python -m SimpleHTTPServer (Starting HTTP Server)

##### Brute force with Hydra:
hydra -l admin -P /root/Desktop/passwords -S X.X.X.X rdp (Self Explanatory)
