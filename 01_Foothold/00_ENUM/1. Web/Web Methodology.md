# Attack web application

### For full WebPenTesting stuff see Dir

First find out what's there
**Chrome Webapplyzer - See if anything sticks out based on tech stack...**

1.  Injection
2.  Broken Authentication
3.  Sensitive Data Exposure
4.  XML External Entities (XXE)
5.  Broken Access Control
6.  Security Misconfiguration
7.  Cross-Site Scripting (XSS)
8.  Insecure Deserialization
9.  Using Components with Known Vulnerabilities
10. Insufficient Logging & Monitoring

## [https://www.owasp.org/index.php/Category:OWASP\_Top\_Ten_Project](https://www.owasp.org/index.php/Category:OWASP_Top_Ten_Project)

## Web application Enumeration

- Programming language and frameworks
- Web server software
- Database software
- server OS

### Inspecting URLs

php
For example, a Java-based web application might use .jsp, .do, or .html.
concept of routes, map uri to a section of code

### Inspecting Page Content

Open Debugger, can pretty code content

Right click -> insepct Element

View Response headers:

- proxy
- firefox network tool

Server header somtimes reveal the server app and version number
Header start with `X-` are non-standard HTTP headers, can reveal additional info

### Inspecting sitemaps

most common site maps files:
`robots.txt`
`sitemap.xml`

### Locating administration consoles

Two common examples are the manager application for Tomcat and phpMyAdmin for MySQL hosted at /manager/html and /phpmyadmin respectively.

## Web application assessment tools

### DIRB

web content scanner, use wordlist.
`dirb http://www.megacorpone.com -r -z 10`
-r to scan non-recursively, and -z 10 to add a 10 millisecond delay to each request

### Burp suite

foxyproxy firefox addon
add ca certificate for burpsuite
proxy->option->regenerate ca certificate
goto browser, enable proxy, open http://burp -\> CA certificate to download crt file, import certificate
send to repeater, send single request

### Nikto

webserver scanner - not intend to stealth itself, send info in user-agent to identify itself
-maxtime
-T which types of tests

```
nikto -host=megacorpne.com -maxtime=30s
```

Exploiting

### exploting admin consoles

Windows client -> XAMPP, start apache and mysql

```
dirb http://10.11.0.22 -r
==> DIRECTORY: http://10.11.0.22/phpmyadmin/
```

⧉ Copied!

### Burp intruder

`<input type="hidden" name="set_session" value="7r8oiuuoofdtcgc7ao731o0tcc" />`
`<input type="hidden" name="token" value="K&amp;$&amp;FR1\.cQ4)QW(" />`

send to intruder
select positions, type: Pitchfork, allowing us to set a unique payload list for each position.