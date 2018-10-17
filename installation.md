# :hamster: Installation tip for ICP v3.1 :hamster:

### preparation
- utility installation : ansible nfs-common, screen
- sharing ssh
- nfs shared disk
- haproxy 
- config.yaml
- hosts
- /etc/hosts
- /etc/ansible/hosts

### command

sudo docker run --net=host -t -e LICENSE=accept -v "$(pwd)":/installer/cluster ibmcom/icp-inception-amd64:3.1.0-ee  install
sudo docker run -e LICENSE=accept --net=host   -v "$(pwd)":/installer/cluster   ibmcom/icp-inception-amd64:3.1.0-ee  worker -l  169.56.80.212 
sudo docker run -e LICENSE=accept --net=host   -v "$(pwd)":/installer/cluster   ibmcom/icp-inception-amd64:3.1.0-ee  uninstall -l  169.56.80.212 
sudo docker run --net=host -t -e LICENSE=accept -v $(pwd):/installer/cluster ibmcom/icp-inception-amd64:latest-ee uninstall



