# :bird: stolon - PostgreSQL cloud native High Availability

### @ IBM Watson Assistant on ICP uses PostgreSQL via stolon package for Watson Training Data
### Stolon Architecture and Components
<p align="center" >
<img width=400 src="https://github.com/moreal70/IBM-Private-Cloud-handsOn/blob/master/images/stolon_architecture.jpg">
</p>
Stolon is composed of 3 main components
* keeper: it manages a PostgreSQL instance converging to the clusterview provided by the sentinel(s).
* sentinel: it discovers and monitors keepers and calculates the optimal clusterview.
* proxy: the client's access point. It enforce connections to the right PostgreSQL master and forcibly closes connections to old masters.

### This is handson guide followed URL below and I added my testing how it work for HA
* https://github.com/sorintlab/stolon/blob/master/examples/kubernetes/README.md

## 1. Installation postgresql stolon
~~~
git clone https://github.com/sorintlab/stolon.git
kubectl run -i -t stolonctl --image=sorintlab/stolon:master-pg10 --restart=Never --rm -- /usr/local/bin/stolonctl --cluster-name=kube-stolon --store-backend=kubernetes --kube-resource-kind=configmap init
cd ~/stolon/examples/kubernetes
kubectl create -f stolon-sentinel.yaml
kubectl create -f secret.yaml
~~~
* need to create pvc and pv instead of storage class
* pvc.yaml
~~~
kind: PersistentVolume
apiVersion: v1
metadata:
  name: stolon-pv2
  labels:
    type: local
spec:
  capacity:
    storage: 20Gi
  accessModes:
    - ReadWriteOnce
  hostPath:
    path: "/stolon-pv2"
~~~
* pv.yaml
~~~
kind: PersistentVolumeClaim
apiVersion: v1
metadata:
  name: stolon-pvc
spec:
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 20Gi
~~~

~~~
kubectl create -f pvc.yaml  
kubectl create -f pv.yaml  
~~~

* need to update stolon-keeper.yaml when storage class "standard" is not privided
~~~
kubectl create -f stolon-keeper.yaml
~~~

~~~
kubectl create -f stolon-proxy.yaml
kubectl create -f stolon-proxy-service.yaml
~~~

* connect to PostgreSQL
~~~
kubectl get svc
  NAME                                TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)          AGE
  stolon-proxy-service                ClusterIP   10.0.0.148   <none>        5432/TCP         3h
psql --host  10.0.0.148 --port 5432 postgres -U stolon -W  # (password1)
~~~

* postgres sql sample
~~~
create table test (id int primary key not null, value text not null);
insert into test values (1, 'value1');
select * from test ;
~~~

## 2. How it works

* screen shot at portal
~~~
NAME	NAMESPACE	STATUS	HOST IP	POD IP	READY	START TIME	ACTION
stolon-keeper-0	default	Running	10.xxx.xxx.217	10.1.103.170	1/1	9 minutes ago
stolon-keeper-1	default	Running	10.xxx.xxx.207	10.1.143.15	1/1	9 minutes ago

NAME	NAMESPACE	STATUS	HOST IP	POD IP	READY	START TIME	ACTION
stolon-proxy-69d4cf8c79-qgkfv	default	Running	10.xxx.xxx.217	10.1.103.185	1/1	1 minute ago
stolon-proxy-69d4cf8c79-rssg8	default	Running	10.xxx.xxx.207	10.1.143.16	1/1	1 minute ago

NAME	NAMESPACE	STATUS	HOST IP	POD IP	READY	START TIME	ACTION
stolon-sentinel-5bd96fb8d9-kbjz7	default	Running	10.xxx.xxx.217	10.1.103.171	1/1	1 hour ago
stolon-sentinel-5bd96fb8d9-w47hx	default	Running	10.xxx.xxx.207	10.1.143.39	1/1	1 hour ago
~~~

* check uid info from phyiscal file
~~~
ansible worker -m shell -a "cat  /stolon-pv/dbstate"
    10.xxx.xxx.217 | SUCCESS | rc=0 >>
    {"UID":"552601a0","Generation":3,"Initializing":false,"InitPGParameters":{"datestyle":"iso, mdy","default_text_search_config":"pg_catalog.english","dynamic_shared_memory_type":"posix","hot_standby":"on","lc_messages":"en_US.utf8","lc_monetary":"en_US.utf8","lc_numeric":"en_US.utf8","lc_time":"en_US.utf8","listen_addresses":"127.0.0.1,10.1.103.170","log_timezone":"UTC","max_connections":"100","max_replication_slots":"20","max_wal_senders":"47","port":"5432","shared_buffers":"128MB","synchronous_standby_names":"","timezone":"UTC","unix_socket_directories":"/tmp","wal_keep_segments":"8","wal_level":"replica"}}

    10.xxx.xxx.207 | SUCCESS | rc=0 >>
    {"UID":"653e0a65","Generation":2,"Initializing":false,"InitPGParameters":null}
* need to check "552601a0"
~~~

* check logs : master
~~~
kubectl logs -f stolon-keeper-0 -c stolon-keeper -n default
    2018-10-31T07:02:42.831Z	INFO	cmd/keeper.go:1389	our db requested role is master
    2018-10-31T07:02:42.832Z	INFO	cmd/keeper.go:1425	already master
    2018-10-31T07:02:42.845Z	INFO	cmd/keeper.go:1557	postgres parameters not changed
    2018-10-31T07:02:42.845Z	INFO	cmd/keeper.go:1584	postgres hba entries not changed
    2018-10-31T07:02:47.865Z	INFO	cmd/keeper.go:1389	our db requested role is master
* keeper0 is master
~~~

* check logs : standby
~~~
kubectl logs -f stolon-keeper-1 -c stolon-keeper -n default
    2018-10-31T07:04:25.680Z	INFO	cmd/keeper.go:1439	our db requested role is standby	{"followedDB": "552601a0"}
    2018-10-31T07:04:25.695Z	INFO	cmd/keeper.go:1584	postgres hba entries not changed
    2018-10-31T07:04:25.694Z	INFO	cmd/keeper.go:1557	postgres parameters not changed
    2018-10-31T07:04:30.717Z	INFO	cmd/keeper.go:1458	already standby
* keeper1 is standby
~~~

* stop keep0 using portal "remove" pod
~~~
    2018-10-31 07:19:08.639 UTC [7456] FATAL:  terminating connection due to administrator command
    2018-10-31 07:19:08.639 UTC [76] LOG:  aborting any active transactions
    2018-10-31 07:19:08.641 UTC [76] LOG:  worker process: logical replication launcher (PID 83) exited with exit code 1
    2018-10-31 07:19:08.641 UTC [78] LOG:  shutting down
    2018-10-31 07:19:08.700 UTC [76] LOG:  database system is shut down
    waiting for server to shut down.... done
    server stopped
    2018-10-31T07:19:21.789Z	INFO	cmd/keeper.go:1911	exclusive lock on data dir taken
    2018-10-31T07:19:21.788Z	WARN	cmd/keeper.go:162	password file permissions are too open. This file should only be readable to the user executing stolon! Continuing...	{"file": "/etc/secrets/stolon/password", "mode": "01000000777"}
    2018-10-31T07:19:21.790Z	INFO	cmd/keeper.go:492	keeper uid	{"uid": "keeper0"}
    2018-10-31T07:19:21.809Z	ERROR	cmd/keeper.go:641	cannot get configured pg parameters	{"error": "dial unix /tmp/.s.PGSQL.5432: connect: no such file or directory"}
    2018-10-31T07:19:21.824Z	INFO	cmd/keeper.go:990	our db boot UID is different than the cluster data one, waiting for it to be updated	{"bootUUID": "dfc92e72-0b16-4642-aa07-d4967e3b902f", "clusterBootUUID": "ba3e1016-1e98-4a6e-a865-d76f2a06b4c8"}
    2018-10-31T07:19:24.310Z	ERROR	cmd/keeper.go:641	cannot get configured pg parameters	{"error": "dial unix /tmp/.s.PGSQL.5432: connect: no such file or directory"}
    2018-10-31T07:19:26.811Z	ERROR	cmd/keeper.go:641	cannot get configured pg parameters	{"error": "dial unix /tmp/.s.PGSQL.5432: connect: no such file or directory"}
    2018-10-31T07:19:26.842Z	INFO	cmd/keeper.go:1389	our db requested role is master
    2018-10-31T07:19:26.856Z	INFO	postgresql/postgresql.go:307	starting database
    2018-10-31 07:19:26.873 UTC [44] LOG:  listening on IPv4 address "127.0.0.1", port 5432
    2018-10-31 07:19:26.880 UTC [44] LOG:  listening on IPv4 address "10.1.103.186", port 5432
    2018-10-31 07:19:26.883 UTC [44] LOG:  listening on Unix socket "/tmp/.s.PGSQL.5432"
    2018-10-31 07:19:26.905 UTC [46] LOG:  database system was shut down at 2018-10-31 07:19:08 UTC
    2018-10-31 07:19:26.912 UTC [44] LOG:  database system is ready to accept connections
    2018-10-31T07:19:27.064Z	INFO	cmd/keeper.go:1425	already master
    2018-10-31T07:19:27.081Z	INFO	cmd/keeper.go:1557	postgres parameters not changed
    2018-10-31T07:19:27.081Z	INFO	cmd/keeper.go:1584	postgres hba entries not changed
    2018-10-31 07:19:28.511 UTC [59] LOG:  incomplete startup packet
    2018-10-31 07:19:28.587 UTC [60] LOG:  incomplete startup packet
* keeper0 is automatically restarted by kubernetes as a master
~~~

* making a failure that pod can not be restarted automatically
~~~
kubectl delete statefulset stolon-keeper --cascade=false
    statefulset.apps "stolon-keeper" deleted
kubectl get pods -n default
    NAME                                                 READY     STATUS    RESTARTS   AGE
    ibm-postgres-dev-ibm-postgres-dev-677f84fdfc-tn5xs   1/1       Running   0          2h
    stolon-keeper-0                                      1/1       Running   0          3m
    stolon-keeper-1                                      1/1       Running   0          51m
~~~

* check the database
~~~
psql --host  10.0.0.148 --port 5432 postgres -U stolon -W   #password1
    Password for user stolon:
    psql (9.5.14, server 10.5 (Debian 10.5-2.pgdg90+1))
    WARNING: psql major version 9.5, server major version 10.
             Some psql features might not work.
    Type "help" for help.

    postgres=# select * from test ;
     id | value
    ----+--------
      1 | value1
      2 | value1
      3 | value1
      4 | value1
      5 | value1
      6 | value1
      7 | value1
      8 | value1
    (8 rows)
~~~

* delete master pod during no stateful, it's for not restarted automatically
~~~
kubectl delete pod stolon-keeper-0
kubectl get pods -n default
    NAME                                                 READY     STATUS    RESTARTS   AGE
    stolon-keeper-1                                      1/1       Running   0          57m
~~~

* check the switching keeper
~~~
    2018-10-31T07:25:55.908Z        INFO    cmd/keeper.go:1458      already standby
    2018-10-31T07:25:55.923Z        INFO    cmd/keeper.go:1557      postgres parameters not changed
    2018-10-31T07:25:55.923Z        INFO    cmd/keeper.go:1584      postgres hba entries not changed
    2018-10-31T07:26:00.942Z        INFO    cmd/keeper.go:1389      our db requested role is master
    2018-10-31T07:26:00.943Z        INFO    cmd/keeper.go:1418      promoting to master
    2018-10-31T07:26:00.943Z        INFO    postgresql/postgresql.go:501    promoting database
    2018-10-31 07:26:00.947 UTC [391] LOG:  received promote request
    2018-10-31 07:26:00.948 UTC [1274] FATAL:  terminating walreceiver process due to administrator command
    2018-10-31 07:26:00.948 UTC [391] LOG:  redo done at 0/50002C8
    2018-10-31 07:26:00.975 UTC [391] LOG:  selected new timeline ID: 2
    2018-10-31 07:26:01.257 UTC [391] LOG:  archive recovery complete
    2018-10-31 07:26:01.298 UTC [390] LOG:  database system is ready to accept connections
    waiting for server to promote.... done
    server promoted
    2018-10-31T07:26:01.362Z        INFO    cmd/keeper.go:1557      postgres parameters not changed
    2018-10-31T07:26:01.362Z        INFO    cmd/keeper.go:1584      postgres hba entries not changed
    2018-10-31T07:26:06.431Z        INFO    cmd/keeper.go:1389      our db requested role is master
    2018-10-31T07:26:06.433Z        INFO    cmd/keeper.go:1425      already master
    2018-10-31T07:26:06.437Z        INFO    cmd/keeper.go:910       creating replication slot       {"slot": "stolon_653e0a65"}
    2018-10-31T07:26:06.482Z        INFO    cmd/keeper.go:1557      postgres parameters not changed
    2018-10-31T07:26:06.482Z        INFO    cmd/keeper.go:1584      postgres hba entries not changed
* check standby --> master
~~~

* re-create the stateful
~~~
kubectl create -f stolon-keeper.yaml
    2018-10-31T07:25:20.330Z	INFO	cmd/keeper.go:1584	postgres hba entries not changed
    2018-10-31T07:25:25.354Z	INFO	cmd/keeper.go:1389	our db requested role is master
    2018-10-31T07:25:25.355Z	INFO	cmd/keeper.go:1425	already master
    2018-10-31T07:25:25.370Z	INFO	cmd/keeper.go:1557	postgres parameters not changed
    2018-10-31T07:25:25.370Z	INFO	cmd/keeper.go:1584	postgres hba entries not changed
    2018-10-31 07:25:28.511 UTC [1002] LOG:  incomplete startup packet
    2018-10-31 07:25:28.586 UTC [1003] LOG:  incomplete startup packet
    2018-10-31T07:25:30.358Z	INFO	postgresql/postgresql.go:372	stopping database
    2018-10-31 07:25:30.359 UTC [44] LOG:  received fast shutdown request
    2018-10-31 07:25:30.361 UTC [44] LOG:  aborting any active transactions
    2018-10-31 07:25:30.363 UTC [44] LOG:  worker process: logical replication launcher (PID 52) exited with exit code 1
    2018-10-31 07:25:30.365 UTC [47] LOG:  shutting down
    2018-10-31 07:25:30.407 UTC [44] LOG:  database system is shut down
    server stopped
    waiting for server to shut down.... done
    2018-10-31T07:30:50.846Z	INFO	cmd/keeper.go:492	keeper uid	{"uid": "keeper0"}
    2018-10-31T07:30:50.844Z	WARN	cmd/keeper.go:162	password file permissions are too open. This file should only be readable to the user executing stolon! Continuing...	{"file": "/etc/secrets/stolon/password", "mode": "01000000777"}
    2018-10-31T07:30:50.844Z	INFO	cmd/keeper.go:1911	exclusive lock on data dir taken
    2018-10-31T07:30:50.869Z	ERROR	cmd/keeper.go:641	cannot get configured pg parameters	{"error": "dial unix /tmp/.s.PGSQL.5432: connect: no such file or directory"}
    2018-10-31T07:30:50.874Z	INFO	cmd/keeper.go:982	no db assigned
    2018-10-31T07:30:53.370Z	ERROR	cmd/keeper.go:641	cannot get configured pg parameters	{"error": "dial unix /tmp/.s.PGSQL.5432: connect: no such file or directory"}
    2018-10-31T07:30:55.870Z	ERROR	cmd/keeper.go:641	cannot get configured pg parameters	{"error": "dial unix /tmp/.s.PGSQL.5432: connect: no such file or directory"}
    2018-10-31T07:30:55.880Z	INFO	cmd/keeper.go:1037	current db UID different than cluster data db UID	{"db": "552601a0", "cdDB": "c9bb276a"}
    2018-10-31T07:30:55.880Z	INFO	cmd/keeper.go:1190	resyncing the database cluster
    2018-10-31T07:30:55.971Z	INFO	cmd/keeper.go:827	syncing from followed db	{"followedDB": "164cb2ca", "keeper": "keeper2"}
    2018-10-31T07:30:55.972Z	INFO	postgresql/postgresql.go:824	running pg_basebackup
    pg_basebackup: removing data directory "/stolon-data/postgres"
    pg_basebackup: could not connect to server: could not connect to server: Connection timed out
    	Is the server running on host "10.1.48.106" and accepting
    	TCP/IP connections on port 5432?
    2018-10-31T07:33:03.205Z	ERROR	cmd/keeper.go:1253	failed to resync from followed instance	{"error": "sync error: error: exit status 1"}
    2018-10-31T07:33:08.211Z	ERROR	cmd/keeper.go:1006	db failed to initialize or resync
    2018-10-31T07:33:08.228Z	INFO	cmd/keeper.go:1037	current db UID different than cluster data db UID	{"db": "", "cdDB": "c9bb276a"}
    2018-10-31T07:33:08.228Z	INFO	cmd/keeper.go:1190	resyncing the database cluster
    2018-10-31T07:33:08.241Z	INFO	cmd/keeper.go:1215	database cluster not initialized
    2018-10-31T07:33:08.252Z	INFO	cmd/keeper.go:827	syncing from followed db	{"followedDB": "653e0a65", "keeper": "keeper1"}
    2018-10-31T07:33:08.253Z	INFO	postgresql/postgresql.go:824	running pg_basebackup
    WARNING:  skipping special file "./postgresql.auto.conf"
    2018-10-31T07:33:10.891Z	ERROR	cmd/keeper.go:641	cannot get configured pg parameters	{"error": "dial unix /tmp/.s.PGSQL.5432: connect: no such file or directory"}
    2018-10-31T07:33:11.654Z	INFO	cmd/keeper.go:833	sync succeeded
    2018-10-31T07:33:11.719Z	INFO	postgresql/postgresql.go:307	starting database
    2018-10-31 07:33:11.808 UTC [86] LOG:  listening on IPv4 address "127.0.0.1", port 5432
    2018-10-31 07:33:11.812 UTC [86] LOG:  listening on IPv4 address "10.1.103.174", port 5432
    2018-10-31 07:33:11.815 UTC [86] LOG:  listening on Unix socket "/tmp/.s.PGSQL.5432"
    2018-10-31 07:33:11.835 UTC [87] LOG:  database system was interrupted; last known up at 2018-10-31 07:33:08 UTC
    2018-10-31 07:33:11.915 UTC [87] LOG:  entering standby mode
    2018-10-31T07:33:11.931Z	INFO	cmd/keeper.go:1458	already standby
    2018-10-31T07:33:11.931Z	INFO	cmd/keeper.go:1439	our db requested role is standby	{"followedDB": "653e0a65"}
    2018-10-31T07:33:11.934Z	ERROR	cmd/keeper.go:1501	error updating replication slots	{"error": "pq: the database system is starting up"}
    2018-10-31T07:33:11.934Z	ERROR	cmd/keeper.go:924	failed to get replication slots	{"error": "pq: the database system is starting up"}
    2018-10-31 07:33:11.934 UTC [91] FATAL:  the database system is starting up
    2018-10-31T07:33:11.942Z	INFO	cmd/keeper.go:1557	postgres parameters not changed
    2018-10-31T07:33:11.942Z	INFO	cmd/keeper.go:1584	postgres hba entries not changed
    2018-10-31 07:33:11.949 UTC [87] LOG:  redo starts at 0/6000028
    2018-10-31 07:33:11.958 UTC [87] LOG:  consistent recovery state reached at 0/6000130
    2018-10-31 07:33:11.958 UTC [86] LOG:  database system is ready to accept read only connections
    2018-10-31 07:33:11.971 UTC [96] LOG:  started streaming WAL from primary at 0/7000000 on timeline 3
    2018-10-31T07:33:16.973Z	INFO	cmd/keeper.go:1458	already standby
    2018-10-31T07:33:16.973Z	INFO	cmd/keeper.go:1439	our db requested role is standby	{"followedDB": "653e0a65"}
    2018-10-31T07:33:16.989Z	INFO	cmd/keeper.go:1584	postgres hba entries not changed
    2018-10-31T07:33:16.988Z	INFO	cmd/keeper.go:1557	postgres parameters not changed
    2018-10-31T07:33:22.033Z	INFO	cmd/keeper.go:1439	our db requested role is standby	{"followedDB": "653e0a65"}
    2018-10-31T07:33:22.033Z	INFO	cmd/keeper.go:1458	already standby
* need to check "653e0a65"

~~~

* check the physical file for uid
~~~
ansible worker -m shell -a "cat  /stolon-pv/dbstate"
    10.xxx.xxx.217 | SUCCESS | rc=0 >>
    {"UID":"c9bb276a","Generation":3,"Initializing":false,"InitPGParameters":null}

    10.xxx.xxx.207 | SUCCESS | rc=0 >>
    {"UID":"653e0a65","Generation":5,"Initializing":false,"InitPGParameters":null}
* need to check "653e0a65"
~~~

* Initializing the database
~~~
kubectl run -i -t stolonctl --image=sorintlab/stolon:master-pg10 --restart=Never --rm -- /usr/local/bin/stolonctl --cluster-name=kube-stolon --store-backend=kubernetes --kube-resource-kind=configmap init
~~~

~~~
ansible worker -m shell -a "cat  /stolon-pv/dbstate"
    10.xxx.xxx.207 | SUCCESS | rc=0 >>
    {"UID":"4e8b11cf","Generation":2,"Initializing":false,"InitPGParameters":null}

    10.xxx.xxx.217 | SUCCESS | rc=0 >>
    {"UID":"28e1748d","Generation":3,"Initializing":false,"InitPGParameters":{"datestyle":"iso, mdy","default_text_search_config":"pg_catalog.english","dynamic_shared_memory_type":"posix","hot_standby":"on","lc_messages":"en_US.utf8","lc_monetary":"en_US.utf8","lc_numeric":"en_US.utf8","lc_time":"en_US.utf8","listen_addresses":"127.0.0.1,10.1.103.174","log_timezone":"UTC","max_connections":"100","max_replication_slots":"20","max_wal_senders":"47","port":"5432","shared_buffers":"128MB","synchronous_standby_names":"","timezone":"UTC","unix_socket_directories":"/tmp","wal_keep_segments":"8","wal_level":"replica"}}
~~~

## Remarks
* http://www.postgresqltutorial.com/psql-commands/
* https://www.postgresql.org/docs/10/static/app-psql.html
* https://github.com/sorintlab/stolon

* sql command
~~~
\list
\dt
\c postgres
CREATE TABLE  icp01 (a text , b text)  ;
INSERT INTO icp01 values ('aaaa', 217) ;
INSERT INTO icp01 values ('aaaa', 221) ;
INSERT INTO icp01 values ('aaaa', 239) ;
INSERT INTO icp01 values ('bbbb', 2) ;
SELECT * FROM icp01 ;
~~~
