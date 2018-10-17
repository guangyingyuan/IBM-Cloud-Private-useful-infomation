### Kubenetes Log
/var/log/kube-apiserver.log - API Server, responsible for serving the API
/var/log/kube-scheduler.log - Scheduler, responsible for making scheduling decisions
/var/log/kube-controller-manager.log - Controller that manages replication controllers

/var/log/kubelet.log - Kubelet, responsible for running containers on the node
/var/log/kube-proxy.log - Kube Proxy, responsible for service load balancing

docker service ls
docker service logs <service ID>
docker logs <container ID>


### Kubenetes trouble shooting
https://kubernetes.io/docs/tasks/debug-application-cluster/debug-application/
https://kubernetes.io/docs/tasks/debug-application-cluster/debug-cluster/
https://kubernetes.io/docs/tasks/debug-application-cluster/troubleshooting/
https://www.ibm.com/support/knowledgecenter/en/SSBS6K_2.1.0.2/getting_started/known_issues.html
504 time out       https://portal2portal.blogspot.com/2018/04/ibm-cloud-private-504-gateway-time-out.html
certification copy https://www.ibm.com/support/knowledgecenter/en/SSBS6K_2.1.0.3/manage_images/configuring_docker_cli.html

~~~
systemctl restart kubelet docker
journalctl -u kubelet -f
systemctl -l|grep -i kubelet.service

systemctl -l|grep -i docker.service
ibmcloud pr login -a https://mycluster.icp:8443 -u admin --skip-ssl-validation
~~~
