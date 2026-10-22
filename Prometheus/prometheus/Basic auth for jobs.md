Edit the `Prometheus` configuration file

```bash
vi /etc/prometheus/prometheus.yml
```

Under `- job_name: "nodes"` add below lines: ([[prometheus.yml]])

```bash
basic_auth:
  username: prometheus
  password: secret-password
```

Restart `prometheus` service:

```bash
systemctl restart prometheus
```