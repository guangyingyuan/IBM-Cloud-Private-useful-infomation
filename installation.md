# :hamster: Installation tip for ICP v3.1 :hamster:

### Installation 
ICP ee install       https://www.ibm.com/support/knowledgecenter/SSBS6K_2.1.0.3/kc_welcome_containers.html
ICP service catalog https://github.com/kubernetes-incubator/service-catalog/blob/v0.1.11/docs/install.md
ICP ce install       https://www.ibm.com/support/knowledgecenter/en/SSBS6K_2.1.0/installing/install_containers_CE.html
ICP ne install       https://www.ibm.com/support/knowledgecenter/SSBS6K_2.1.0/installing/install.html

docker install    https://docs.docker.com/install/linux/docker-ce/ubuntu/#install-docker-ce-1
docker-compose https://docs.docker.com/compose/install/#install-compose
docker ee install https://docs.docker.com/install/linux/docker-ee/ubuntu/
docker ee URL   https://storebits.docker.com/ee/ubuntu/sub-1fee5906-f1cf-4836-84cd-d1200124ef1e

CLI install          https://github.com/patrocinio/cloud-private-bootcamp/blob/master/Labs_development/Lab-Install-CLI-Tools_DJM.md
IBM dev CLI     https://console.bluemix.net/docs/cli/index.html#overview
IBM Cloud cli     https://console.bluemix.net/docs/cli/index.html#cli
kubectl ClI install   https://kubernetes.io/docs/tasks/tools/install-kubectl/#install-kubectl   

Helm setup        https://www.ibm.com/support/knowledgecenter/SSBS6K_2.1.0.3/app_center/create_helm_cli.html            
Helm install       https://docs.helm.sh/using_helm/#installing-helm

socat                 https://www.howtoinstall.co/en/ubuntu/xenial/socat
docker load       http://pyrasis.com/book/DockerForTheReallyImpatient/Chapter20/16
Helm do            https://docs.helm.sh/using_helm/#using-helm
IBM/charts        https://www.ibm.com/support/knowledgecenter/SSBS6K_2.1.0.3/getting_started/enterprise_catalog.html
htop install        https://linuxhint.com/install-htop-ubuntu/

### preparation
- utility installation : ansible nfs-common, screen
- sharing ssh
- nfs shared disk
- haproxy 
- config.yaml
- hosts
- /etc/hosts
- /etc/ansible/hosts

### installation command
~~~
sudo docker run --net=host -t -e LICENSE=accept -v "$(pwd)":/installer/cluster ibmcom/icp-inception-amd64:3.1.0-ee  install
sudo docker run -e LICENSE=accept --net=host   -v "$(pwd)":/installer/cluster   ibmcom/icp-inception-amd64:3.1.0-ee  worker -l  169.56.80.212 
sudo docker run -e LICENSE=accept --net=host   -v "$(pwd)":/installer/cluster   ibmcom/icp-inception-amd64:3.1.0-ee  uninstall -l  169.56.80.212 
sudo docker run --net=host -t -e LICENSE=accept -v $(pwd):/installer/cluster ibmcom/icp-inception-amd64:latest-ee uninstall
~~~


