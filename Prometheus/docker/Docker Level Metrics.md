Create/edit `/etc/docker/daemon.json` file: ([[daemon.json]])

```sh
vi /etc/docker/daemon.json
```

Add below given lines in it:

```json
  "metrics-addr" : "127.0.0.1:9323",
  "experimental" : true
```

The edited file should look similar to this:

```json
{
  "exec-opts": [
    "native.cgroupdriver=cgroupfs"
  ],
  "bip": "172.12.0.1/24",
  "registry-mirrors": [
    "http://docker-registry-mirror.kodekloud.com"
  ],
  "metrics-addr": "127.0.0.1:9323",
  "experimental": true
}
```

Restart `docker` service:

```sh
systemctl restart docker
```

Verify if docker is exporting the metrics now:

```sh
curl localhost:9323/metrics
```


Edit `/etc/prometheus/prometheus.yml` file: ([[prometheus.yml]])

```sh
vi /etc/prometheus/prometheus.yml
```

Add below given lines under `scrape_configs:`

```yaml
  - job_name: "docker"
    static_configs:
      - targets: ["localhost:9323"]
```

Restart `prometheus` service:

```sh
systemctl restart prometheus
```