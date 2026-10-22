Create a new file `/etc/prometheus/node-rules.yaml`:  

```bash
vi /etc/prometheus/node-rules.yaml
```

Add below lines in it:  

```bash
groups:
  - name: node
    interval: 15s
    rules:
      - record: node_network_receive_bytes_rate
        expr: rate(node_network_receive_bytes_total{job="nodes"}[2m])
```
  
Edit `/etc/prometheus/prometheus.yml` file:  

```bash
vi /etc/prometheus/prometheus.yml
```
  
Add below line under `rule_files:` section:  

```bash
  - "node-rules.yaml"
```
  
Restart the `prometheus` service:  

```bash
systemctl restart prometheus
```


Edit `/etc/prometheus/node-rules.yaml` file:  

```bash
vi /etc/prometheus/node-rules.yaml
```
  
Add the required rule so that the file looks like below:  

```yaml
groups:
  - name: node
    interval: 15s
    rules:
      - record: node_network_receive_bytes_rate
        expr: rate(node_network_receive_bytes_total{job="nodes"}[2m])
      - record: node_network_receive_bytes_rate_avg
        expr: avg by(instance) (node_network_receive_bytes_rate)
```
  
Restart the `prometheus` service:  

```bash
systemctl restart prometheus
```


Edit `/etc/prometheus/node-rules.yaml` file:  
  
```bash
vi /etc/prometheus/node-rules.yaml
```
  
Add the required rule so that the file looks like below:  

```bash
groups:
  - name: node
    interval: 15s
    rules:
      - record: node_network_receive_bytes_rate
        expr: rate(node_network_receive_bytes_total{job="nodes"}[2m])
      - record: node_network_receive_bytes_rate_avg
        expr: avg by(instance) (node_network_receive_bytes_rate)
      - record: node_filesystem_free_percent
        expr: 100 * node_filesystem_free_bytes{job="nodes"} / node_filesystem_size_bytes{job="nodes"}
```

Restart the `prometheus` service:  
  
```bash
systemctl restart prometheus
```


Create a new file `/etc/prometheus/api-rules.yaml`:  
  
```bash
vi /etc/prometheus/api-rules.yaml
```

Add below lines in it:  

```bash
groups:
  - name: api
    interval: 15s
    rules:
      - record: avg_latency_2m
        expr: rate(http_request_total_sum{job="api"}[2m]) / rate(http_request_total_count{job="api"}[2m])
```
  
Edit `/etc/prometheus/prometheus.yml` file:  

```bash
vi /etc/prometheus/prometheus.yml
```
  
Add below line under `rule_files:` section:  

```bash
  - "api-rules.yaml"
```
  
Restart `prometheus` service:  

```bash
systemctl restart prometheus
```