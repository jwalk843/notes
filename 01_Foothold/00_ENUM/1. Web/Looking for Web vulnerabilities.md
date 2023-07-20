## Looking for Web vulnerabilities

- Explore the website and look for vulnerabilities listed in this repository: SQL injection, XSS, CRLF, Cookies, ....
    
- Test for Business Logic weaknesses
    
    - High or negative numerical values
    - Try all the features and click all the buttons
- [The Web Application Hacker's Handbook Checklist](https://gist.github.com/gbedoya/10935137) copied from http://mdsec.net/wahh/tasks.html
    
- Subscribe to the site and pay for the additional functionality to test
    
- Inspect Payment functionality - [@gwendallecoguic](https://twitter.com/gwendallecoguic/status/988138794686779392)
    
    > if the webapp you're testing uses an external payment gateway, check the doc to find the test credit numbers, purchase something and if the webapp didn't disable the test mode, it will be free
    
    From https://stripe.com/docs/testing#cards : "Use any of the following test card numbers, a valid expiration date in the future, and any random CVC number, to create a successful payment. Each test card's billing country is set to U.S. "
    e.g :
    
    Test card numbers and tokens
    
    | NUMBER | BRAND | TOKEN |
    | --- | --- | --- |
    | 4242424242424242 | Visa | tok_visa |
    | 4000056655665556 | Visa (debit) | tok\_visa\_debit |
    | 5555555555554444 | Mastercard | tok_mastercard |
    
    International test card numbers and tokens
    
    | NUMBER | TOKEN | COUNTRY | BRAND |
    | --- | --- | --- | --- |
    | 4000000400000008 | tok_at | Austria (AT) | Visa |
    | 4000000560000004 | tok_be | Belgium (BE) | Visa |
    | 4000002080000001 | tok_dk | Denmark (DK) | Visa |
    | 4000002460000001 | tok_fi | Finland (FI) | Visa |
    | 4000002500000003 | tok_fr | France (FR) | Visa |
    

## References

- [\[BugBounty\] Yahoo phpinfo.php disclosure - Patrik Fehrenbach](http://blog.it-securityguard.com/bugbounty-yahoo-phpinfo-php-disclosure-2/)
- [Nmap CheatSheet - HackerTarget](https://hackertarget.com/nmap-cheatsheet-a-quick-reference-guide/)