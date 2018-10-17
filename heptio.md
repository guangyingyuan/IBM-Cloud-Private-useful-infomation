# heptio ark - backup solution

- reference url
https://github.com/heptio/ark
https://medium.com/@pmvk/kubernetes-backups-and-recovery-efc33180e89d
https://github.com/heptio/ark/releases
https://github.com/krishnapmv/kube-backup
https://medium.com/ibm-cloud/integration-of-ibm-kubernetes-deployments-with-ibm-cloud-object-storage-via-heptio-ark-9097c58b5330

~~~
git clone https://github.com/heptio/ark.git

kubectl apply -f 00-prereqs.yaml
kubectl get crd
kubectl get clusterrolebinding
kubectl get serviceaccount
kubectl get namespace

kubectl apply -f examples/minio/
kubectl get job  -n heptio-ark
kubectl get serviceaccount  -n heptio-ark
kubectl get deploy  -n heptio-ark
kubectl get config  -n heptio-ark

kubectl apply -f base.yaml
kubectl get deploy -n nginx-example
kubectl get service  -n nginx-example

ark backup create nginx-backup --selector app=nginx
ark backup describe nginx-backup

kubectl delete namespace nginx-example
kubectl get namespaces
kubectl get deployments --namespace=nginx-example
kubectl get services --namespace=nginx-example
kubectl get namespace/nginx-example

ark restore create --from-backup nginx-backup
ark restore get
kubectl get namespace/nginx-example

ark backup create heptio-ark-backup --include-namespaces heptio-ark
ark backup describe heptio-ark-backup
~~~

### Backup to IBM COS (cloud object storage) 
order ICOS  http://console.bluemix.net
create bucket
generate credential  with HMAC option  {"HMAC":true}  
access_key_id  e85fc4839b374a37a79343befe3fca7a
secret_access_key  7dbea1d284b7c4f6a661018cf5d67c4a2edde4060912c727
service endpoint   s3.us-south.objectstorage.softlayer.net

create  credentials-ark  file 

cos://ap-geo/heptio-ark-bucket

~~~
kubectl apply -f common/00-prereqs.yaml
kubectl get crd
kubectl get clusterrolebinding
kubectl get serviceaccount
kubectl get namespace

kubectl apply -f examples/ibm/00-ark-config.yaml
kubectl apply -f examples/ibm/10-deployment.yaml
~~~

~~~
apiVersion: v1
kind: Secret
metadata:
    namespace: heptio-ark-server
    name: cloud-credentials
stringData:
    cloud: 
        [default]
        aws_access_key_id = e85fc4839b374a37a79343befe3fca7a
        aws_secret_access_key = 7dbea1d284b7c4f6a661018cf5d67c4a2edde4060912c727

apiVersion: ark.heptio.com/v1
kind: Config
metadata:
    namespace: heptio-ark
    name: default
backupStorageProvider:
    name: aws
    bucket: heptio-ark-backup
    config:
        region: us-south
        s3ForcePathStyle: "true"
        s3Url: http://s3.us-south.objectstorage.softlayer.net
backupSyncPeriod: 1m
gcSyncPeriod: 1m
scheduleSyncPeriod: 1m
restoreOnlyMode: false
~~~







