### sample application
pet clinic	https://github.ibm.com/jeongkm/petclinic-on-icp
kube-toy	https://github.com/IBM-ICP-CoC/KubeToy
nodejs-helm	https://github.com/ibm-developer/icp-nodejs-sample


### Application load test

for i in {1..10}; do curl 172.17.17.198; done
watch -n 1 'date ; curl http://169.56.80.214:31711/about.html '
