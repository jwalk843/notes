
https://bitsdeep.com/posts/attacking-rsa-for-fun-and-ctf-points-part-1/

# Think about factordb.com to retrieve p and q from n

### Theory

```
# Used
# c  → cyphertext
# m → Plaintext message converted as a number
# e  → public exponent
# d  → private exponent
# n  → modulo => p * q

# Encrypt
c = (m^e)[n] => pow(m,e,n)
    
# Decrypt
m = (c^d)[n] => pow(c,d,n)


# 5 times encryption
c = m^(e1 * e2 * e3 * e4 * e5)
m = c^(d1 * d2 * d3 * d4 * d5)

# Can get n and e from openssl like this
cat alice_pubkey.pem | openssl rsa -pubin -inform PEM -text -noout
```

## Tips & Tricks

```
# Getting clear when you have c, d, n
text = pow(c, d, n)   # équivaut à text = (c^d)[n]
result = hex(text)
result = result.replace("0x", "").replace("L", "")
print(result.decode('hex'))
    
# Convert ASCII message to INT
int(binascii.hexlify(m),16)
    

# Convert INT message to ASCII
binascii.unhexlify(hex(m).split('x')[1])
        
        
# Get n and e from a public key in Python
from Crypto.PublicKey import RSA

key = RSA.importKey(open("public_key_path.pem").read())
n = key.n
e = key.e
```

### Attacks : Public Key + Message {#attacks--public-key--message}
```

# Factorization Attack:
→ When n is small, go for factordb.com
        
# Fermat Attack
→ When n is quite small

# Low Exponent Attack: 
→ Usefull when e=3 and n is quite big because pow(m,e,n) == pow(m,e)

# ROCA: 
→ Usable when RSA key has 512 bits long n

# Twin Primes: 
→ q = p + 2
→ Usefull is most cases when n is too bid and others attacks doesn\'t work

# Boneh Durfee Attack:
→ Allows to go slightly faster then Wiener Attack because d < n^0.292
```

### Attacks : Several public keys {#attacks--several-public-keys}

```
# Chinese Remainder Attack
→ Usable when 3 messages have the same exponent (c= m^3 mod (n^b * n^c * n^d))
→   chinese_reminder.py

# Common Modulus Attack:
→ Usable when you have 2 messages, 2 public keys and n1 == n2

# Wiener Attack:
→ Usable when private exponen d is quite small compared to N (d < n^(1/4)) 
→ https://github.com/rk700/attackrsa
→ attackrsa -t wiener -n N_VALUE -e E_VALUE
```

### Remote Service allowing to decrypt

```
# Decipher Oracle in Python
from pwn import *
from Crypto.Util.number import *

n = <>
e = <>

c1 = <>
c2 = pow(2, e, n)

c = c1*c2

# If it's a process that is the Oracle
r = process("./oracle")

# If it's a socket that is the Oracle
# r = remote("ip",port)

r.recvuntil("where_firs_message_stop")
r.sendline(str(c))
res = r.recvline()
res = r.recvline()
dec = long_to_bytes(long(res.split(" ")[-1]) / 2)
print(dec)
```