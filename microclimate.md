# microclimate
----------------
## useful information
Microclimate is a Web IDE for DevOps on IBM Cloud Private, Key functionalities are Immediate view for Web Application log, Resource Monitoring like JVM or GC, ntegrated with Jenkins for CI/CD

- microclimate site : https://microclimate-dev2ops.github.io/gettingstarted
- Use Microclimate to run an End-to-End DevOps on ICP	https://github.com/ibm-cloud-architecture/refarch-cloudnative-bluecompute-microclimate
----------------

## install microclimate on ICP
- https://github.com/IBM/charts/blob/master/stable/ibm-microclimate/README.md

### 1. create Namespace
~~~
kubectl create namespace devops
kubectl create namespace microclimate-pipeline-deployments
kubectl config set-context $(kubectl config current-context) --namespace=devops
~~~

### 2. create secret key  
~~~
kubectl create secret docker-registry microclimate-registry-secret --docker-server=mycluster.icp:8500 --docker-username=admin --docker-password=admin --docker-email=null -n devops
~~~

### 3. check image policy and add new one if the default is not allowed all
~~~
kubectl get clusterimagepolicy
NAME                                    AGE
ibmcloud-default-cluster-image-policy   2d
kubectl edit clusterimagepolicy ibmcloud-default-cluster-image-policy
~~~

~~~
  - name: mycluster.icp:8500:*
  - name: docker.io/maven:*
  - name: docker.io/lachlanevenson/k8s-helm:*
  - name: docker.io/jenkins/*
~~~

### create helm secret
~~~
cloudctl login -a https://mycluster.icp:8443 --skip-ssl-validation
kubectl create secret generic microclimate-helm-secret --from-file=cert.pem=$HELM_HOME/cert.pem --from-file=ca.pem=$HELM_HOME/ca.pem --from-file=key.pem=$HELM_HOME/key.pem
~~~

~~~
kubectl create secret docker-registry microclimate-pipeline-secret --docker-server=mycluster.icp:8500 --docker-username=admin --docker-password=admin --docker-email=null --namespace=microclimate-pipeline-deployments
~~~

### patch service account
~~~
kubectl describe serviceaccount default --namespace microclimate-pipeline-deployments
kubectl patch serviceaccount default --namespace microclimate-pipeline-deployments -p '{"imagePullSecrets": [{"name": "microclimate-pipeline-secret"}]}'
~~~
- preventing replace existing secret
~~~
kubectl patch serviceaccount default --namespace microclimate-pipeline-deployments -p '{"imagePullSecrets": [{"name": "microclimate-pipeline-secret"}, {"name": "secret-1"}, ...., {"name": "secret-n"} ]}'
~~~

### create PV
- create PVC & PV for Microclimate w/ReadWriteMany, Jenkins w/ReadWriteOnce

### add helm repo and install
~~~
helm repo add ibm-charts https://raw.githubusercontent.com/IBM/charts/master/repo/stable/
helm install --name microclimate --namespace devops --set global.rbac.serviceAccountName=micro-sa,jenkins.rbac.serviceAccountName=pipeline-sa,hostName=microclimate.192.168.xx.xx.nip.io,jenkins.Master.HostName=jenkins.192.168.xx.xx.nip.io,persistence.useDynamicProvisioning=false ibm-charts/ibm-microclimate --tls
~~~

### Remarks
~~~
kubectl get ingress -l release=<release_name>
kubectl get all -l chart=ibm-microclimate-x.y.z

helm delete microclimate5 --purge --tls
helm upgrade microclimate -f overrides.yaml ibm-charts-public/ibm-microclimate --reuse-values --tls

kubectl create secret tls ibm-microclimate-tls-secret --key ca.key --cert ca.crt --dry-run -o yaml | kubectl replace --force -f - secret/ibm-microclimate-tls-secret replaced
kubectl get nodes -l proxy=true -o yaml | grep -B 1 ExternalIP
~~~
