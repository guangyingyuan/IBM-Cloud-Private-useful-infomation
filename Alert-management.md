# Alter manager

## Alert event flow overview      
<p align="center" >
<img width=700 src="https://github.com/moreal70/IBM-Cloud-Private-useful-infomation/blob/master/images/alert-process.jpg">
</p>

- aler manager pod is resided on Management node
<p align="center" >
<img width=700 src="https://github.com/moreal70/IBM-Cloud-Private-useful-infomation/blob/master/images/alert-manager-pod.jpg">
</p>

<p align="center" >
<img width=700 src="https://github.com/moreal70/IBM-Cloud-Private-useful-infomation/blob/master/images/alert-manager-portal.jpg">
</p>

<p align="center" >
<img width=700 src="https://github.com/moreal70/IBM-Cloud-Private-useful-infomation/blob/master/images/alert-manager-configmap.jpg">
</p>


- how to set up alert	https://developer.ibm.com/recipes/tutorials/setting-up-system-alerts-in-an-ibm-cloud-private-environment/
- config map	https://www.ibm.com/support/knowledgecenter/SSBS6K_3.1.0/manage_metrics/monitoring_service.html
- alert rule	https://github.com/ibm-cloud-architecture/CSMO-ICP/tree/master/prometheus/alerts_prometheus2.x

## Webhook
- snmp	https://github.com/chrusty/prometheus_webhook_snmptrapper
- snmp trapper 	https://github.com/ibm-cloud-architecture/CSMO-ICP/tree/master/prometheus/alertmanager_to_snmp

## CEM
- CEM dashboard	https://devcluster.icp/cemui/administration
- icp set up	https://medium.com/@niklaushirt/ibm-cloud-private-alerting-with-prometheus-bc01e2f9b518
- icp set up	https://github.com/niklaushirt/alertmanager_configuration
- CEM manual	https://www.ibm.com/support/knowledgecenter/en/SSURRN/com.ibm.cem.doc/em_prometheus.html

## Remarks
- parameter	https://www.robustperception.io/whats-the-difference-between-group_interval-group_wait-and-repeat_interval
- amtool	https://github.com/prometheus/alertmanager/blob/master/README.md

### example
- alert manager
~~~
apiVersion: v1
data:
  alertmanager.yml: |-
    global:
    receivers:
      - name: default-receiver
        webhook_configs:
        - url: 'http://10.10.80.119:9999'
    route:
      group_by: ['alertname','instance','kubernetes_namespace','pod','container']
      group_wait: 10s
      group_interval: 5m
      receiver: default-receiver
      repeat_interval: 3h
      routes:
      - receiver: default-receiver
        match:
          alertname: ICPMonitoringHeartbeat
        repeat_interval: 5m
kind: ConfigMap
metadata:
  labels:
    app: monitoring-prometheus
    chart: ibm-icpmonitoring-1.1.0
    component: alertmanager
    heritage: Tiller
    release: monitoring
  name: monitoring-prometheus-alertmanager
  namespace: kube-system
  selfLink: /api/v1/namespaces/kube-system/configmaps/monitoring-prometheus-alertmanager
~~~

- alert rule
~~~
apiVersion: v1
data:
  icp_base_rules.yml: |
    groups:
    - name: node.rules
      rules:
      - alert: ICPNodeExporterDown
        expr: absent(up{component="nodeexporter"} == 1)
        for: 10m
        labels:
          severity: warning
        annotations:
          description: Prometheus could not scrape a node-exporter for more than 10m,
            or node-exporters have disappeared from discovery.
          summary: node-exporter cannot be scraped
      - alert: ICPNodeOutOfDisk
        expr: kube_node_status_condition{condition="OutOfDisk",status="true"} == 1
        labels:
          service: ICP
          severity: critical
        annotations:
          description: '{{ $labels.node }} has run out of disk space.'
          summary: Node ran out of disk space.
      - alert: ICPNodeMemoryPressure
        expr: kube_node_status_condition{condition="MemoryPressure",status="true"} ==
          1
        labels:
          service: ICP
          severity: warning
        annotations:
          description: '{{ $labels.node }} is under memory pressure.'
          summary: Node is under memory pressure.
      - alert: ICPNodeDiskPressure
        expr: kube_node_status_condition{condition="DiskPressure",status="true"} == 1
        labels:
          service: ICP
          severity: warning
        annotations:
          description: '{{ $labels.node }} is under disk pressure.'
          summary: Node is under disk pressure.
      - alert: ICPHostCPUUtilisation
        expr: 100 - (avg by(instance) (irate(node_cpu{mode="idle"}[5m])) * 100) > 70
        for: 20m
        labels:
          severity: warning
        annotations:
          description: 'High CPU utilisation detected for instance {{ $labels.instance_id
            }} tagged as: {{ $labels.instance_name_tag }}, the utilisation is currently:
            {{ $value }}%'
          summary: CPU Utilisation Alert
      - alert: ICPPreditciveHostDiskSpace
        expr: predict_linear(node_filesystem_free{mountpoint="/"}[4h], 4 * 3600) < 0
        for: 30m
        labels:
          severity: warning
        annotations:
          description: 'Based on recent sampling, the disk is likely to will fill on volume
            {{ $labels.mountpoint }} within the next 4 hours for instace: {{ $labels.instance_id
            }} tagged as: {{ $labels.instance_name_tag }}'
          summary: Predictive Disk Space Utilisation Alert
      - alert: ICPHostHighMemeoryLoad
        expr: (sum(node_memory_MemTotal) - sum(node_memory_MemFree + node_memory_Buffers
          + node_memory_Cached)) / sum(node_memory_MemTotal) * 100 > 85
        labels:
          severity: warning
        annotations:
          description: Memory of a host is almost full for instance {{ $labels.instance_id
            }}
          summary: Memory utilization Alert
      - alert: ICPNodeSwapUsage
        expr: (((node_memory_SwapTotal - node_memory_SwapFree) / node_memory_SwapTotal)
          * 100) > 75
        for: 2m
        labels:
          severity: warning
        annotations:
          description: '{{$labels.instance}}: Swap usage usage is above 75% (current value
            is: {{ $value }})'
          summary: '{{$labels.instance}}: Swap usage detected'
    - name: prometheus.rules
      rules:
      - alert: PrometheusConfigReloadFailed
        expr: prometheus_config_last_reload_successful == 0
        for: 10m
        labels:
          severity: warning
        annotations:
          description: Reloading Prometheus configuration has failed for {{ $labels.namespace
            }}/{{ $labels.pod}}.
          summary: Prometheus configuration reload has failed
      - alert: PrometheusNotificationQueueRunningFull
        expr: predict_linear(prometheus_notifications_queue_length[5m], 60 * 30) > prometheus_notifications_queue_capacity
        for: 10m
        labels:
          severity: warning
        annotations:
          description: Prometheus alert notification queue is running full for {{$labels.namespace}}/{{$labels.pod}}
          summary: Prometheus alert notification queue is running full
      - alert: PrometheusErrorSendingAlerts
        expr: rate(prometheus_notifications_errors_total[5m]) / rate(prometheus_notifications_sent_total[5m])
          > 0.01
        for: 10m
        labels:
          severity: warning
        annotations:
          description: Errors while sending alerts from Prometheus {{$labels.namespace}}/{{$labels.pod}}
            to Alertmanager {{$labels.Alertmanager}}
          summary: Errors while sending alert from Prometheus
      - alert: PrometheusErrorSendingAlerts
        expr: rate(prometheus_notifications_errors_total[5m]) / rate(prometheus_notifications_sent_total[5m])
          > 0.03
        for: 10m
        labels:
          severity: critical
        annotations:
          description: Errors while sending alerts from Prometheus {{$labels.namespace}}/{{$labels.pod}}
            to Alertmanager {{$labels.Alertmanager}}
          summary: Errors while sending alert from Prometheus
      - alert: PrometheusNotConnectedToAlertmanagers
        expr: prometheus_notifications_alertmanagers_discovered < 1
        for: 10m
        labels:
          severity: warning
        annotations:
          description: Prometheus {{ $labels.namespace }}/{{ $labels.pod}} is not connected
            to any Alertmanagers
          summary: Prometheus is not connected to any Alertmanagers
    - name: kubelet.rules
      rules:
      - alert: ICPNodeNotReady
        expr: kube_node_status_condition{condition="Ready",status="true"} == 0
        for: 1m
        labels:
          severity: warning
        annotations:
          description: The Kubelet on {{ $labels.node }} has not checked in with the API,
            or has set itself to NotReady, for more than an hour
          summary: Node status is NotReady
      - alert: ICPManyNodesNotReady
        expr: count(kube_node_status_condition{condition="Ready",status="true"} == 0)
          > 1 and (count(kube_node_status_condition{condition="Ready",status="true"} ==
          0) / count(kube_node_status_condition{condition="Ready",status="true"})) > 0.2
        for: 1m
        labels:
          severity: critical
        annotations:
          description: '{{ $value }} Kubernetes nodes (more than 10% are in the NotReady
            state).'
          summary: Many Kubernetes nodes are Not Ready
      - alert: ICPKubeletDown
        expr: count(up{job="kubernetes-nodes"} == 0) / count(up{job="kubernetes-nodes"})
          > 0.03
        for: 1h
        labels:
          severity: warning
        annotations:
          description: Prometheus failed to scrape {{ $value }}% of kubelets.
          summary: Many Kubelets cannot be scraped
      - alert: ICPKubeletDown
        expr: absent(up{job="kubernetes-nodes"} == 1) or count(up{job="kubernetes-nodes"}
          == 0) / count(up{job="kubernetes-nodes"}) > 0.1
        for: 1h
        labels:
          severity: critical
        annotations:
          description: Prometheus failed to scrape {{ $value }}% of kubelets, or all Kubelets
            have disappeared from service discovery.
          summary: Many Kubelets cannot be scraped
      - alert: ICPKubeletTooManyPods
        expr: kubelet_running_pod_count > 110
        labels:
          severity: warning
        annotations:
          description: Kubelet {{$labels.instance}} is running {{$value}} pods, close
            to the limit of 110
          summary: Kubelet is close to pod limit
    - name: kube-apiserver.rules
      rules:
      - alert: ICPApiserverDown
        expr: absent(up{job="kubernetes-apiservers"} == 1)
        for: 5m
        labels:
          severity: critical
        annotations:
          description: Prometheus failed to scrape API server(s), or all API servers have
            disappeared from service discovery.
          summary: API server unreachable
      - alert: ICPApiServerLatency
        expr: histogram_quantile(0.99, sum without(instance, resource) (apiserver_request_latencies_bucket{verb!~"CONNECT|WATCHLIST|WATCH|PROXY"}))
          / 1e+06 > 5
        for: 10m
        labels:
          severity: warning
        annotations:
          description: 99th percentile Latency for {{ $labels.verb }} requests to the
            kube-apiserver is higher than 1s.
          summary: Kubernetes apiserver latency is high
    - name: general.rules
      rules:
      - alert: ICPMonitoringTargetDown
        expr: 100 * (count by(job) (up == 0) / count by(job) (up)) > 20
        for: 10m
        labels:
          severity: warning
        annotations:
          description: '{{ $value }}% or more of {{ $labels.job }} targets are down.'
          summary: Targets are down
      - alert: ICPMonitoringHeartbeat
        expr: vector(1)
        labels:
          severity: none
        annotations:
          description: This is a Hearbeat event meant to ensure that the entire Alerting
            pipeline is functional.
          summary: Alerting Heartbeat
      - alert: ICPTooManyOpenFileDescriptors
        expr: 100 * (process_open_fds / process_max_fds) > 95
        for: 10m
        labels:
          severity: critical
        annotations:
          description: '{{ $labels.job }}: {{ $labels.namespace }}/{{ $labels.pod }} ({{
            $labels.instance }}) is using {{ $value }}% of the available file/socket descriptors.'
          summary: Too many open file descriptors for the monitoring target
      - record: instance:fd_utilization
        expr: process_open_fds / process_max_fds
      - alert: ICPFdExhaustionClose
        expr: predict_linear(instance:fd_utilization[1h], 3600 * 4) > 1
        for: 10m
        labels:
          severity: warning
        annotations:
          description: '{{ $labels.job }}: {{ $labels.namespace }}/{{ $labels.pod }} ({{
            $labels.instance }}) instance will exhaust in file/socket descriptors soon'
          summary: File descriptors soon exhausted for the monitoring target
      - alert: ICPFdExhaustionClose
        expr: predict_linear(instance:fd_utilization[10m], 3600) > 1
        for: 10m
        labels:
          severity: critical
        annotations:
          description: '{{ $labels.job }}: {{ $labels.namespace }}/{{ $labels.pod }} ({{
            $labels.instance }}) instance will exhaust in file/socket descriptors soon'
          summary: File descriptors soon exhausted for the monitoring target
      - alert: ICPPodFrequentlyRestarting
        expr: delta(kube_pod_container_status_restarts[1h]) > 2
        for: 10m
        labels:
          severity: warning
        annotations:
          description: Pod {{$labels.namespaces}}/{{$labels.pod}} is was restarted {{$value}}
            times within the last hour
          summary: Pod is restarting frequently
kind: ConfigMap
metadata:
  labels:
    app: monitoring-prometheus
    component: prometheus
  name: monitoring-prometheus-alertrules
  namespace: kube-system
  selfLink: /api/v1/namespaces/kube-system/configmaps/monitoring-prometheus-alertrules
~~~
