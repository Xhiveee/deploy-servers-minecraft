apt-get update && apt-get upgrade -y && apt autoremove -y
timedatectl set-timezone Europe/Moscow
hostnamectl set-hostname name
### UFW (фаервол)
ufw default deny incoming
ufw default allow outgoing 
ufw allow 6767/tcp comment "ssh"
ufw allow 80/tcp comment "http"
ufw allow 443/tcp comment "https"
ufw allow 23333/tcp comment "mcs-web"
nano /etc/ssh/sshd_config
#### Убрать IPV 6
nano /etc/default/ufw
ufw enable
### Crowdsec (аналог Fail2Ban)
curl -s https://install.crowdsec.net | sh
apt update
apt install crowdsec -y
cscli collections install crowdsecurity/sshd
apt install crowdsec-firewall-bouncer-iptables -y
cscli collections list
cscli bouncers list
cscli metrics
cscli alerts list
cscli decisions list

### Автообновления
apt install unattended-upgrades -y
dpkg-reconfigure --priority=low unattended-upgrades
### Базовые пакеты
apt install -y btop nano curl wget unzip ufw

# Librespeed (аналог SpeedTest)

wget https://github.com/librespeed/speedtest-cli/releases/download/v1.0.12/librespeed-cli_1.0.12_linux_amd64.tar.gz
tar -xzf librespeed-cli_1.0.12_linux_amd64.tar.gz
mv librespeed-cli /usr/local/bin/
chmod +x /usr/local/bin/librespeed-cli
librespeed-cli
curl -fsSL https://get.docker.com -o get-docker.sh
sh get-docker.sh


# MCSmanger (через Docker + папка серверы)

curl -fsSL https://get.docker.com -o get-docker.sh
sudo sh get-docker.sh

mkdir -p /opt/mcsmanager
cd /opt/mcsmanager
nano docker-compose.yml
```
services:
  web:
    image: githubyumao/mcsmanager-web:latest
    container_name: mcsm-web
    restart: unless-stopped
    ports:
      - "23333:23333"
    volumes:
      - /etc/localtime:/etc/localtime:ro
      - ./web/data:/opt/mcsmanager/web/data
      - ./web/logs:/opt/mcsmanager/web/logs

  daemon:
    image: githubyumao/mcsmanager-daemon:latest
    container_name: mcsm-daemon
    restart: unless-stopped
    ports:
      - "24444:24444"
    environment:
      - MCSM_DOCKER_WORKSPACE_PATH=/opt/servers
    volumes:
      - /etc/localtime:/etc/localtime:ro
      - ./daemon/data:/opt/mcsmanager/daemon/data
      - ./daemon/logs:/opt/mcsmanager/daemon/logs
      - /opt/servers:/opt/servers
      - /var/run/docker.sock:/var/run/docker.sock
```
mkdir -p /opt/servers
docker compose up -d
docker logs mcsm-web
docker logs mcsm-daemon
docker exec -it mcsm-daemon sh
cat /opt/mcsmanager/daemon/data/Config/global.json
docker ps
docker restart mcsm-web
docker restart mcsm-daemon
docker start mcsm-web
docker start mcsm-daemon
docker stop mcsm-web
docker stop mcsm-daemon

# Передача файлов
Сжать
tar -czf /opt/servers_backup.tar.gz -C /opt servers/
tar -xzf /opt/servers_backup.tar.gz -C /
Быстро
tar -cf /opt/servers_backup.tar -C /opt servers/
tar -xf /opt/servers_backup.tar -C /

apt install -y git g++ make zlib1g-dev libssl-dev
git clone https://github.com/eeertekin/bbcp.git
cd bbcp/src
make
cp ../bin/amd64_linux/bbcp /usr/local/bin/
bbcp --version

bbcp -f -r -s 1 -Z 5031:5031 -w 10m -P 5 -v /opt/servers/ root@айпи:/opt/servers/
bbcp -f -r -s 1 -Z 5031:5031 -w 10m -P 5 -v /opt/servers/test root@1айпи78.63.251.143:/opt/servers/

```
bbcp -f -s 16 -w 10m -P 5 -v /opt/mcsmanager/daemon/test/test.zip root@айпи:/opt/servers/test
```
