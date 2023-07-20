## Spawn a tty:

1.  rlwrap nc localhost 80
    
2.  rlwrap -r -f . nc
    

- socat file:`tty`,raw,echo=0 tcp-listen:12345
- /bin/sh -i
- /bin/bash -i
- python -c 'import pty; pty.spawn("/bin/sh")'
- perl -e 'exec "/bin/sh";'
- perl: exec "/bin/sh";
- ruby: exec "/bin/sh"
- lua: os.execute('/bin/sh')