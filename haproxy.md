# HA configuration with external load balancer using haproxy

There are 2 options for HA configure of ICP
  - https://www.ibm.com/support/knowledgecenter/en/SSBS6K_3.1.0/installing/custom_install.html#HA
  - https://www.ibm.com/support/knowledgecenter/en/SSBS6K_3.1.0/installing/set_loadbalancer.html

Here is for 2nd option with haproxy
1. prepare separate node for load balancing    

2. installing & config haproxy   
  - refer to sample on bottom
  ~~~
  apt-get install haproxy  
  vi /etc/haproxy/haproxy.conf
  ~~~

3. start and validate haproxy
  - operation
  ~~~
  systemctl start haproxy
  systemctl stop haproxy
  systemctl status haproxy
  ~~~
  - validating  
  ~~~
  master node> curl  https://169.xx.xx.x14:8443
  curl: (60) server certificate verification failed. CAfile: /etc/ssl/certs/ca-certificates.crt CRLfile: none
  More details here: http://curl.haxx.se/docs/sslcerts.html

  curl performs SSL certificate verification by default, using a "bundle"
   of Certificate Authority (CA) public keys (CA certs). If the default
   bundle file isn't adequate, you can specify an alternate file
   using the --cacert option.
  If this HTTPS server uses a certificate signed by a CA represented in
   the bundle, the certificate verification probably failed due to a
   problem with the certificate (it might be expired, or the name might
   not match the domain name in the URL).
  If you'd like to turn off curl's verification of the certificate, use
   the -k (or --insecure) option.
   ~~~

4. configure config.yaml & hosts  for ICP installation
~~~
  [master]
  169..221
  169.xx.xx.x17
  169.xx.xx.x10
  [worker]
  169.xx.xx.x22
  [proxy]
  169.xx.xx.x18
  169.xx.xx.x12
  [management]
  169.xx.xx.x15
  [lb--optional]  
  169.xx.xx.x14
~~~

- confi.yaml
~~~
cluster_lb_address: 169.xx.xx.x14  
#cluster_vip
#proxy_vip
~~~

5. Installation ICP

6. Sample haproxy.conf : manual example is not working with latest version of haproxy
~~~
global
      log         127.0.0.1 local2
      chroot      /var/lib/haproxy
      pidfile     /var/run/haproxy.pid
      maxconn     4000
      user        haproxy
      group       haproxy
      daemon
      stats socket /var/lib/haproxy/stats            
defaults
      mode                    http
      log                     global
      option                  httplog
      option                  dontlognull
      option 		      http-server-close
      option                  redispatch
      retries                 3
      timeout http-request    10s
      timeout queue           1m
      timeout connect         10s
      timeout client          1m
      timeout server          1m
      timeout http-keep-alive 10s
      timeout check           10s
      maxconn                 3000

frontend k8s-api
      bind *:8001
      mode tcp
      option tcplog
      use_backend k8s-api
backend k8s-api
      mode tcp
      balance roundrobin
      server server1 169.xx.xx.x21:8001
      server server2 169.xx.xx.x10:8001
      server server3 169.xx.xx.x17:8001

frontend dashboard
      bind *:8443
      mode tcp
      option tcplog
      use_backend dashboard
backend dashboard
      mode tcp
      balance roundrobin
      server server1 169.xx.xx.x21:8443
      server server2 169.xx.xx.x10:8443
      server server3 169.xx.xx.x17:8443

frontend auth
      bind *:9443
      mode tcp
      option tcplog
      use_backend auth
backend auth
      mode tcp
      balance roundrobin
      server server1 169.xx.xx.x21:9443
      server server2 169.xx.xx.x10:9443
      server server3 169.xx.xx.x17:9443

frontend registry
      bind *:8500
      mode tcp
      option tcplog
      use_backend registry
backend registry
      mode tcp
      balance roundrobin
      server server1 169.xx.xx.x21:8500
      server server2 169.xx.xx.x10:8500
      server server3 169.xx.xx.x17:8500

frontend image-manager
      bind *:8600
      mode tcp
      option tcplog
      use_backend image-manager
backend image-manager
      mode tcp
      balance roundrobin
      server server1 169.xx.xx.x21:8600
      server server2 169.xx.xx.x10:8600
      server server3 169.xx.xx.x17:8600

frontend proxy-http
      bind *:80
      mode tcp
      option tcplog
      use_backend proxy-http
backend proxy-http
      mode tcp
      balance roundrobin
      server server1 169.xx.xx.x18:80
      server server2 169.xx.xx.x12:80

frontend proxy-https
      bind *:443
      mode tcp
      option tcplog
      use_backend proxy-https
backend proxy-https
      mode tcp
      balance roundrobin
      server server1 169.xx.xx.x18:443
      server server2 169.xx.xx.x12:443
~~~
