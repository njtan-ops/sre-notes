SSH to `node01`

```bash
ssh root@node01
```

Create the config:

```bash
mkdir /etc/node_exporter/
touch /etc/node_exporter/config.yml
chmod 700 /etc/node_exporter
chmod 600 /etc/node_exporter/config.yml
chown -R node_exporter:node_exporter /etc/node_exporter
```

Edit `node_exporter` service

```bash
vi /etc/systemd/system/node_exporter.service
```

Change below line:

```bash
ExecStart=/usr/local/bin/node_exporter
```

to

```bash
ExecStart=/usr/local/bin/node_exporter --web.config.file=/etc/node_exporter/config.yml 
```

Reload the `daemon` and Restart `node_exporter` service

```bash
systemctl daemon-reload
systemctl restart node_exporter
exit
```