# Linux Suid and Guids

```
# Find all SUID root files:

find / -user root -perm -4000 -print

# Find all SGID root files:

find / -group root -perm -2000 -print

# Find all SUID and SGID files owned by anyone:

find / -perm -4000 -o -perm -2000 -print

# Find all files that are not owned by any user:

find / -nouser -print

# Find all files that are not owned by any group:

find / -nogroup -print

# Find all symlinks and what they point to:

find / -type l -ls
```



