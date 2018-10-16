# :hamster: Installation tip for ICP v3.1 :hamster:

### 0. preparation


, nfs-common, screen, haproxy
- 
sharing ssh
nfs shared disk
haproxy
screen install 
config.yaml
hosts
/etc/hosts
/etc/ansible/hosts



v3.1 install guide   https://pages.github.ibm.com/IBMPrivateCloud/environments/install_cicd/ 
v3.1 install guide    https://github.ibm.com/ICP-DevOps/icp-deploy-automation    
v3.1 manual          https://www-03preprod.ibm.com/support/knowledgecenter/SSBS6K_3.1.0/user_management/saml.html

HA master            https://github.com/ibm-cloud-architecture/refarch-privatecloud/blob/master/Resiliency/Configure_HA_ICP_cluster.md


post installation check  https://www-03preprod.ibm.com/support/knowledgecenter/SSBS6K_3.1.0/installing/install_proxy.html


sudo docker run --net=host -t -e LICENSE=accept -v $(pwd):/installer/cluster ibmcom/icp-inception-amd64:latest-ee install
sudo docker run --net=host -t -e LICENSE=accept -v $(pwd):/installer/cluster ibmcom/icp-inception-amd64:latest-ee uninstall
