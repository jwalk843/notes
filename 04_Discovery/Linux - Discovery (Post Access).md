## Linux

Security Commands In Linux:

PID:
fuser -nv tcp 80 (list PID of process)
fuser -k -n tcp 80 (Kill Process of PID)

For Privilege Escalation Exploit search:

cat files.csv | grep -i linux | grep -i kernel | grep -i local | grep -v dos | uniq | grep 2.6 | egrep “<|<=” | sort -k3

find programs with a set uid bit

# find / -uid 0 -perm -4000

find things that are world writable

# find / -perm -o=w

find names with dots and spaces, there shouldn’t be any

# find / -name ” ” -print

# find / -name “..” -print

# find / -name “. ” -print

# find / -name ” ” -print

find files that are not owned by anyone

# find / -nouser

look for files that are unlinked

# lsof +L1

get information about procceses with open ports

# lsof -i

look for weird things in arp

# arp -a

look at all accounts including AD

# getent passwd

look at all groups and membership including AD

# getent group

list crontabs for all users including AD

# for user in $(getent passwd|cut -f1 -d:); do echo “### Crontabs for $user ####”; crontab -u $user -l; done

generate random passwords
cat /dev/urandom| tr -dc ‘a-zA-Z0-9-*!@#$%^&*()*+{}|:<>?=’|fold -w 12| head -n 4

find all immutable files, there should not be any

# find . | xargs -I file lsattr -a file 2>/dev/null | grep ‘^….i’

fix immutable files

# chattr -i file