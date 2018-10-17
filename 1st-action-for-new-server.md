# 1st task for new server

- add 2nd disk on softlayer
~~~
fdisk -l
fdisk /dev/xvdc
mkfs.ext4 /dev/xvdc1
mount
~~~