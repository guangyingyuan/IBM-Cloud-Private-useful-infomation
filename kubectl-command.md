### kubectl command
kubectl get statefulset -n kube-system logging-elk-data -o=custom-columns=A:.status.readyReplicas,B:.status.replicas --no-headers=true | tr -s " " | awk '$1 == $2 {print "READY"}'
kubectl get statefulsets -n kube-system
kubectl edit statefulsets/icp-mongodb  -n kube-system

kubectl get configmaps -n kube-system | grep mongo
kubectl describe configmaps/icp-mongodb  -n kube-system
kubectl edit  configmaps/icp-mongodb  -n kube-system

### login command
cloudctl login -a https://169.56.80.221:8443 --skip-ssl-validation
docker login mycluster.icp:8001

### docker run command
docker run --rm -v /usr/local/bin:/data ibmcom/etcd:v3.2.14 cp /usr/local/bin/etcdctl /data