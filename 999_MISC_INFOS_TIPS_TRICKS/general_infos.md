MISC - Tricks

# Checking program/binary protections
checksec --file ./binary

# Getting more informations about an ELF
readelf -a ./binary

# Gettign system() adress
# Can be simple
objdump -D ./binary | grep system

# Getting known string ("GNU" for example)
# Look for
objdump -s ./binary


# Getting padding using pwntools
>> from pwn import *

# Generate pattern
>> cyclic(400)

## Find offset where SIGSEGV
>> find_cyclic('<RSPoffset>')
>> find_cyclic('yaac')


# You can list gadgets using ROPGadgets.py
# And then look for one you want
./ROPgadget.py --binary ./binary
