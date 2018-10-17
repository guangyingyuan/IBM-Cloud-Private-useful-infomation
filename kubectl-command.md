### kubectl command
~~~
kubectl get statefulset -n kube-system logging-elk-data -o=custom-columns=A:.status.readyReplicas,B:.status.replicas --no-headers=true | tr -s " " | awk '$1 == $2 {print "READY"}'
kubectl get statefulsets -n kube-system
kubectl edit statefulsets/icp-mongodb  -n kube-system

kubectl get configmaps -n kube-system | grep mongo
kubectl describe configmaps/icp-mongodb  -n kube-system
kubectl edit  configmaps/icp-mongodb  -n kube-system

kubectl delete pod 1234-56-7890-234234-456456
kubectl delete pods --all
kubectl delete deployment guestbook
kubectl delete deploy guestbook
kubectl delete service guestbook

kubectl get pods -l app=guestbook
kubectl get service guestbook
kubectl edit deployment guestbook

kubectl describe service guestbook
kubectl describe  pod k8s-etcd-169.62.169.131  -n kube-system
kubectl scale --replicas=10 deployment guestbook
kubectl expose deployment guestbook --type="NodePort" --port=3000
kubectl cluster-info
kubectl autoscale deployment hello-world --cpu-percent=10 --min=4 --max=6


kubectl logs <podname>
kubectl logs <pod-name> <container-name>
kubectl run bb --image busybox --restart=Never -it --rm
kubectl get endpoints <service>
~~~

### login command
~~~
cloudctl login -a https://169.56.80.221:8443 --skip-ssl-validation
docker login mycluster.icp:8001
~~~

~~~
ibmcloud cs cluster-config <name-of-cluster>
ibmcloud ks command  https://console.bluemix.net/docs/containers/cs_cli_reference.html#cs_cli_reference
ibmcloud ks api
ibmcloud ks region-set
ibmcloud ks region-set ap-north
ibmcloud ks zones
ibmcloud ks cluster-config mycluster
ibmcloud ks workers --cluster mycluster
ibmcloud ks worker-pools --cluster mycluster
ibmcloud ks albs  --cluster mycluster
ibmcloud ks subnets
ibmcloud ks machine-types --zone seo01
ibmcloud ks logging-config-get  --cluster mycluster
ibmcloud ks worker-rm --cluster mycluster --workers  kube-seo01-cr13ebbb99e023421cb031b8fbbd8b7ea2-w3
~~~

## Command reference
- https://kubernetes-v1-4.github.io/docs/user-guide/kubectl-overview/




