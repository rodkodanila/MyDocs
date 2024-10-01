# 1. Update System and Install Required Packages
```bash
sudo apt update -y
sudo apt upgrade -y
sudo apt install wget -y
```
# 2. Install Java
```bash
sudo apt install openjdk-17-jre-headless -y
```
# 3. Create a Nexus User
```bash
sudo adduser --disabled-login --no-create-home --gecos "" nexus
```
# 4. Download and Extract Nexus
```bash
cd /opt
sudo wget https://download.sonatype.com/nexus/3/latest-unix.tar.gz
sudo tar -zxvf latest-unix.tar.gz
```
# 5. Set Up Directory and Permissions
```bash
sudo mv /opt/nexus-*/ /opt/nexus
sudo chown -R nexus:nexus /opt/nexus
sudo chown -R nexus:nexus /opt/sonatype-work
```
# 6. Configure Nexus to Run as the Nexus User
```bash
sudo vim /opt/nexus/bin/nexus.rc
run_as_user="nexus"
```
# 7.  Configure JVM Options
```bash
cat << EOF >> /opt/nexus/bin/nexus.vmoptions
-Xms1024m
-Xmx1024m
-XX:MaxDirectMemorySize=1024m
-XX:LogFile=./sonatype-work/nexus3/log/jvm.log
-XX:-OmitStackTraceInFastThrow
-Djava.net.preferIPv4Stack=true
-Dkaraf.home=.
-Dkaraf.base=.
-Dkaraf.etc=etc/karaf
-Djava.util.logging.config.file=/etc/karaf/java.util.logging.properties
-Dkaraf.data=./sonatype-work/nexus3
-Dkaraf.log=./sonatype-work/nexus3/log
-Djava.io.tmpdir=./sonatype-work/nexus3/tmp
EOF
```
# 8. Create a Systemd Service File
```bash
cat << EOF > /etc/systemd/system/nexus.service
[Unit]
Description=Nexus Service
After=network.target

[Service]
Type=forking
LimitNOFILE=65536
ExecStart=/opt/nexus/bin/nexus start
ExecStop=/opt/nexus/bin/nexus stop
User=nexus
Restart=on-abort

[Install]
WantedBy=multi-user.target
EOF
```
# 9. Start and Enable Nexus Service
```bash
sudo systemctl daemon-reload
sudo systemctl start nexus
sudo systemctl enable nexus
```

# 10. Check nexus port  8081
```bash
netstat -tulpn | grep 8081
```