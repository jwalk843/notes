Phishing Infrastructure (Gophish+Vultr)
=======================================

### General Informations


# Using a specific and dedicated machine is a good choice.
# It avoid being indexed and referenced as a phishing server for your own VPS

# All the server configuration is pulled from the following Github repository
# it provides a fully functionnal bash script to automate the process
https://github.com/n0pe-sled/Postfix-Server-Setup

# Generate typo & permutations on a domain name
https://dnstwister.report/


# Message Header Analyzer
https://mha.azurewebsites.net/


# The Spamhaus Project
# Interesting stats about The World's Most Abused TLDs...
https://www.spamhaus.org/statistics/tlds/


# Steal Credentials & Bypass 2FA Using noVNC
# And using Evilginx2
https://mrd0x.com/bypass-2fa-using-novnc/

### Debugging Tips / Important notes {#debugging-tips--important-notes}

##### Blacklisted/Spam domain


# If the domain is blacklisted / considered as spam, you can try to reference www.domain.fr, just update SSL certs

##### Capturing data


# Login form need to be in POST
# Variables have to be set

<form action="" method="post" name="form">
    <label>Username:</label>
    <input name="username" type="text"/>
    <label>Password:</label>
    <input name="password" type="password"/>
</form>

##### E-mail not sent / TLS {#e-mail-not-sent--tls}


# For TLS, you could need to set option as "may" instead of "encrypt"

# Another possible thing : postfix configuration
https://www.digitalocean.com/community/questions/is-this-a-spammer-trying-to-access-or-just-the-system

##### Incoherent raw events and results


# If raw events are incoherent compared to gophish results, it may be caused by the "Email Opened" tracker. 
# If images are unavailable for the recipient, the tracker could not be triggered.
# If so, the "E-mail Opened" event won't be generated but Gophish will increment the counter if "Clicked Link" is triggered.
# Solution : for these people, use the "Clicked Link" event as "E-mail Opened" for stats

##### Embedded Images


#  In the actually HTML, reference the image by typing <img src="cid:###">.
# The ### should be the EXACT file name uploaded like cat.png.
# This will not put the attachment in the "attachment bar" in Outlook but embed it directly where
# the image tag is located inline in the email without requiring the downloading of external resources.

#### Statistics


# Timeline
# Get sorted and unique data for users
# The awk part allows to manipulate/sort on only one part of the line
cat raw_events.csv | grep "Submitted" | cut -d ',' -f 1,2 | awk -F, '!seen[substr($1,1,8)]++'

# Dirty solution to deal with Gophish UTC hours
>>> events = open("CUTED_EVENTS","r")
>>> outfile = open("PARSED_EVENTS","w")
>>> for event in events:
...     tbl1 = event.split('T')
...     tbl2 = tbl1[1].split(':')
...     if tbl2[0] == "24":
...             realHour = "00"
...     else:
...             realHour = int(tbl2[0]) + 1
...     realEvent = tbl1[0] + 'T' + str(realHour) + ":" + tbl2[1] + ":" + tbl2[2]
...     outfile.write(realEvent)
...     print(realEvent)

#### Testing e-mail "spam" level


https://www.mail-tester.com/

# Making a text version of your HTML e-mail can also be usefull to avoid spam

### Domain Configuration


# First step is to buy a domain name
# Ex : totallylegit.eu

# internetbs, gandhi, OVH...
# InternetBS can be a good option because it is offshore

# Data has to be anonymous to avoid WHOIS leaks
# Configure to not disclose any e-mail
# Also possible to use fake but realistic Names, Adresses...


# If using Vultr, change DNS in the management of the domain name
ns1.vultr.com
ns2.vultr.com

### VPS (Vultr) general configuration

#### Firewall


# Create a new Firewall group in Vultr and link it to the VPS
# Default behavior is dropping everything

# Create accept rules for SSH connections for your public IP adresses
ACCEPT TCP 2233 xxx.xxx.xxx.xxx/32
ACCEPT TCP 2233 xxx.xxx.xxx.xxx/32

# Create accept rules for Gophish administration panel
# Only for your public IP adresses
ACCEPT TCP 3333 xxx.xxx.xxx.xxx/32
ACCEPT TCP 3333 xxx.xxx.xxx.xxx/32

ACCEPT TCP 80 0.0.0.0/0
ACCEPT TCP 25 0.0.0.0/0

#### DNS


# 3 configuration parts : 
# - Create a new DNS Entry by setting server IP and domain name
# - Add the DNS entries provided by the server configuration part (see below)
# - Then, in the server settings, set the reverse DNS to the domain name used

### Server configuration

#### Global System Settings


# Prepare debian
sudo apt-get update
sudo apt-get upgrade 
sudo apt-get install unzip git
sudo apt-get remove -qq -y exim4 exim4-base exim4-config exim4-daemon-light
sudo rm -r /var/log/exim4/
sudo update-rc.d nfs-common disable > /dev/null 2>&1
sudo update-rc.d rpcbind disable > /dev/null 2>&1

sudo timedatectl set-timezone Europe/Paris

cat <<-EOF >> /etc/sysctl.conf
net.ipv6.conf.all.disable_ipv6 = 1
net.ipv6.conf.default.disable_ipv6 = 1
net.ipv6.conf.lo.disable_ipv6 = 1
net.ipv6.conf.eth0.disable_ipv6 = 1
net.ipv6.conf.eth1.disable_ipv6 = 1
net.ipv6.conf.ppp0.disable_ipv6 = 1
net.ipv6.conf.tun0.disable_ipv6 = 1
EOF

sysctl -p > /dev/null 2>&1

cat <<-EOF > /etc/hosts
127.0.1.1 asgard asgard
127.0.0.1 localhost
EOF

cat <<-EOF > /etc/hostname
asgard
EOF

#### SSL - Let's Encrypt


# In order to avoid errors, DNS should be set before setting SSL

# SSL / Let's Encrypt
# Certbot-auto is now deprecated
https://community.letsencrypt.org/t/certbot-auto-no-longer-works-on-debian-based-systems/139702/6

# Now
https://certbot.eff.org/lets-encrypt/debianbuster-apache

# Install
sudo snap install --classic certbot
sudo ln -s /snap/bin/certbot /usr/bin/certbot

# Set up
certbot certonly --standalone -d domain.fr --register-unsafely-without-email --agree-tos
sudo certbot certonly -d passwordupdate.support --manual --preferred-challenges dns

# Verify 
ls -l /etc/letsencrypt/live/domain.fr

# If you need to remove an SSL cert
sudo certbot delete --cert-name domain.fr

#### Postfix/Dovecot Installation & Configuration {#postfixdovecot-installation--configuration}


# Istallation Postfix / Dovecot
apt-get install -qq -y dovecot-imapd dovecot-lmtpd
apt-get install -qq -y postfix postgrey postfix-policyd-spf-python
apt-get install -qq -y opendkim opendkim-tools
apt-get install -qq -y opendmarc
apt-get install -qq -y mailutils

primary_domain = "<DOMAIN>"


cat <<-EOF > /etc/postfix/main.cf
smtpd_banner = \$myhostname ESMTP \$mail_name (Debian/GNU)
biff = no
append_dot_mydomain = no
readme_directory = no
smtpd_tls_cert_file=/etc/letsencrypt/live/<DOMAIN>/fullchain.pem
smtpd_tls_key_file=/etc/letsencrypt/live/<DOMAIN>/privkey.pem
smtpd_tls_security_level = may
smtp_tls_security_level = encrypt
smtpd_tls_session_cache_database = btree:\${data_directory}/smtpd_scache
smtp_tls_session_cache_database = btree:\${data_directory}/smtp_scache
smtpd_relay_restrictions = permit_mynetworks permit_sasl_authenticated defer_unauth_destination
myhostname = ${primary_domain}
alias_maps = hash:/etc/aliases
alias_database = hash:/etc/aliases
myorigin = /etc/mailname
mydestination = ${primary_domain}, localhost.com, , localhost
relayhost =
mynetworks = 127.0.0.0/8 [::ffff:127.0.0.0]/104 [::1]/128 ${relay_ip}
mailbox_command = procmail -a "\$EXTENSION"
mailbox_size_limit = 0
recipient_delimiter = +
inet_interfaces = all
inet_protocols = ipv4
milter_default_action = accept
milter_protocol = 6
smtpd_milters = inet:12301,inet:localhost:54321
non_smtpd_milters = inet:12301,inet:localhost:54321
EOF


cat <<-EOF >> /etc/postfix/master.cf
submission inet n       -       -       -       -       smtpd
 -o syslog_name=postfix/submission
 -o smtpd_tls_wrappermode=no
 -o smtpd_tls_security_level=encrypt
 -o smtpd_sasl_auth_enable=yes
 -o smtpd_recipient_restrictions=permit_mynetworks,permit_sasl_authenticated,reject
 -o milter_macro_daemon_name=ORIGINATING
 -o smtpd_sasl_type=dovecot
 -o smtpd_sasl_path=private/auth
EOF


mkdir -p "/etc/opendkim/keys/${primary_domain}"
cp /etc/opendkim.conf /etc/opendkim.conf.orig

cat <<-EOF > /etc/opendkim.conf
Domain                               *
AutoRestart                      Yes
AutoRestartRate              10/1h
Umask                                    0002
Syslog                               Yes
SyslogSuccess                    Yes
LogWhy                               Yes
Canonicalization         relaxed/simple
ExternalIgnoreList       refile:/etc/opendkim/TrustedHosts
InternalHosts                    refile:/etc/opendkim/TrustedHosts
KeyFile                              /etc/opendkim/keys/${primary_domain}/mail.private
Selector                         mail
Mode                                 sv
PidFile                              /var/run/opendkim/opendkim.pid
SignatureAlgorithm       rsa-sha256
UserID                               opendkim:opendkim
Socket                               inet:12301@localhost
EOF


cat <<-EOF > /etc/opendkim/TrustedHosts
127.0.0.1
localhost
${primary_domain}
${relay_ip}
EOF


cd "/etc/opendkim/keys/<DOMAIN>" || exit

opendkim-genkey -s mail -d "<DOMAIN>"

echo 'SOCKET="inet:12301"' >> /etc/default/opendkim
chown -R opendkim:opendkim /etc/opendkim

cat <<-EOF > /etc/opendmarc.conf
AuthservID ${primary_domain}
PidFile /var/run/opendmarc/opendmarc.pid
RejectFailures false
Syslog true
TrustedAuthservIDs ${primary_domain}
Socket  inet:54321@localhost
UMask 0002
UserID opendmarc:opendmarc
IgnoreHosts /etc/opendmarc/ignore.hosts
HistoryFile /var/run/opendmarc/opendmarc.dat
EOF

mkdir "/etc/opendmarc/"
echo "localhost" > /etc/opendmarc/ignore.hosts
chown -R opendmarc:opendmarc /etc/opendmarc
echo 'SOCKET="inet:54321"' >> /etc/default/opendmarc

cat <<-EOF > /etc/dovecot/dovecot.conf
disable_plaintext_auth = no
mail_privileged_group = mail
mail_location = mbox:~/mail:INBOX=/var/mail/%u

userdb {
  driver = passwd
}

passdb {
  args = %s
  driver = pam
}

protocols = " imap"
protocol imap {
  mail_plugins = " autocreate"
}

plugin {
  autocreate = Trash
  autocreate2 = Sent
  autosubscribe = Trash
  autosubscribe2 = Sent
}

service imap-login {
  inet_listener imap {
    port = 0
  }
  inet_listener imaps {
    port = 993
  }
}

service auth {
  unix_listener /var/spool/postfix/private/auth {
    group = postfix
    mode = 0660
    user = postfix
  }
}

ssl=required
ssl_cert = </etc/letsencrypt/live/${primary_domain}/fullchain.pem
ssl_key = </etc/letsencrypt/live/${primary_domain}/privkey.pem
EOF

echo "<user>: root" >> /etc/aliases

service postfix restart
service opendkim restart
service opendmarc restart
service dovecot restart

service postfix status
service opendkim status
service opendmarc status
service dovecot status

#### Aliases


# Assign an e-mail to a user
echo "<EMAIL>: <USER>" >> /etc/aliases

#### DNS Configuration


" If outlook"

extip=$(ifconfig|grep 'Link encap\|inet '|awk '!/Loopback|:127./'|tr -s ' '|grep 'inet'|tr ':' ' '|cut -d" " -f4)
domain=$(ls /etc/opendkim/keys/ | head -1)
fields=$(echo "${domain}" | tr '.' '\n' | wc -l)
dkimrecord=$(cut -d '"' -f 2 "/etc/opendkim/keys/${domain}/mail.txt" | tr -d "[:space:]")

cat <<-EOF > dnsentries.txt
DNS Entries for ${domain}:

====================================================================
Namecheap - Enter under Advanced DNS

Record Type: A
Host: @
Value: ${extip}
TTL: 5 min

Record Type: TXT
Host: @
Value: v=spf1 ip4:${extip} -all
TTL: 5 min

Record Type: TXT
Host: mail._domainkey
Value: ${dkimrecord}
TTL: 5 min

Record Type: TXT
Host: _dmarc
Value: v=DMARC1; p=reject
TTL: 5 min

Change Mail Settings to Custom MX and Add New Record
Record Type: MX
Host: @
Value: ${domain}
Priority: 10
TTL: 5 min
EOF
cat dnsentries.txt


# Then, these records have to be manually set in the Vultr Web Console (DNS Settings)

#### Test configuration


# It is possible to make a simple first test using the command line
mail target@example.com 

# And then follow the prompt 
https://mxtoolbox.com/
https://intodns.com

### Gophish

#### Installation and Configuration


# Download the latest version
# Check for release on their github
wget https://github.com/gophish/gophish/releases/download/v0.8.0/gophish-v0.8.0-linux-64bit.zip
unzip gophish-v0.8.0-linux-64bit.zip

# Apply good ermissions to the configuration file
chmod 640 config.json

# Self Signed Certificate (Don't usually need if you use Let's Encrypt)
openssl req -newkey rsa:2048 -nodes -keyout gophish.key -x509 -days 365 -out gophish.crt

# By default, normal users can't bind ports <1024 to services
# So, in order to get the phishing server on port 443
# And to avoid running as root, yo can set capabilities
sudo setcap cap_net_bind_service=+ep /home/user/gophish/gophish

# You can also use iptables rules to forward local port

# Logging can be nice
mkdir logs

# Starting
./gophish

# Default credentials are admin/gophish
# Update as soon as possible


cp /etc/letsencrypt/live/${primary_domain}/fullchain.pem ${primary_domain}.crt
cp /etc/letsencrypt/live/${primary_domain}/privkey.pem ${primary_domain}.key


{
    "admin_server": {
        "listen_url": "0.0.0.0:3333",
        "use_tls": true,
        "cert_path": "${primary_domain}.crt",
        "key_path": "${primary_domain}.key"
    },
    "phish_server": {
        "listen_url": "0.0.0.0:443",
        "use_tls": true,
        "cert_path": "${primary_domain}.crt",
        "key_path": "${primary_domain}.key"
    },
    "db_name": "sqlite3",
    "db_path": "gophish.db",
    "migrations_prefix": "db/db_",
    "contact_address": "",
    "logging": {
        "filename": "logs/testLogs.log"
    }
}

#### Create and manage campaigns

    # STEP 1 : Target groups
    # Create your pool of target
    # Users & Groups > New grouo
    # It is possible set argets manually or provide CSV file

    # STEP 2 : Template e-mail
    # Manually through the HTML Editor or import an existing template
    # Think about adding a tracking image (checkbox)
    # You can also use variables

    {{.FirstName}},

    The password for {{.Email}} has expired. Please reset your password here.

    Thanks,
    Morning Catch IT Team


    # Template reference
    {{.RId}} The target's unique ID
    {{.FirstName}} The target's first name
    {{.LastName}} The target's last name
    {{.Position}} The target's position
    {{.Email}} The target's email address
    {{.From}} The spoofed sender
    {{.TrackingURL}} The URL to the tracking handler
    {{.Tracker}} An alias for <img src="{{.TrackingURL}}"/>
    {{.URL}} The phishing URL
    {{.BaseURL}} The base URL with the path and rid parameter stripped. Useful for making links to static files.

    # STEP 3 : Landing page
    # Manual creation or copy an existing page using the "Import from site"
    # Don't forget to check capture submitted data / passwords
    # Don't forget to set redirection to the real page

    # STEP 4 : Sending profile
    # The SMTP Relay
    # "From" e-mail adress needs to be valid one (for example contact@domain.fr)
    # SMTP Server format (VPS used) : host:port (localhost:25)
    # You can send test e-mails

    # STEP 5 : Campaign
    # Select email template, landing page, sending profile and groups from previously created
    # The URL param correspond to the "{{.URL}}" value, or you can manually set it

    # You can program automatic startup using the "Launch Date"
    # Also usefull to spread e-mail sent in several waves using the "Send Emails"

    # 2 ways of export
    # Results (Max event for each person) : id, email, first_name, last_name, position, status, ip, latitude, longitude
    # Raw (All events sorted by time)

