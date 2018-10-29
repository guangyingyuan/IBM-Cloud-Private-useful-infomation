# :sheep: mariadb backup and recovery

## @ mariadb check with mariadb cli

### 1. Install mysql client (this is only for checking with mysql cli)  
~~~
apt-get install mysql-client
~~~

### 2. Prepare script
~~~
git clone https://github.com/ibm-cloud-architecture/icp-backup.git
cd ~/icp-backup/src/mariadb-backup/1.0
source helper-functions.sh
mariadb_user=$(getMariaDBUser)
mariadb_password=$(getMariaDBPassword)
mysql --user=$mariadb_user --password=$mariadb_password --host=master
~~~

### 3. check and create sample mysql table
~~~
mysql> show databases ;
+--------------------+
| Database           |
+--------------------+
| OAuthDBSchema      |
| icptest            |
| information_schema |
| mysql              |
| performance_schema |
+--------------------+
5 rows in set (0.00 sec)

create database icptest;
show databases ;
use icptest ;
create table icp01(a varchar(10), b integer) ;
show tables ;
insert into icp01 values("xxxx",100) ;
select * from icp01 ;
~~~

## @ Backup with docker container job

### 4. Build docker image
~~~
cd ~/icp-backup/src/mariadb-backup
docker build -f Dockerfile-1.0 --build-arg version=1.0 -t ibmcase/icp-mariadb-backup .
docker images | grep mariadb
~~~

- in case of moving to the other server
~~~
mkdir ~/images
docker save ibmcase/icp-mariadb-backup | gzip > ~/images/icp-mariadb-backup-image-0.1.0.tgz
@ Move to master node if image is located on other nodes
docker load -i ~/images/icp-mariadb-backup-image-0.1.0.tgz
~~~

~~~
docker tag ibmcase/icp-mariadb-backup:latest mycluster.icp:8500/default/ibmcase/icp-mariadb-backup:latest
docker login mycluster.icp:8500
docker push mycluster.icp:8500/default/ibmcase/icp-mariadb-backup:latest
docker images | grep mariadb
~~~

### 5. Create PV & PVC
~~~
cd ~/icp-backup/resources
kubectl create -f mariadb-backup-pvc.yaml
~~~
- need to create PV mariadb-backup at portal  with “path” option

### 6. Backup
~~~
cd ~/icp-backup/resources
cloudctl login
kubectl create -f icp-mariadb-backup-job.yaml
kubectl get pods -n default | grep mariadb
kubectl logs pod/icp-mariadb-backup-xxxxx
~~~

### 7. check output
- check PV mariadb-backup directory at worker node
~~~
ll /mariadb-backup
drwxr-xr-x  2 root root 4096 Sep 25 02:56 icp-mariadb-backup-2018-09-25-02-55-59
drwxr-xr-x  2 root root 4096 Sep 25 03:04 icp-mariadb-backup-2018-09-25-03-04-17
drwxr-xr-x  2 root root 4096 Sep 25 03:13 icp-mariadb-backup-2018-09-25-03-13-21
~~~

### 8. make failure situation
~~~
cd ~/icp-backup/src/mariadb-backup/1.0
source helper-functions.sh
mariadb_user=$(getMariaDBUser)
mariadb_password=$(getMariaDBPassword)
mysql --user=$mariadb_user --password=$mariadb_password --host=ms01

show databases ;
drop database icptest;
show databases ;
~~~

## @ Recovery with backup image

### 9. Restore
~~~
cd ~/icp-backup/resources  
kubectl delete job/icp-mariadb-restore  
kubectl create -f icp-mariadb-restore-job.yaml   # need to check PV  
kubectl get pods -n default | grep mariadb   
kubectl logs pod/icp-mariadb-restore-xxxxx  
~~~
### 10. check mysql database
~~~
mysql --user=$mariadb_user --password=$mariadb_password --host=ms01  

show databases ;  
use icptest ;  
show tables ;  
select * from icp01 ;  
~~~

<p align="center" >
<img width=700 src="https://github.com/moreal70/IBM-Cloud-Private-useful-infomation/blob/master/images/mariadb-backup-recovery-pod.jpg">
</p>

### Remarks

backup process https://github.com/ibm-cloud-architecture/icp-backup/blob/master/docs/mariadb.md    
demo log file  https://github.com/moreal70/IBM-Cloud-Private-useful-infomation/blob/master/files/mariadb-backup-recovery-log.jpg
