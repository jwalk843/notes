# Fully Interactive TTY Shell

# RLWRAP allows you to interface local and remote keyboards
# It gives access to keyboard arrows and history
rlwrap ncat -lvp port

## Another way to get a better shell script is almost everytime present on the machine
/usr/bin/script -qc /bin/bash /dev/null

### Fully Interactive TTY Shell {#fully-interactive-tty-shell}


## Using STTY
### In reverse shell
```
$ python -c 'import pty; pty.spawn("/bin/bash")'
Ctrl-Z
```

## In Kali
```
stty raw -echo
fg
```

## In reverse shell
```
reset
export SHELL=bash
export TERM=xterm-256color
stty -raw echo
# or
stty rows <num> columns <cols>
```


# Using os.system
`echo os.system('/bin/bash')`

# Using interactive sh
`/bin/sh -i`

# Using Perl, Ruby or Lua
```
perl -e 'exec "/bin/sh";'
perl: exec "/bin/sh";
ruby: exec "/bin/sh";
lua: os.execute('/bin/bash')
```

# Using Vi
```
:!bash
:set shell=/bin/bash:shell
```



