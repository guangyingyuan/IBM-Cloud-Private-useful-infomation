### ICP SSO
- https://w3-connections.ibm.com/wikis/home?lang=en-us#!/wiki/BlueID%20Single%20Sign-On%20(SSO)%20Self-Boarding%20Process/page/SSO%20with%20blueID%20(IBM%20ID)?section=Register%20Application
- https://w3.innovate.ibm.com/tools/sso/home.html
- https://www.ibm.com/support/knowledgecenter/en/SSBS6K_3.1.0/apis/sso_apis.html#enable
- https://www.ibm.com/support/knowledgecenter/en/SSBS6K_3.1.0/manage_cluster/cli_sso_configure.html

~~~
cloudctl iam saml-status
cloudctl iam saml-enable
cloudctl iam saml-disable
cloudctl iam saml-export-metadata --file <file_name>.xml
cloudctl iam saml-upload-metadata --file <file_name>.xml

curl -v -k -X PUT --header 'Authorization: Bearer $ACCESS_TOKEN' --header 'Content-Type: application/json' -d '{"enable": true}' https://169.56.xxx:8443/idmgmt/v1/saml/management
curl -k -v -X GET --header 'Authorization: Bearer $ACCESS_TOKEN' https://169.56.xxx:8443/idauth/ibm/saml20/defaultSP/samlmetadata
curl -v -k -X POST --header 'Authorization: Bearer $ACCESS_TOKEN' -F 'data=@samlidp2_IBM_metadata_CIS_STAGE.xml' https://169.56.xxx:8443/idmgmt/v1/saml/upload
curl -k -v -X GET --header 'Authorization: Bearer $ACCESS_TOKEN' https://169.56.xxx:8443/idmgmt/v1/saml/status
curl -v -k -X PUT --header 'Authorization: Bearer $ACCESS_TOKEN' --header 'Content-Type: application/json' -d '{"enable": false}'  https://169.56.xxx:8443/idmgmt/v1/saml/management
~~~


### CAS
- cas (Central Authentication Service)  https://apereo.github.io/cas/4.2.x/planning/Installation-Requirements.html  
- tomcat install         https://www.digitalocean.com/community/tutorials/install-tomcat-9-ubuntu-1804  
~~~
apt-get install default-jdk maven
~~~

### Gluu
- installation	https://gluu.org/docs/ce/latest/installation-guide/  
- document	https://gluu.org/docs/ce/latest/admin-guide/saml/  
~~~
service gluu-server-3.1.3.1 start
service gluu-server-3.1.3.1 stop
service gluu-server-3.1.3.1 login 
~~~

### Remarks
- nextree	http://www.nextree.co.kr/oauth-2reul-iyonghan-sso-hwangyeong-gucug-1-2/  




