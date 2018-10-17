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
cam		https://www.ibm.com/support/knowledgecenter/SS2L37_3.1.0.0/kc_welcome.html
ICP HA		https://www.ibm.com/developerworks/community/blogs/5092bd93-e659-4f89-8de2-a7ac980487f0/entry/Availability_considerations_for_single_ICP_cluster_topologies?lang=en
elasticsearch	https://www.youtube.com/watch?v=w7aHzIkD3D4&index=2&list=PLVNY1HnUlO24LCsgOxR_eK2Yi4sOgH9Pg


- node maintenance
replacing master node https://www.ibm.com/support/knowledgecenter/en/SSBS6K_2.1.0.3/troubleshoot/replace_master_node.html  
add a worker node https://www.ibm.com/support/knowledgecenter/en/SSBS6K_2.1.0.3/installing/add_worker.html  
removing nodes     https://www.ibm.com/support/knowledgecenter/en/SSBS6K_2.1.0.3/installing/remove_worker.html  
Add node	https://www.ibm.com/support/knowledgecenter/en/SSBS6K_3.1.0/installing/add_node.html#worker  

- backup
docker registry https://github.com/ibm-cloud-architecture/icp-backup/blob/master/docs/registry.md  
PV backup	https://github.com/ibm-cloud-architecture/icp-backup/blob/master/docs/pvs.md  

- medium ICP backup
https://medium.com/@epatro/how-to-back-up-an-ibm-cloud-private-environment-7bb0a5176c0b  
https://medium.com/ibm-cloud/how-to-backup-and-restore-ibm-cloud-private-part-1-b6300dc1d7d8  
https://medium.com/ibm-cloud/integration-of-ibm-kubernetes-deployments-with-ibm-cloud-object-storage-via-heptio-ark-9097c58b5330  

# CSMO
https://github.com/ibm-cloud-architecture/CSMO-ICP
https://www.ibm.com/blogs/bluemix/2018/01/dashboards-for-ibm-cloud-private/

# DR issue
https://github.ibm.com/IBMPrivateCloud/roadmap/issues/6424


# GlusterFS  
https://www.ibm.com/support/knowledgecenter/en/SSBS6K_3.1.0/manage_cluster/glusterfs_recover_data.html

# heptio - backup solution
https://github.com/heptio/ark
