Create a docker compose for `cAdvisor`

```yaml
version: '3.4'
services:
  cadvisor:
    image: gcr.io/cadvisor/cadvisor
    container_name: cadvisor
    privileged: true
    devices:
      - "/dev/kmsg:/dev/kmsg"
    volumes:
      - /:/rootfs:ro
      - /var/run:/var/run:ro
      - /sys:/sys:ro
      - /var/lib/docker/:/var/lib/docker:ro
      - /dev/disk/:/dev/disk:ro
    ports:
      - 8070:8080
```

Edit `/etc/prometheus/prometheus.yml` file: ([[prometheus.yml]])

```sh
vi /etc/prometheus/prometheus.yml
```

Add below given lines under `scrape_configs:`

```yaml
  - job_name: "cadvisor"
    static_configs:
      - targets: ["localhost:8070"]
```

Restart `prometheus` service:

```sh
systemctl restart prometheus
```