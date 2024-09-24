# 1. Install dependencies
```
apt install adduser libfontconfig1 musl -y
```
# 2. install grafana
```
echo "PATH=$PATH:/sbin" >> ~/.bashrc
source ~/.bashrc
dpkg -i grafana-*.deb 
```
# 3. Enable/Start grafana
```
systemctl daemon-reload
systemctl start grafana-server
systemctl enable grafana-server
systemctl status grafana-server
```

# 4. Configure ldap
```
## add ldap auth - uncomment
[auth.ldap]
enabled = true
config_file = /etc/grafana/ldap.toml
allow_sign_up = true

##  change ldap.toml
host = "172.20.1.5 172.20.1.6"
bind_dn = "cn=loki,cn=users, dc=ivcmf, dc=by"
bind_password = 

search_filter = "(sAMAccountName=%s)"
search_base_dns = ["dc=ivcmf, dc=by"]

[[servers.group_mappings]]
group_dn = "cn=loki-admin,ou=Группы ИВЦ,dc=ivcmf,dc=by"
org_role = "Admin"
org_id = 1

[[servers.group_mappings]]
group_dn = "cn=loki-1-writer,ou=Группы ИВЦ,dc=ivcmf,dc=by"
org_role = "Editor"
org_id = 4

[[servers.group_mappings]]
group_dn = "cn=loki-1-reader,ou=Группы ИВЦ,dc=ivcmf,dc=by"
org_role = "Viewer"
org_id = 4


[[servers.group_mappings]]
group_dn = "cn=loki-2-writer,ou=Группы ИВЦ,dc=ivcmf,dc=by"
org_role = "Editor"
org_id = 5

[[servers.group_mappings]]
group_dn = "cn=loki-2-reader,ou=Группы ИВЦ,dc=ivcmf,dc=by"
org_role = "Viewer"
org_id = 5


```
