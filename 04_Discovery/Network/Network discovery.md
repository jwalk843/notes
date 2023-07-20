### Network discovery

- Subdomains enumeration
    
    - Enumerate already found subdomains: [projectdiscovery/subfinder](https://github.com/projectdiscovery/subfinder): `subfinder -d hackerone.com`
    - Permutate subdomains: [infosec-au/altdns](https://github.com/infosec-au/altdns)
    - Bruteforce subdomains: [Josue87/gotator](https://github.com/Josue87/gotator)
    - Subdomain takeovers: [EdOverflow/can-i-take-over-xyz](https://github.com/EdOverflow/can-i-take-over-xyz)
- Network discovery
    
    - Scan IP ranges with `nmap`, [robertdavidgraham/masscan](https://github.com/robertdavidgraham/masscan) and [projectdiscovery/naabu](https://github.com/projectdiscovery/naabu)
    - Discover services, version and banners
- Review latest acquisitions
    
- ASN enumeration
    
    - [projectdiscovery/asnmap](https://github.com/projectdiscovery/asnmap): `asnmap -a AS45596 -silent`
- DNS Zone Transfer
    
    ```ps1
    host -t ns domain.local
    domain.local name server master.domain.local.
    
    host master.domain.local        
    master.domain.local has address 192.168.1.1
    
    dig axfr domain.local @192.168.1.1
    ```