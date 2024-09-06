# install promtail binary
```
curl -O -L "https://github.com/grafana/loki/releases/download/latest/promtail-linux-amd64.zip"
unzip "promtail-linux-amd64.zip"
sudo chmod a+x "promtail-linux-amd64"

cat << EOF > config-promtail.yml
server:
  http_listen_port: 9080
  grpc_listen_port: 0

positions:
  filename: /tmp/positions.yaml

clients:
  - url: http://localhost:3100/loki/api/v1/push

scrape_config_files:
  - /etc/promtail/projects/*
EOF

sudo useradd --system promtail
cat << EOF > /etc/systemd/system/promtail.service
[Unit]
Description=Promtail service
After=network.target

[Service]
Type=simple
User=promtail
ExecStart=/usr/local/bin/promtail-linux-amd64 -config.file /usr/local/bin/config-promtail.yml

[Install]
WantedBy=multi-user.target
EOF
sudo service promtail start
sudo service promtail status
usermod -a -G adm promtail
sudo service promtail restart
sudo service promtail status
sudo systemctl enable promtail.service

```
