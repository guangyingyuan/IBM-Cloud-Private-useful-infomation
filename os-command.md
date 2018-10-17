## linux os command
~~~
cat /proc/cpuinfo | awk '/^processor/{print $3}'
free -m
scp /etc/docker/certs.d/mycluster.icp:8500/ca.crt root@mg01:/etc/docker/certs.d/mycluster.icp:8500/  
apt-get install docker-ce=18.03.1~ce-0~ubuntu
~~~

- tar with timestamp
~~~
export DATE=$(date '+%Y-%m-%d_%H-%M-%S')
cd /var/lib/icp/mongodb/workdir/Backup/
tar -zcvf /mongodump-$DATE.tar.gz mongodump/
rm -R mongodump
tar -xvf /mongodump-2018-09-18_10.tar.gz -C /var/lib/icp/mongodb/workdir/Backup/  
~~~

- ssh setup 
~~~
ssh-keygen -b 4096 -f ~/.ssh/id_rsa -N ""
cat ~/.ssh/id_rsa.pub | sudo tee -a ~/.ssh/authorized_keys
ssh-copy-id -i ~/.ssh/id_rsa.pub <user>@<node_ip_address>
~~~

- ucp install        https://docs.docker.com/ee/ucp/admin/install/  
~~~
docker image pull docker/ucp:3.0.2
docker container run --rm -it --name ucp -v /var/run/docker.sock:/var/run/docker.sock docker/ucp:3.0.2 install --host-address 169.56.71.55 --interactive
~~~

- dtr install
~~~
docker container run -it --rm docker/dtr:2.5.0 install --ucp-node ucp.seo.co.kr --ucp-insecure-tls
~~~