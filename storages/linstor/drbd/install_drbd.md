# Install DRBD on Ubuntu 22.04 LTS
# 1. Add repo
```bash
sudo add-apt-repository ppa:linbit/linbit-drbd9-stack
```
# 2. Verify that we have drbd 9
```bash
sudo apt list "*drbd*"
```
# 3. Install drbd-dkms and drbd-utils
```bash
sudo apt install drbd-dkms drbd-utils -y
```

# Install DRBD Debian 12
# 1. Compile and attach drbd kernel module
```bash
wget https://linbit.gateway.scarf.sh//downloads/drbd/9/drbd-9.2.12.tar.gz
tar xvf drbd*.tar.gz
cd  drbd-9.2.12/
apt install -y git build-essential debhelper dkms docbook-xsl help2man python3-all python3-dbus  xsltproc flex autoconf automake
 
make clean
make 
make install 
depmod -a
modprobe drbd
cat /proc/drbd
```
# 2. Install drbd-utils
```bash
apt install drbd-utils -y
drbdadm --version
```

