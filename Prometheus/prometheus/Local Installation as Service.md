Navigate to the `prometheus-3.5.0.linux-amd64` directory by executing the following command:

```sh
cd prometheus-3.5.0.linux-amd64
```

Add Prometheus user as below:

```bash
useradd --no-create-home --shell /bin/false prometheus
```

Create Directories for storing prometheus config file and data:

```bash
mkdir /etc/prometheus
mkdir /var/lib/prometheus
```

Change the permissions:

```bash
chown prometheus:prometheus /etc/prometheus
chown prometheus:prometheus /var/lib/prometheus
```

Copy the binaries:

```bash
cp prometheus promtool /usr/local/bin/
```

Change the ownership of binaries:

```bash
chown prometheus:prometheus /usr/local/bin/prometheus
chown prometheus:prometheus /usr/local/bin/promtool
```

Move `prometheus.yml` file to `/etc/prometheus` directory:

```bash
cp prometheus.yml /etc/prometheus/
```

Change the ownership of file `/etc/prometheus/prometheus.yml`:

```bash
chown prometheus:prometheus /etc/prometheus/prometheus.yml
```

Create a service for `prometheus`:

```sh
vi /etc/systemd/system/prometheus.service
```

Add below lines in it: [[prometheus.service]]

```ini
[Unit]
Description=Prometheus Server
Wants=network-online.target
After=network-online.target

[Service]
User=prometheus
Group=prometheus
Type=simple
ExecStart=/usr/local/bin/prometheus \
  --config.file=/etc/prometheus/prometheus.yml \
  --storage.tsdb.path=/var/lib/prometheus \
  --web.listen-address=0.0.0.0:9090

[Install]
WantedBy=multi-user.target
```

**Key Points:**

- **Do not include** the `--web.console.templates` or `--web.console.libraries` flags these are not needed unless you use custom templates. Prometheus will serve the built-in consoles by default in version 3.5.0.

Run below commands:

```bash
systemctl daemon-reload
systemctl start prometheus
systemctl enable prometheus
systemctl status prometheus
```