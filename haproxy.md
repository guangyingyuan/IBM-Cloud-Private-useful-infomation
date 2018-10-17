# Configure haproxy


- /etc/haproxy/haproxy.conf
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
      server server1 169.56.80.221:8001
      server server2 169.56.80.210:8001
      server server3 169.56.80.217:8001

frontend dashboard
      bind *:8443
      mode tcp
      option tcplog
      use_backend dashboard
backend dashboard
      mode tcp
      balance roundrobin
      server server1 169.56.80.221:8443
      server server2 169.56.80.210:8443
      server server3 169.56.80.217:8443

frontend auth 
      bind *:9443
      mode tcp
      option tcplog
      use_backend auth
backend auth
      mode tcp
      balance roundrobin
      server server1 169.56.80.221:9443
      server server2 169.56.80.210:9443
      server server3 169.56.80.217:9443

frontend registry
      bind *:8500
      mode tcp
      option tcplog
      use_backend registry
backend registry
      mode tcp
      balance roundrobin
      server server1 169.56.80.221:8500
      server server2 169.56.80.210:8500
      server server3 169.56.80.217:8500
  
frontend image-manager 
      bind *:8600
      mode tcp
      option tcplog
      use_backend image-manager
backend image-manager
      mode tcp
      balance roundrobin
      server server1 169.56.80.221:8600
      server server2 169.56.80.210:8600
      server server3 169.56.80.217:8600

frontend proxy-http
      bind *:80
      mode tcp
      option tcplog
      use_backend proxy-http
backend proxy-http
      mode tcp
      balance roundrobin
      server server1 169.56.80.218:80
      server server2 169.56.80.212:80

frontend proxy-https
      bind *:443
      mode tcp
      option tcplog
      use_backend proxy-https
backend proxy-https
      mode tcp
      balance roundrobin
      server server1 169.56.80.218:443
      server server2 169.56.80.212:443
~~~
