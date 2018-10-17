# :snowman: etcd backup and recovery for multi-master :snowman:

### 0. prereq
~~~
apt-get install ansible
cp <install_dir>/cluster/hosts /etc/ansible/hosts
apt-get install jq
export endpoint=169.56.xx
export CLUSTER_DIR=/opt/ibm-cloud-private-3.1.0/cluster
~~~

### 1. backup etcd 
~~~
cd ~/icp-backup/scripts 
./backupEtcd.sh
ls -al /tmp/etcd* 
~~~

### 2. stop etcd   
~~~
ansible master -m command -a "mkdir -p /etc/cfc/podbackup"
ansible master -m shell -a "mv /etc/cfc/pods/*.json /etc/cfc/podbackup"
ansible master -m wait_for -a  "port=4001 state=stopped"
~~~

### 3. stop kubelet & remove etcd data 
~~~
ansible master,management -m service -a "name=kubelet state=stopped"
ansible master -m shell -a "mv  /var/lib/etcd /var/lib/old-etcd2"
~~~

### 4. copy backup file to other master node  
~~~
ansible master -m shell -a "ls -al /tmp/etcd* "
scp /tmp/etcd....  root@master01:/tmp/snapshot.db
scp /tmp/etcd....  root@master02:/tmp/snapshot.db
scp /tmp/etcd....  root@master03:/tmp/snapshot.db
~~~

### 5. restore to each master node  
~~~
ansible master -m script -a "./multimaster-etcd-restore.sh"
ansible master -m shell -a "mv /var/lib/etcd/restored/* /var/lib/etcd/"
~~~

### 6. start kubelet & etcd 
- docker restarting takes more than 5 min.
~~~
ansible master,management -m service -a "name=docker state=restarted"
ansible master,management -m script -a "./purge_kubelet_pods.sh"
ansible master,management -m service -a "name=kubelet state=started"
~~~

### 7. start etcd
~~~
ansible master -m shell -a "mv /etc/cfc/podbackup/etcd.json /etc/cfc/pods"
ansible master -m wait_for -a  "port=4001 state=started"
ansible master -m shell -a "mv /etc/cfc/podbackup/*.json /etc/cfc/pods"
ansible master -m shell -a "docker ps | grep etcd"
~~~

### :zap: Remarks

~~~
cd ~/icp-backup/scripts 
. ./etcd.sh
$etcdctl2 cluster-health

ansible master -m package -a "use=apt name=jq state=present"
~~~