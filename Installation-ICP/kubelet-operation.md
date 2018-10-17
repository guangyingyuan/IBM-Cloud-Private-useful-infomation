### kubelet stop/start/status check
~~~
systemctl list-unit-files | grep docker 
systemctl list-units --type=service --state=running
systemctl list-units --type=service --state=active
~~~

~~~
ls /etc/systemd/system/*  | grep kubelet
ls /run/systemd/system/* | grep kubelet
ls /lib/systemd/system/* | grep kubelet
~~~

~~~
ls /lib/systemd/system/docker.service
ls /etc/systemd/system/kubelet.service
~~~

~~~
kubectl cordon 9.111.255.122
kubectl uncordon 9.111.255.122
~~~

~~~
sudo systemctl stop docker
sudo systemctl stop kubelet
sudo systemctl status kubelet
~~~

~~~
sudo journalctl -e -u kubelet
sudo journalctl -f -u kubelet
~~~
