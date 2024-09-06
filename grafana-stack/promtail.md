# 1. Download promtail deb package
```
wget https://github.com/grafana/loki/releases/download/v3.0.1/promtail_3.0.1_amd64.deb
```
# 2. Install deb package
```
dpkg -i promtail*.deb
```
# 3. Start/Enable service
```
systemctl enable promtail
systemctl start promtail
systemctl status promtail
```





