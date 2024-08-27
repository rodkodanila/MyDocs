# 1. Download Prometheus 
```
wget https://github.com/prometheus/prometheus/releases/download/v2.54.0/prometheus-2.54.0.linux-amd64.tar.gz
```
# 2. Install Prometheus
```
sudo tar -xzf prometheus*.tar.gz
sudo rm prometheus*.tar.gz
cd prometheus*/
sudo groupadd --system prometheus
sudo useradd -s /sbin/nologin --system -g prometheus prometheus
sudo mv prometheus /usr/local/bin
sudo mv promtool /usr/local/bin
sudo chown prometheus:prometheus /usr/local/bin/prometheus
sudo chown prometheus:prometheus /usr/local/bin/promtool
sudo mkdir /etc/prometheus
sudo mkdir /var/lib/prometheus
sudo mv consoles /etc/prometheus
sudo mv console_libraries /etc/prometheus
sudo mv prometheus.yml /etc/prometheus
sudo chown prometheus:prometheus /etc/prometheus
sudo chown -R prometheus:prometheus /etc/prometheus/consoles
sudo chown -R prometheus:prometheus /etc/prometheus/console_libraries
sudo chown -R prometheus:prometheus /var/lib/prometheus
```
# 3. Configure Prometheus 
```
cat << EOF > /etc/prometheus/prometheus.yml
global:
	scrape_interval: 15s # интервал сбора и очистки данных с целей
	evaluation_interval: 15s #  интервал вычисления правил сбора

rule_files:
	# - "first.rules"
	# - "second.rules"

scrape_configs:
	- job_name: prometheus # имя задания, которое будет собирать данные
		static_configs: # целевые адреса целей в рамках указанного задания
			- targets: ['localhost:9090']
EOF 
```
# 4. Check configuration
```
promtool check config /etc/prometheus/prometheus.yml
```
# 5. Create service
```
cat << EOF > /etc/systemd/system/prometheus.service
[Unit]
Description=Background service of Prometheus
Wants=network-online.target
After=network-online.target

[Service]
User=prometheus
Group=prometheus
Type=simple
ExecStart=/usr/local/bin/prometheus 
	--config.file /etc/prometheus/prometheus.yml \
	--storage.tsdb.path /var/lib/prometheus/ \
	--web.console.templates=/etc/prometheus/consoles \
	--web.console.libraries=/etc/prometheus/console_libraries

[Install]
WantedBy=multi-user.target
EOF
```
# 6. Start Service
```
sudo systemctl daemon-reload
sudo systemctl enable prometheus
sudo systemctl start prometheus
sudo systemctl status prometheus
```

  
