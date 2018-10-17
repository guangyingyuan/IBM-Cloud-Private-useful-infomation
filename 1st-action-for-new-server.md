# 1st task for new server

fail2ban log https://www.the-art-of-web.com/system/fail2ban-log/ 
port check  https://www.ibm.com/support/knowledgecenter/en/SSBS6K_2.1.0/supported_system_config/required_ports.html  
NTP setup	http://webdir.tistory.com/208  
time zone select	http://ngee.tistory.com/643  
gnome & Tightvnc	http://www.ctplex.com/tech/ubuntu/tightvnc-server-%EC%84%A4%EC%B9%98%ED%95%98%EA%B8%B0-ubuntu-12-04-14-04-16-04/  
screen	http://www.incodom.kr/Linux/%EA%B8%B0%EB%B3%B8%EB%AA%85%EB%A0%B9%EC%96%B4/screen  


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
