Do [[Adding config]] first

```bash
ssh root@node01
```

Install `apache2-utils` package:

```sh
apt update
apt install apache2-utils -y
```

Generate password hash:

```sh
htpasswd -nBC 10 "" | tr -d ':\n'; echo
```

It will ask for the password twice as below (enter password `secret-password` twice):

```sh
New password: 
Re-type new password: 
```

Finally, you will get a hashed value of your password.  
Edit `/etc/node_exporter/config.yml` ([[config.yml]]) file:

```sh
vi /etc/node_exporter/config.yml
```

Add below lines in it:

```yml
basic_auth_users:
  prometheus: <hashed-password>
```

Restart `node_exporter` service

```sh
systemctl restart node_exporter
exit
```

You can verify the changes using `curl` command:

```bash
curl http://node01:9100/metrics
```

return output should be `Unauthorized`  
`Note:` Follow same steps for `node02` except generating the password hash, you should be able to use the same password hash for `node02`.

Do [[Basic auth for jobs]] next
Do [[TLS Certification]] next