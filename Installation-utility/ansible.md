## ansible
~~~
apt-get install ansible
vi /etc/ansible/hosts
ansible all -m command -a "ping -c 3 www.ibm.com"
~~~
