
### Retrieve AES Key and mount partition


# Dump AES key
aeskeyfind dump
```
# Result
8d 3f 52 7d e5 14 87 2f 59 59 08 95 8d bc 0e d1

# Mount partition
echo 8d3f527de514872f595908958dbc0ed1 | xxd -r -p > key.bin
sudo cryptsetup luksFormat --verify-passphrase --cipher=aes-ecb --hash=sha1 --key-size=128 --master-key-file=key.bin dump
sudo cryptsetup luksOpen dump test
sudo mount -t ext4 /dev/mapper/test /mnt/media
sudo ls /mnt/media/dir2
```
### Decipher encrypted aes256cbc file using openssl

```
# Key
d49af309a4c69382ff07bc6f83ba4c2595a7f086d3e5b69e119e2337cb75172d' ----> SHA-256 de 'mcsTnTld1dDn':

# Decipher
openssl enc -aes-256-cbc -d -in Confidentiel.jpg.enc -out Confidentiel.jpg -iv '0000000000000000' -K 'd49af309a4c69382ff07bc6f83ba4c2595a7f086d3e5b69e119e2337cb75172d'

```
