### hosts example
- /etc/hosts
~~~
169.xx.xx.212    et01.seo01.co.kr et01
169.xx.xx.215    mg01.seo01.co.kr mg01
169.xx.xx.221    ms01.seo01.co.kr ms01
169.xx.xx.210    ms02.seo01.co.kr ms02
169.xx.xx.214    ms03.seo01.co.kr ms03
169.xx.xx.218    px01.seo01.co.kr px01
169.xx.xx.217    va01.seo01.co.kr va01
169.xx.xx.222    wk01.seo01.co.kr wk01

- <install_dir>/cluster/hosts
[master]
169.xx.xx.221    
169.xx.xx.210    
169.xx.xx.214    
[worker]
169.xx.xx.222
[proxy]
169.xx.xx.218
[management]
169.xx.xx.215
[va]
169.xx.xx.217  
[etcd]
169.xx.xx.212     