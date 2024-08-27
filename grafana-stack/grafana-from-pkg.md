# 1. Install dependencies
```
apt install adduser libfontconfig1 musl -y
```
# 2. install grafana
```
export PATH=$PATH:/sbin
dpkg -i grafana-enterprise_11.1.4_amd64.deb -y
```
# 3. Enable/Start grafana
```
systemctl daemon-reload
systemctl start grafana-server
systemctl enable grafana-server
systemctl status grafana-server
```