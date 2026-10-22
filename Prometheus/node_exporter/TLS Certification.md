SSH to `node01`

```bash
ssh root@node01
```

Generate the certificate and key

```bash
openssl req -new -newkey rsa:2048 -days 365 -nodes -x509 -keyout node_exporter.key -out node_exporter.crt -subj "/C=US/ST=California/L=Oakland/O=MyOrg/CN=localhost" -addext "subjectAltName = DNS:localhost"
```

Move the `crt` and `key` file under `/etc/node_exporter/` directory

```bash
mv node_exporter.crt node_exporter.key /etc/node_exporter/
```

Change ownership:

```bash
chown node_exporter.node_exporter /etc/node_exporter/node_exporter.key
chown node_exporter.node_exporter /etc/node_exporter/node_exporter.crt
```

Edit `/etc/node_exporter/config.yml` file: ([[config.yml]])

```bash
vi /etc/node_exporter/config.yml
```

Add below lines in this file:

```bash
tls_server_config:
  cert_file: node_exporter.crt
  key_file: node_exporter.key
```

Restart node exporter service:

```bash
systemctl restart node_exporter
exit
```

You can verify your changes using `curl` command:

```bash
curl -u prometheus:secret-password -k https://node01:9100/metrics
```

`Follow same steps for node02`

Copy the certificate from `node01` to `Prometheus` server 

```bash
scp root@node01:/etc/node_exporter/node_exporter.crt /etc/prometheus/node_exporter.crt
```

Change certificate file ownership:

```bash
chown prometheus.prometheus /etc/prometheus/node_exporter.crt
```

Edit `/etc/prometheus/prometheus.yml` file

```bash
vi /etc/prometheus/prometheus.yml 
```

Add below given lines under `- job_name: "nodes"`

```bash
    scheme: https
    tls_config:
      ca_file: /etc/prometheus/node_exporter.crt
      insecure_skip_verify: true
```

Restart prometheus service

```bash
systemctl restart prometheus
```