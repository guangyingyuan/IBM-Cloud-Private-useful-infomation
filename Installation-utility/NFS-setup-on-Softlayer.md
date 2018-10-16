# softlayer nfs mount
    1. IBM Cloud NFS order Endurance 10 IOPS at portal storage menu
    2. authorize to each VM server at portal
    3. mount to each master node

    mkdir -p /var/lib/registry
    mount -t nfs fsf-seo0101b-fz.adn.networklayer.com:/IBM02SEV1556965_1/data01  /var/lib/registry

    mkdir -p /var/lib/icp/audit
    mount -t nfs fsf-seo0101b-fz.adn.networklayer.com:/IBM02SEV1556965_2/data01  /var/lib/icp/audit

    mkdir -p /var/log/audit
    mount -t nfs fsf-seo0101a-fz.adn.networklayer.com:/IBM02SEV1556965_3/data01  /var/log/audit