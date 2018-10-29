# :snowflake: ETCD backup and recovery 

### overall process for etcd backup and recovery    
<p align="center" >
<img width=600 src="https://github.com/moreal70/IBM-Private-Cloud-handsOn/blob/master/images/etcd-backup-recovery.jpg">
</p>

### 1. down load script
~~~
git clone https://github.com/ibm-cloud-architecture/icp-backup.git
~~~

### 2. backup etcd & check output
~~~
cd ~/icp-backup/scripts
export org=ibmcom
export repo=etcd
export tag=v3.2.14
export endpoint=10.10.13.xx
./backupEtcd.sh

ls -al /tmp/etcd*
~~~

### 3. stop etcd
~~~
docker ps | grep etcd
mv /etc/cfc/pods/etcd.json /etc/cfc/
ll /etc/cfc/
ll /etc/cfc/pods
~~~

### 4. restore etcd
~~~
cd ~/icp-backup/scripts
rm -rf  /var/lib/etcd/restored
ls -al  /tmp/etcd*
-rw-r--r-- 1 root root 27512864 Oct  6 18:14 /tmp/etcd.2018.10.06-18.14.21.db
-rw-r--r-- 1 root root 27512864 Oct  6 18:14 /tmp/etcd.2018.10.06-18.14.37.db
-rw-r--r-- 1 root root 27512864 Oct  6 18:21 /tmp/etcd.2018.10.06-18.21.10.db
-rw-r--r-- 1 root root 27512864 Oct  6 19:42 /tmp/etcd.2018.10.06-19.42.19.db

./restoreEtcd.sh etcd.2018.10.06-19.42.19.db
ls -al  /var/lib/etcd/restored/
~~~

### 5. delete existing & copy restored member data
~~~
ls -al /var/lib/etcd
mv   /var/lib/etcd/member  /tmp/member
mv  /var/lib/etcd/restored/member  /var/lib/etcd/member
~~~

### 6. start etcd
~~~
mv /etc/cfc/etcd.json /etc/cfc/pods/
ls -al /etc/cfc/pods
watch -d -n 1 "docker ps | grep etcd"
kubectl get pods --all-namespaces
~~~

### :cyclone: Remarks  
~~~
kubectl exec -it k8s-etcd-169.56.80.221 -n kube-system sh
watch -d -n 2 "docker ps | grep etcd"
~~~

- main	https://github.com/ibm-cloud-architecture/icp-backup/blob/master/docs/etcd.md
- etcd restore to single   https://github.com/ibm-cloud-architecture/icp-backup/blob/master/docs/etcd_restore_single.md
- etcd restore to multi    https://github.com/ibm-cloud-architecture/icp-backup/blob/master/docs/etcd_restore_multi.md
- etcd	https://github.com/haoqing0110/icp-workspace/tree/master/icp-backup-restore/etcd
- etcd backup & restore  https://juner417.github.io/blog/etcd-backup-and-restore/
- etcd everything	https://www.mirantis.com/blog/everything-you-ever-wanted-to-know-about-using-etcd-with-kubernetes-v1-6-but-were-afraid-to-ask/
