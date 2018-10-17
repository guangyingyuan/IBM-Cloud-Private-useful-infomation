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


### Ref.Url
v3.1 install guide	https://pages.github.ibm.com/IBMPrivateCloud/environments/install_cicd/ 
v3.1 install guide	https://github.ibm.com/ICP-DevOps/icp-deploy-automation    
v3.1 manual	https://www-03preprod.ibm.com/support/knowledgecenter/SSBS6K_3.1.0/user_management/saml.html
HA master	https://github.com/ibm-cloud-architecture/refarch-privatecloud/blob/master/Resiliency/Configure_HA_ICP_cluster.md
post installation check  https://www-03preprod.ibm.com/support/knowledgecenter/SSBS6K_3.1.0/installing/install_proxy.html
vpk		https://github.com/daveweilert/vpk
icp featured	https://9.37.239.93/dev/
gluster	config	https://github.com/gluster/gluster-kubernetes/tree/master/docs/examples/containerized_heketi_dedicated_gluster
gluster install	https://www.itzgeek.com/how-tos/linux/ubuntu-how-tos/install-and-configure-glusterfs-on-ubuntu-16-04-debian-8.html
cam		https://www.ibm.com/support/knowledgecenter/SS2L37_3.1.0.0/kc_welcome.html
ICP HA		https://www.ibm.com/developerworks/community/blogs/5092bd93-e659-4f89-8de2-a7ac980487f0/entry/Availability_considerations_for_single_ICP_cluster_topologies?lang=en
elasticsearch	https://www.youtube.com/watch?v=w7aHzIkD3D4&index=2&list=PLVNY1HnUlO24LCsgOxR_eK2Yi4sOgH9Pg
Add node	https://www.ibm.com/support/knowledgecenter/en/SSBS6K_3.1.0/installing/add_node.html#worker



