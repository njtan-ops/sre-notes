```yml
# ==== BASIC AUTH ENCRYPTION ====
basic_auth_users:
  prometheus: (hashed password)
# ==== TLS Certificate ====
tls_server_config:
  cert_file: node_exporter.crt
  key_file: node_exporter.key
```