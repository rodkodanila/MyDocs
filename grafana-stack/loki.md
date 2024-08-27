# 1. Install go
```
wget https://go.dev/dl/go1.23.0.linux-amd64.tar.gz
tar -C /usr/local -xzf go*.tar.gz
export PATH=$PATH:/sbin/:/usr/local/go/bin
go version
```
# 2. Download and build loki
```
git clone https://github.com/grafana/loki
go build ./cmd/loki
mv loki /usr/local/bin/
mkdir /etc/loki
mv cmd/loki/loki-local-config.yaml /etc/loki/
```

# 3. Change /tmp to /opt/loki
```
sed -i 's/\/tmp\/wal/\/opt\/loki\/wal/g' /etc/loki/loki-local-config.yaml
sed -i 's/\/tmp\/loki/\/opt\/loki/g' /etc/loki/loki-local-config.yaml
mkdir /opt/loki
/usr/local/bin/loki -config.file=/etc/loki/loki-local-config.yaml
```

# 4. Generate autostart
```
useradd --no-create-home --shell /bin/false loki
chown loki:loki /usr/local/bin/loki
chown -R loki:loki /etc/loki
chown -R loki:loki /opt/loki
```

```
cat << EOF > /etc/systemd/system/loki.service
[Unit]
Description=Grafana Loki Service
After=network.target

[Service]
User=loki
Group=loki
Type=simple
ExecStart=/usr/local/bin/loki -config.file=/etc/loki/loki-local-config.yaml
ExecReload=/bin/kill -HUP $MAINPID
Restart=on-failure

[Install]
WantedBy=multi-user.target
EOF
```

```
systemctl daemon-reload
systemctl enable loki --now
systemctl status loki

```


