Global Tricks (sysadmin)
========================

### MISC / TIPS {#misc--tips}

# If you need to kill a specific process using a given port
sudo fuser -k 445/tcp

# You can use the following trick to easy navigate and select paths or others args
# $_ takes the last argument of the last simplec command executed
cd $_ 

# For example
mkdir my-folder && cd $_

# You can use xclip to automate clipping
# Can be usefull for long outputs (enum4linux, privcheck...)
cat /etc/resolv.conf | xclip -sel clip

# You can even create aliases
alias toclip="xclip -sel clip"
cat /etc/resolv.conf | toclip

### Parsing tips


# Log parsing cheatsheets
https://twitter.com/fr0gger_/status/1343867388660494337
https://twitter.com/fr0gger_/status/1346040749763293189
