# :cloud: mongoDB bakcup & recovery :cloud:

### 1. check mongodb pod  
~~~
kubectl -n kube-system get pods | grep mongodb
icp-mongodb-0                                                  1/1       Running     0          2d  
icp-mongodb-1                                                  1/1       Running     0          2d  
icp-mongodb-2                                                  1/1       Running     0          2d  
~~~

### 2. go into mongodb container  
- it may need to change container name 
~~~  
kubectl -n kube-system exec -it icp-mongodb-0 bash
mongo  --host rs0/mongodb:27017 --username admin --password $ADMIN_PASSWORD --authenticationDatabase admin --ssl --sslCAFile /ca/tls.crt --sslPEMKeyFile /work-dir/mongo.pem
~~~  

### 3. check database & collection via mongo cli
~~~
show dbs ;
use HELM_API_DB ;
show collections ;
db.helm_repos.find() ;
db.helm_repos.count() ;
~~~

### 4. backup mongodb full
~~~
kubectl -n kube-system exec icp-mongodb-0 -- sh -c 'mkdir -p /work-dir/Backup/mongodump; mongodump --oplog --out /work-dir/Backup/mongodump --host rs0/mongodb:27017 --username admin --password $ADMIN_PASSWORD --authenticationDatabase admin --ssl --sslCAFile /ca/tls.crt --sslPEMKeyFile /work-dir/mongo.pem'
~~~

### 5. check backup output (PV directory)
~~~
ll /var/lib/icp/mongodb/workdir/Backup/mongodump
~~~


### 6. make failure  
- go into mongoDB container and mongo cli
~~~
kubectl -n kube-system exec -it icp-mongodb-0 bash
mongo  --host rs0/mongodb:27017 --username admin --password $ADMIN_PASSWORD --authenticationDatabase admin --ssl --sslCAFile /ca/tls.crt --sslPEMKeyFile /work-dir/mongo.pem
~~~

- drop help repository database 
~~~
show dbs ;
use HELM_API_DB ;
db.dropDatabase()  ;
show dbs ;
~~~

### 7. check portal helm repo 
- repositories should not be listed 
![helm repo](https://github.com/moreal70/IBM-Private-Cloud-handsOn/blob/master/images/portal_helm_repo.JPG)

### 8. recovery 
- you can change option "--nsInclude" for specify the dbname
~~~
kubectl -n kube-system exec icp-mongodb-2 -- sh -c 'mongorestore --host rs0/mongodb:27017 --username admin --password $ADMIN_PASSWORD --authenticationDatabase admin --ssl --sslCAFile /ca/tls.crt --sslPEMKeyFile /work-dir/mongo.pem /work-dir/Backup/mongodump --nsInclude HELM_API_DB.*'
~~~

### 9. check DB & table
~~~
show dbs ;
use HELM_API_DB ;
~~~

### :umbrella: Remarks 
- mongo sql example
~~~
use icp02;
use metering_DB ;
use icptest ;
db.icp02.insertOne({test:10000}) ; 
db.icp02.find() ;
db.icp02.count() ;
~~~

- selective restore example
~~~
kubectl -n kube-system exec icp-mongodb-2 -- sh -c 'mongorestore --host rs0/mongodb:27017 --username admin --password $ADMIN_PASSWORD --authenticationDatabase admin --ssl --sslCAFile /ca/tls.crt --sslPEMKeyFile /work-dir/mongo.pem /work-dir/Backup/mongodump --nsInclude icptest.*'  
kubectl -n kube-system exec icp-mongodb-2 -- sh -c 'mongorestore --host rs0/mongodb:27017 --username admin --password $ADMIN_PASSWORD --authenticationDatabase admin --ssl --sslCAFile /ca/tls.crt --sslPEMKeyFile /work-dir/mongo.pem /work-dir/Backup/mongodump --nsInclude metering_DB.*'
~~~

- Reference document  
https://github.com/ibm-cloud-architecture/icp-backup/blob/master/docs/mongodb.md (official, but not working)  
https://github.ibm.com/IBMPrivateCloud/icp-mongodb/blob/master/BackupRestore.README.md (working)
