# One Command Installation
Satu kali jalan kan command untuk install semua basic **Package** webserver

```bash
sudo apt update && sudo apt -y full-upgrade && sudo apt install -y \
apt-transport-https \
ca-certificates \
software-properties-common \
gnupg \
lsb-release \
curl \
wget \
git \
gh \
zip \
unzip \
tree \
jq \
btop \
net-tools \
dnsutils \
traceroute \
nmap \
build-essential \
gcc \
g++ \
make \
openssl \
nginx \
qemu-guest-agent && \
sudo systemctl enable nginx qemu-guest-agent && \
sudo systemctl start nginx qemu-guest-agent
```