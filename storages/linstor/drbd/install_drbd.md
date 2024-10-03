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

