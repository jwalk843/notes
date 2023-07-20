Amazon Web Services (AWS)
=========================


https://github.com/toniblyx/my-arsenal-of-aws-security-tools

https://github.com/RhinoSecurityLabs/ccat
https://github.com/RhinoSecurityLabs/pacu
https://github.com/carnal0wnage/weirdAAL
https://github.com/Static-Flow/CloudCopy

# Cheatsheet
https://www.magnussen.funcmylife.fr/article_35

### GitLeaks (<https://github.com/zricethezav/gitleaks>) {#gitleaks-httpsgithubcomzricethezavgitleaks}


# Audit git repos for secrets keys
./gitleaks --repo=xxxxx --verbose --pretty

### Enumerate IAM (<https://github.com/andresriancho/enumerate-iam>) {#enumerate-iam-httpsgithubcomandresrianchoenumerate-iam}


# Enumerate the permissions associated with AWS credential set 
./enumerate-iam.py --access-key AKIA... --secret-key StF0q...

# enumerate-iam.py tries to brute force all API calls allowed by the IAM policy.
# The calls performed by this tool are all non-destructive (only get* and list* calls are performed).

### SQLmap proxy for AWS


https://github.com/puresec/lambda-proxy

# A simple utility to help test AWS Lambda functions for SQL Injection vulnerabilities
# Using a local HTTP proxy, which transforms the SQLMap HTTP-based attacks to AWS Lambda invoke calls.

# Run
$ python3 main.py

# Update request.txt, which is the file containing your Lambda function's event data, and run
$ sqlmap -r request.txt
