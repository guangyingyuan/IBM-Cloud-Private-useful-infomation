# 1st task for new server

- fail2ban log https://www.the-art-of-web.com/system/fail2ban-log/    
- port check  https://www.ibm.com/support/knowledgecenter/en/SSBS6K_2.1.0/supported_system_config/required_ports.html    
- NTP setup http://webdir.tistory.com/208     
- time zone select http://ngee.tistory.com/643     
- gnome & Tightvnc http://www.ctplex.com/tech/ubuntu/tightvnc-server-%EC%84%A4%EC%B9%98%ED%95%98%EA%B8%B0-ubuntu-12-04-14-04-16-04/     
- screen http://www.incodom.kr/Linux/%EA%B8%B0%EB%B3%B8%EB%AA%85%EB%A0%B9%EC%96%B4/screen     

- OS info	https://unix.stackexchange.com/questions/88644/how-to-check-os-and-version-using-a-linux-command  
- apt-get guide	https://blog.outsider.ne.kr/346  
- Fail2ban (linux) https://developer.ibm.com/kr/cloud/softlayer-bluemix-infra/security/2017/08/16/ssh_security_policy_script/   
- vim colorscheme https://alvinalexander.com/linux/vi-vim-editor-color-scheme-colorscheme  

- xorg	https://wiki.gentoo.org/wiki/Xorg/Guide/ko  
- gdm,kdm,lightm	https://askubuntu.com/questions/829108/what-is-gdm3-kdm-lightdm-how-to-install-and-remove-them  
- ubuntu gnome	https://askubuntu.com/questions/766071/install-gnome-shell-on-ubuntu-16-04  

- Tightvnc	http://www.ctplex.com/tech/ubuntu/tightvnc-server-%EC%84%A4%EC%B9%98%ED%95%98%EA%B8%B0-ubuntu-12-04-14-04-16-04/  
- Tightvnc	 http://knowledgelayer.softlayer.com/learning/tightvnc-server-ubuntu-1604  
- Tightvnc	http://blog.naver.com/PostView.nhn?blogId=ssamba&logNo=163333072  

- xstartup gnome	https://almost-a-technocrat.blogspot.kr/2010/06/how-to-start-up-vnc-session-with-gnome.html  
- xstartup gnome	https://unix.stackexchange.com/questions/416027/xstartup-file-for-gnome-3-session-on-ubuntu-16-04  
- xstartup gnome	https://www.digitalocean.com/community/questions/how-to-install-ubuntu-desktop-and-vnc  
- xstartup gnome	https://www.linode.com/docs/applications/remote-desktop/install-vnc-on-ubuntu-16-04/#connect-to-vnc-from-your-desktop  

- xfonts install	 https://askubuntu.com/questions/72122/how-to-install-xorg-x11-fonts    

- anaconda config	https://www.digitalocean.com/community/tutorials/how-to-install-the-anaconda-python-distribution-on-ubuntu-16-04  
- anaconda download https://www.anaconda.com/download/  

- atom	http://tipsonubuntu.com/2016/08/05/install-atom-text-editor-ubuntu-16-04/   
- atom install	https://comcxx11.github.io/com/install-github-atom/  
- matplotlib	 https://blog.naver.com/noxburn/220777157690   

- IBM qiskit	 https://developer.ibm.com/code/2017/05/17/developers-guide-to-quantum-qiskit-sdk/  

- python site info	https://stackoverflow.com/questions/122327/how-do-i-find-the-location-of-my-python-site-packages-directory  
- python 3.6	https://askubuntu.com/questions/865554/how-do-i-install-python-3-6-using-apt-get  

- dpkg unmet	 https://askubuntu.com/questions/140246/how-do-i-resolve-unmet-dependencies-after-adding-a-ppa   
- R install	 https://medium.com/@GalarnykMichael/install-r-and-rstudio-on-ubuntu-12-04-14-04-16-04-b6b3107f7779  
- R studio	 https://www.rstudio.com/products/rstudio/download-server/  
- go install	 https://golang.org/doc/install  
- node.js install		https://nodejs.org/ko/download/package-manager/  
                           https://github.com/nodesource/distributions#debinstall  
- docker install	 https://docs.docker.com/install/linux/docker-ce/ubuntu/#install-docker-ce-1  
                          https://docs.docker.com/compose/install/#install-compose  
- IBM blockchain	 https://ibm-blockchain.github.io/setup/  
- Kuberctl install	 https://kubernetes.io/docs/tasks/kubectl/install/  

- HyperLedger Fab		http://blog.naver.com/alice_k106/221127478495  
- HyperLedger	 http://hyperledger-fabric.readthedocs.io/en/latest/prereqs.html  

- gcloud SDKinstall	 https://cloud.google.com/sdk/docs/quickstart-debian-ubuntu  
- Bluemix cli install	 http://clis.ng.bluemix.net/ui/home.html  

- ADD a disk in SL	 https://knowledgelayer.softlayer.com/procedure/adding-new-drive-linux  
https://wikidocs.net/4338
- utf-8 error,fbprophet	https://github.com/pypa/pip/issues/4251  


- add 2nd disk on softlayer
~~~
fdisk -l
fdisk /dev/xvdc
mkfs.ext4 /dev/xvdc1
mount
~~~
- fail2ban install       
~~~
wget https://raw.githubusercontent.com/FunctionClub/Fail2ban/master/fail2ban.sh && bash fail2ban.sh 2>&1 | tee fail2ban.log
~~~
