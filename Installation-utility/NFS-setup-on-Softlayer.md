# :mouse: How to setup NFS on Softlayer for iCP shared storage with multi-master cluster 

### 1. Order file storage (NFS) and authorize for all master node
- 10 iops is recommended for performance
![nfs](https://github.com/moreal70/IBM-Private-Cloud-handsOn/blob/master/images/sl_file_storage.jpg)
- authorize 
![nfs](https://github.com/moreal70/IBM-Private-Cloud-handsOn/blob/master/images/sl_file_storage_authorize.jpg)

### 2. create shared directory & mount
- excute at all master node or use ansible
~~~
mkdir -p /var/lib/registry
mkdir -p /var/lib/icp/audit
mkdir -p /var/log/audit
~~~
- mount with nfs url 
~~~
mount -t nfs fsf-seo0101b-fz.adn.networklayer.com:/IBM02SEV1556965_1/data01  /var/lib/registry
mount -t nfs fsf-seo0101b-fz.adn.networklayer.com:/IBM02SEV1556965_2/data01  /var/lib/icp/audit
mount -t nfs fsf-seo0101a-fz.adn.networklayer.com:/IBM02SEV1556965_3/data01  /var/log/audit
~~~

### :rabbit: Remarks
- Image registry directory	https://www.ibm.com/support/knowledgecenter/en/SSBS6K_2.1.0.3/manage_cluster/ha_registry.html
- nfs client install	https://help.ubuntu.com/community/SettingUpNFSHowTo
- nfs mount	https://console.bluemix.net/docs/infrastructure/FileStorage/accessing-file-storage-linux.html#mounting-file-storage-on-linux

