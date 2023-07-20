## KILL CHAIN

Data is useless and requires a process to apply context.
Context rich data is Information.  Information is target-able and requires protection.

### 1\. Recon:

- **Gather information** - 
    *Passive and Active techniques -* *Scan public sources, forums, and social medi*a 
    *Looking for data relating to:*
    - **Employees**:
        - Role and Data Access
        - Possible IDs and Associated Emails
            - Email Password Breaches
        - Linkages indirect and direct
            - People - and Assocated Metrics for Datasets.
    - **Infrastructure** \- Scan public sources, forums, and social media.
        - **Software**
            - Scope and Function
            - Origin
                - Source Control
                - Supply Chain
                - Ownership
        - **Hardware - What exists, how**
            - Scope and Function
            - Origin
                - Source Control
                - Supply Chain
                - Ownership
    - \*\*Analyze information - \*\*
        - Associate
            - Dates
            - Sources
            - Compatibility

### 2\. Weaponize:

- Create malware:
    - Design and develop malware or tools that will be used in the attack. This can involve programming, testing, and refining the malware.
    - Modify existing malware:
        - Change the code, payload, or evasion techniques of existing malware to suit the attacker's needs and make it more effective.

### 3\. Delivery:

- Select delivery method:
    - email
    - social media
    - website.
- Craft message:
    - Determine the most effective message for the selected delivery method
        - For email, craft a convincing phishing email that appears to be from a legitimate source and entices the recipient to click on a malicious link or attachment.
        - For social media, craft a message that appears to be from a legitimate source and entices the recipient to click on a malicious link or attachment.
        - For website, create a fake website that appears to be legitimate and entices the victim to enter sensitive information.
- Develop phishing emails (if email delivery method is selected):
    - Craft a convincing email that appears to be from a legitimate source and entices the recipient to click on a malicious link or attachment.
- Identify vulnerabilities (if website delivery method is selected):
    - Scan the target website to identify potential vulnerabilities that can be exploited to deliver the malware.

### 4\. Exploitation:

- Exploit vulnerabilities:
    - Determine the most effective method for exploiting the identified vulnerabilities
        - Run code to exploit software or hardware vulnerabilities.
        - Exploit a misconfigured system.
        - Use a zero-day exploit.
- Run code:
    - Execute the malware on the target system.

### 5\. Installation:

- Establish persistence:
    - Install additional tools or malware to maintain persistence on the target system
        - Create backdoors for remote access to the target system.
        - Create new accounts or modify existing accounts to maintain access to the system.
- Move laterally:
    - Move laterally through the network by installing tools or malware on additional systems
        - Exploit vulnerabilities on additional systems to install the malware.
        - Perform password cracking or pass-the-hash attacks to gain access to additional systems.

### 6\. Command and Control:

- Establish communication:
    - Identify the appropriate command and control (C2) server to use for remote control of the compromised system.
    - Establish a connection to the C2 server using the appropriate protocol (e.g., HTTP, HTTPS, DNS).
- Avoid detection:
    - Implement encryption to hide communication with the C2 server.
    - Use proxy servers or anonymization techniques to disguise the origin of the traffic.

### 7\. Actions on Objectives:

- Steal data:
    - Determine the types of data that are most valuable to the attacker
        - Personally identifiable information.
        - Financial information.
        - Intellectual property.
    - Use keyloggers, scraping tools, or other techniques to extract the desired data from the target system.
    - Encrypt and exfiltrate the stolen data to a remote server under the control of the attacker.
- Disrupt services:
    - Identify the most critical services provided by the target system
        - Web server.
        - Database server.
        - Email server.
    - Use denial-of-service (DoS) attacks, change configurations