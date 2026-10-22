```systemd hl:11,
[Unit]
Description=Prometheus Server
Wants=network-online.target
After=network-online.target

[Service]
User=prometheus
Group=prometheus
Type=simple
ExecStart=/usr/bin/local/prometheus \
        --config.file=/etc/prometheus/prometheus.yml \ 
        --storage.tsdb.path=/var/lib/prometheus \
        --web.listen-address=0.0.0.0:9090


[Install]
WantedBy=multi-user.target
```

- See config file: [[prometheus.yml]]