# :hamster: Installation tip for ICP v3.1 :hamster:

### 0. prereq
ansible
sharing ssh
nfs shared disk
haproxy
screen install 
config.yaml
hosts
/etc/hosts
/etc/ansible/hosts



v3.1 install image  https://na.artifactory.swg-devops.com/artifactory/list/hyc-cloud-private-stable-generic-local/offline/20180826/
download             wget --user=parkhsu@kr.ibm.com  https://na.artifactory.swg-devops.com/artifactory/list/hyc-cloud-private-stable-generic-local/offline/20180905/ibm-cloud-private-x86_64-latest.tar.gz

apt-get install ansible
create /etc/ansible/hosts
ansible all -m command -a "ping -c 3 www.ibm.com"


v3.1 install guide   https://pages.github.ibm.com/IBMPrivateCloud/environments/install_cicd/ 
v3.1 install guide    https://github.ibm.com/ICP-DevOps/icp-deploy-automation    
v3.1 manual          https://www-03preprod.ibm.com/support/knowledgecenter/SSBS6K_3.1.0/user_management/saml.html

shared disk config  https://www.ibm.com/support/knowledgecenter/en/SSBS6K_2.1.0.3/manage_cluster/ha_registry.html
nfs client install     https://help.ubuntu.com/community/SettingUpNFSHowTo
nfs mount             https://console.bluemix.net/docs/infrastructure/FileStorage/accessing-file-storage-linux.html#mounting-file-storage-on-linux

HA master            https://github.com/ibm-cloud-architecture/refarch-privatecloud/blob/master/Resiliency/Configure_HA_ICP_cluster.md

sudo docker run --net=host -t -e LICENSE=accept -v $(pwd):/installer/cluster ibmcom/icp-inception-amd64:latest-ee install

post installation check  https://www-03preprod.ibm.com/support/knowledgecenter/SSBS6K_3.1.0/installing/install_proxy.html


kubectl config set-cluster cluster.local --server=https://169.62.169.131:8001 --insecure-skip-tls-verify=true
kubectl config set-context cluster.local-context --cluster=cluster.local
kubectl config set-credentials admin --token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiJ9.eyJhdF9oYXNoIjoiaHV1ZHRza3h6cmVrM3F5MGVocXMiLCJyZWFsbU5hbWUiOiJjdXN0b21SZWFsbSIsInVuaXF1ZVNlY3VyaXR5TmFtZSI6ImFkbWluIiwiaXNzIjoiaHR0cHM6Ly9teWNsdXN0ZXIuaWNwOjk0NDMvb2lkYy9lbmRwb2ludC9PUCIsImF1ZCI6IjUxOGYxNDQ0ZTg0OWZhMjZkOGNjMGY1NTQyYjRhZWMwIiwiZXhwIjoxNTM2NTAxNDQyLCJpYXQiOjE1MzY0NzI2NDIsInN1YiI6ImFkbWluIiwidGVhbVJvbGVNYXBwaW5ncyI6W119.aEHMATuE5ayqNSGcrntzVX3HJ5o07_bMkBTPi2_x4-trZ1xbVU4077YfxDR9G1dg1Dzyn1OFrfzwnXNc-Yw345VwUyl6NtIKm2n6LT7Meu33BdvSCB8mZI3uJpoMlDkJ15tMwnSQ50FO6JsMlKITj5jmSTKFmcFAsEueSJgcdtFXYcCp6f-KPF1um_-E_wTZ6Aj9dEp_wJLQWI3AwY5uWyCoswkXxT2rsG9uN3H7ZjsspSyA-RYV6NJk9ihRujWdUWWoRhdCaUvNfHLIQhHkhiWNTMJDLyDoxeJsVySV-L-KskJTzHeHjCvsGlGR5WOFpZO6gHgpOICDHeruIrO3vw
kubectl config set-context cluster.local-context --user=admin --namespace=cam-namespace
kubectl config use-context cluster.local-context

SSO implementation    http://www.nextree.co.kr/oauth-2reul-iyonghan-sso-hwangyeong-gucug-1-2/
ibm sso             https://w3-connections.ibm.com/wikis/home?lang=en-us#!/wiki/BlueID%20Single%20Sign-On%20(SSO)%20Self-Boarding%20Process/page/SSO%20with%20blueID%20(IBM%20ID)?section=Register%20Application
ibm sso             https://w3.innovate.ibm.com/tools/sso/home.html

https://www.ibm.com/support/knowledgecenter/en/SSBS6K_3.1.0/apis/sso_apis.html#enable
https://www.ibm.com/support/knowledgecenter/en/SSBS6K_3.1.0/manage_cluster/cli_sso_configure.html


# CAS installation
cas (Central Authentication Service)  https://apereo.github.io/cas/4.2.x/planning/Installation-Requirements.html
apt-get install default-jdk maven
tomcat install         https://www.digitalocean.com/community/tutorials/install-tomcat-9-ubuntu-1804

Gluu install     https://gluu.org/docs/ce/latest/installation-guide/
                    https://gluu.org/docs/ce/latest/admin-guide/saml/

https://10.73.212.136:8443

sudo docker run --net=host -t -e LICENSE=accept -v "$(pwd)":/installer/cluster ibmcom/icp-inception-amd64:latest-ee   uninstall
