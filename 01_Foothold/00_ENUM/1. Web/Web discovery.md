### Web discovery

- **Chrome Webapplyzer **
- Locate `robots.txt`, `security.txt`, `sitemap.xml` files
    
- Retrieve comments in source code
    
- Discover URL: [tomnomnom/waybackurls](/tmp/.mount_Joplin6WgL8E/resources/app.asar/github.com/tomnomnom/waybackurls "github.com/tomnomnom/waybackurls")
    
- Search for `hidden` parameters: [PortSwigger/param-miner](https://github.com/PortSwigger/param-miner)
    
- List all the subdirectories and files with `gobuster` or `ffuf`
    
    ```
    # gobuster -w wordlist -u URL -t threads
    ./gobuster -u http://example.com/ -w words.txt -t 10
    ```
    
    Find backup files with [mazen160/bfac](https://github.com/mazen160/bfac)
    
    ```
    bfac --url http://example.com/test.php --level 4
    bfac --list testing_list.txt
    ```
    
- Map technologies: Web service enumeration using [projectdiscovery/httpx](https://github.com/projectdiscovery/httpx) or Wappalyzer
    
    - Gather favicon hash, JARM fingerprint, ASN, status code, services and technologies (Github Pages, Cloudflare, Ruby, Nginx,...)
- Take screenshots for every websites using [sensepost/gowitness](https://github.com/sensepost/gowitness)
    
- Automated vulnerability scanners
    
    - [projectdiscovery/nuclei](https://github.com/projectdiscovery/nuclei): `nuclei -u https://example.com`
    - [Burp Suite's web vulnerability scanner](https://portswigger.net/burp/vulnerability-scanner)
    - [sullo/nikto](https://github.com/sullo/nikto): `./nikto.pl -h http://www.example.com`
- Manual Testing: Explore the website with a proxy:
    
    - [Caido - A lightweight web security auditing toolkit](https://caido.io/)
    - [ZAP - OWASP Zed Attack Proxy](https://www.zaproxy.org/)
    - [Burp Suite - Community Edition](https://portswigger.net/burp/communitydownload)