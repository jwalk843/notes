Bug Bounty Tips
===============

### General


# This section will be mainly used to store information found on Twitter
# About some bug bounty tips & web tricks

### Password Reset


https://twitter.com/HusseiN98D/status/1254888748216655872/photo/1

# 1/ Include controlled mail as a second parameter
# 2/ Bruteforce reset token
# 3/ Try to use a reset token on another account
# 4/ Try to figure out how token are generated

### Payloads


# Top 25 XSS Dorks
https://twitter.com/trbughunters/status/1276851918946603013/photo/1


# E-mail fields are usually less sanitized or filtered
# It can be a good candidate for payloads

### 401/403 Bypass {#401403-bypass}


https://twitter.com/h4x0r_dz/status/1317218511937261570/photo/1

# GET /api/getUser --> 403
# GET / + X-Original-URL : /api/GetUser --> 200

# GET /api/getUser --> 403
# GET / + Referer : https://site.com/api/GetUser --> 200
# or
# GET /api/getUser + Referer : https://site.com/api/GetUser --> 200


https://github.com/laluka/bypass-url-parser
# Tool that tests MANY url bypass to reach a 40X protected page.

./bypass.sh http://127.0.0.1/blocked/path/


# A bash script to bypass "403 Forbidden" responses with well-known methods discussed in #bugbountytips
https://github.com/lobuhi/byp4xx

./byp4xx.sh [OPTIONS] http(s)://url/path

OPTIONS:
  -c Return the entire curl command if response is 200
  -r Redirects if the response is 3XX

### Authorization checks


> Authorization checks made easy with Firefox Containers
> and Authorize extension
> https://blog.rootrwx.com/post/2021-01-11-auth-checks-made-easy/
















