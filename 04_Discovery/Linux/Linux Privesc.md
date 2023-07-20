Recon and Enumeration
=====================

## Resources


https://blog.g0tmi1k.com/2011/08/basic-linux-privilege-escalation/

 Tmux shortcuts
https://gist.github.com/MohamedAlaa/2961058

## Basics

```bash
hostname
whoami
id
ifconfig
uname -a
uname -m
``` 
Check version using 
      
      searchsploit -w distrib
      searchsploit -w kernel_version

- Test trivials login combos

      /etc/passwd
      su login=passwordinaison
- Look for known machines

      arp -a

- Network configuration
    
```bash
/sbin/ifconfig -a
cat /etc/network/interfaces
cat /etc/sysconfig/network
cat /etc/resolv.conf
cat /etc/sysconfig/network
cat /etc/networks
iptables -L
hostname
dnsdomainname
```

<br>

Linux Distro

      cat /etc/issue
      cat /etc/*-release
      cat /etc/lsb-release
      cat /etc/redhat-release;

Environment variables

      cat /etc/profile
      cat /etc/bashrc
      cat ~/.bash_profile
      cat ~/.bashrc
      cat ~/.bash_logout
      env
      set

Search for filename, path, user, group, mode as columns

      find /home -printf -type f "%f\t%p\t%u\t%g\%m\n" 2>/dev/null | column -t

Services

      netstat -lantp
      lsof -i
      sockstat -l
      ss -lantp

Find printers

      lpstat -a

Network

      netstat -tunlp

Check sudo rights
      
      sudo -l

User bash history  
      
      cat ~/.bash_history
      cat ~/.nano_history
      cat ~/.atftp_history
      cat ~/.mysql_history
      cat ~/.php_history

Writable configuration files

      find /etc/ -writable -type f 2>/dev/null

Compilers

      which gcc g++ cc

Look for SUID or GUID binaries

      find / -perm -g=s -o -perm -4000 ! -type l -maxdepth 6 -exec ls -ld {} 2>/dev/null
      find / -perm -1000 -type d 2>/dev/null
      find / -perm -g=s -type f 2>/dev/null

ARP cache
      
      cat /proc/net/arp

## Pspy

 pspy is a command line tool designed to snoop on processes without need 
 for root permissions. It allows you to see commands run by other 
 users, cron jobs, etc. as they execute.
https://github.com/DominicBreuker/pspy

## Usefull scripts


https://github.com/pentestmonkey/unix-privesc-check
https://raw.githubusercontent.com/sleventyeleven/linuxprivchecker/master/linuxprivchecker.py

https://raw.githubusercontent.com/rebootuser/LinEnum/master/LinEnum.sh

 Check for root cron jobs
https://github.com/codingo/OSCP-2//BASH/CronJobChecker.sh

 LinuxPrivCheck
https://github.com/codingo/OSCP-2//BASH/LinuxPrivCheck.sh

 Clear and efficient
https://github.com/diego-treitos/linux-smart-enumeration

## Swap Digger


https://github.com/sevagas/swap_digger

> swap_digger is a bash script used to automate Linux swap analysis for post-exploitation or forensics purpose. It automates swap extraction and searches for Linux user credentials, Web form credentials, Web form emails, HTTP basic authentication, WiFi
SSID and keys, etc.

 ./swap_digger.sh [ OPTIONS ]
```
 Options : 
  -x, --extended    Run Extended tests on the target swap to retrieve other interesting data
        (web passwords, emails, wifi creds, most accessed urls, etc)
  -g, --guessing  Try to guess potential passwords based on observations and stats
        Warning: This option is not reliable, it may dig more passwords as well as hundreds false positives.
  -h, --help    Display this help.
  -v, --verbose Verbose mode.
  -l, --log Log all outputs in a log file (protected inside the generated working directory).
  -c, --clean Automatically erase the generated working directory at end of script (will also remove log file)
  -r PATH, --root-path=PATH   Location of the target file-system root (default value is /)
        Change this value for forensic analysis when target is a mounted file system.
        This option has to  be used along the -s option to indicate path to swap device.
  -s PATH, --swap-path=PATH   Location of swap device or swap dump to analyse
        Use this option for forensic/remote analysis of a swap dump or a mounted external swap partition.
        This option should be used with the -r option where at least /<root-path>/etc/shadow exists.
  -S, --swap-search   Search for all available swap devices (use for forensics).
```