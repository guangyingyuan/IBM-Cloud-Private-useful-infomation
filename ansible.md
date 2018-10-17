## ansible
~~~
apt-get install ansible
vi /etc/ansible/hosts
cp icpinstall_dir/cluster/hosts /etc/ansible/hosts
~~~

~~~
export CLUSTER_DIR=/icp31/cluster
ansible master,management -i $CLUSTER_DIR/hosts -e @$CLUSTER_DIR/config.yaml --private-key=$CLUSTER_DIR/ssh_key -m script -a "./purge_kubelet_pods.sh"
ansible master,management -i $CLUSTER_DIR/hosts -e @$CLUSTER_DIR/config.yaml --private-key=$CLUSTER_DIR/ssh_key -m service -a "name=kubelet state=started"
ansible master -i $CLUSTER_DIR/hosts -e @$CLUSTER_DIR/config.yaml --private-key=$CLUSTER_DIR/ssh_key -m copy -a "src=/tmp/etcd.2018.09.12-10.09.32.db dest=/tmp/snapshot.db"
~~~

~~~
ansible all  -m command -a "ls -al  /etc/docker/certs.d/mycluster.icp:8500"
ansible all  -m command -a "mkdir -p /etc/docker/certs.d/mycluster.icp:8500"
ansible all  -m shell -a "docker ps | grep etcd"
ansible all -m command -a "ping -c 3 www.ibm.com"
~~~