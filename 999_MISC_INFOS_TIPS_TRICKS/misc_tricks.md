MISC
====


# Security Headers
https://securityheaders.com

# /!\ HTTP POST Requests needs two carriage return at the end to be valid !

# Firefox extension for security
https://addons.mozilla.org/fr/firefox/addon/hacktools/

### Qsreplace


https://github.com/tomnomnom/qsreplace
# Accept URLs on stdin, replace all query string values with a user-supplied value
# only output each combination of query string parameters once per host and path.

cat urls.txt | qsreplace newval
https://example.com/path?one=newval&two=newval
https://example.com/pathtwo?one=newval&two=newval
https://example.net/a/path?one=newval&two=newval

### JSON Web Tokens (JWT)


https://jwt.io/

# If RSA signed, you can try to set HS256
# You need to get the public key
# By getting 2 tokens, you can calculate the public key
# More info
https://crypto.stackexchange.com/questions/62904/rsa-deduce-the-public-key-from-message-signed-message

### HTTP Methods


# With this script, you can test various HTTP methods against an URL.
# Cool and usefull tool by Shutdown for the recon phase
https://github.com/ShutdownRepo/httpmethods

HTTP Methods Tester, v1.0.2
usage: httpmethods.py [-h] -u URL [-v] [-q] [-k] [-w WORDLIST]
                              [-t THREADS] [-j JSONFILE]

This Python script can be used for HTTP verb tampering to bypass forbidden access, and for HTTP methods enumeration to find dangerous enabled methods like PUT

optional arguments:
  -h, --help            show this help message and exit
  -u URL, --url URL     e.g. https://example.com:port/path
  -v, --verbose         verbosity level (-v for verbose, -vv for debug)
  -q, --quiet           Show no informations at all
  -k, --insecure        Allow insecure server connections when using SSL (default: False)
  -w WORDLIST, --wordlist WORDLIST
                        HTTP methods wordlist (default: default_wordlist.txt)
  -t THREADS, --threads THREADS
                        Number of threads (default: 5)
  -j JSONFILE, --jsonfile JSONFILE
                        Save results to specified JSON file.

### NIP IO / SSRF Redirections {#nip-io--ssrf-redirections}


# Redirections / Bypass filters for SSRF
# <anything>.<IP>.nip.io will redirect to the <IP> you specify
1.1.1.1.nip.io  will resolve to 1.1.1.1

### Spring Boot


# Spring Boot / Metrics
/metrics → Endpoint showing tons of endpoints
/heapdump → Get a memory dump of the application
Exploit → jhat -port 7401 -J-Xmx4G heapdump2016-12-27-13-54-live2196484565712626494.hprof

### VERB tampering


# Test for others HTTP methods (verb tampering)
# Bypass .htaccess
curl -X COUCOU <target>

### Install files


# Install files
# You can check for backup files using the following extensions
.backup, .bck, .old, .save, .bak, .sav, ~, .copy, .old, .orig, .tmp, .txt, .back, .bkp, .bac, .tar, .gz, .tar.gz, .zip, .rar

# Check headers
# They often give informations
curl -v <target>

### Parameter override


# If some JSON objects are in the response but not in the request
# Try do add them in the request, you could override properties
{"id":"7"} => {"id":"7", "admin":false}
{"id":"7", "admin":true} => {"id":"7", "admin":true}

### O365 Testing

# If facing OWA applications, you can check if the target is using O365 or on premise applications
# If the following is "Managed", the targets uses o365
https://login.microsoftonline.com/getuserrealm.srf?login=targetemail@company.com&xml=1

### Payload Transformations


https://twitter.com/jobertabma/status/1257126413699670016

# Transformation tool
https://github.com/jobertabma/transformations
https://transformations.jobertabma.nl/

# It helps finding processes made to an input, given then output
# Can be usefull for crafting

### Prototype Pollution


# Resources
https://labs.detectify.com/2021/06/08/what-is-a-prototype-pollution-vulnerability-and-how-does-page-fetch-help/
https://research.securitum.com/prototype-pollution-and-bypassing-client-side-html-sanitizers/

# Payloads
https://github.com/BlackFan/client-side-prototype-pollution

# Example
# The payload parameter pollutes the prototype, making it available on the messages object
# The message parameter is used to choose our payload to be displayed
?__proto__[payload]=<img%20src%20onerror=alert(document.domain)>&message=payload

# Another example
```
const user = { userid: 123 };
if (user.admin) {
    console.log('You are an admin');
}
Object.prototype.admin = true;
const user = { userid: 123 };
if (user.admin) {
    console.log('You are an admin'); // this will execute
}
```

# Page-fetch can be used to detect prototype pollution
https://github.com/detectify/page-fetch

# Usage
echo https://detectify.com | page-fetch 
GET https://detectify.com/ 200 text/html; charset=utf-8 
GET https://detectify.com/site/themes/detectify/css/detectify.css?v=1619009098 200 text/css 
GET https://detectify.com/site/themes/detectify/img/detectify_logo_black.svg 200 image/svg+xml 
GET https://fonts.googleapis.com/css?family=Merriweather:300i 200 text/css; charset=utf-8 

# Execute Javascript
echo https://detectify.com | page-fetch -j 'document.querySelector("title").innerText' | grep ^JS
JS (https://detectify.com): Website vulnerability scanner - scan web assets | Detectify

# Check for XSS vulnerability
echo 'http://poc-tools-storage.s3-website.eu-west-2.amazonaws.com/pp.html?__proto__[testparam]=testval' | page-fetch -j 'window.testparam == "testval"? "vulnerable" : "not vulnerable"' | grep ^JS
JS (http://poc-tools-storage.s3-website.eu-west-2.amazonaws.com/pp.html?__proto__[testparam]=testval): vulnerable

