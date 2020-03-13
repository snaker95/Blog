YTIOvKxR2Hbf

## 开启防火墙, 设置sshd

```shell
# sshd 配置
# vi /etc/ssh/sshd_config
RSAAuthentication yes #RSA认证
PubkeyAuthentication yes #开启公钥验证
AuthorizedKeysFile .ssh/authorized_keys #验证文件路径
PasswordAuthentication no #禁止密码认证
PermitEmptyPasswords no #禁止空密码
# 最后保存，重启
# systemctl restart sshd.service

# 防火墙
systemctl status iptables
systemctl start iptables
systemctl enable iptables
iptables -A OUTPUT -p tcp -m multiport --dports 25,26,465 -j REJECT --reject-with tcp-reset
iptables -A OUTPUT -p udp -m multiport --dports 25,26,465 -j DROP
# POP Ports
iptables -A OUTPUT -p tcp -m multiport --dports 109,110,995 -j REJECT --reject-with tcp-reset
iptables -A OUTPUT -p udp -m multiport --dports 109,110,995 -j DROP
# IMAP Ports
iptables -A OUTPUT -p tcp -m multiport --dports 143,218,220,993 -j REJECT --reject-with tcp-reset
iptables -A OUTPUT -p udp -m multiport --dports 143,218,220,993 -j DROP
# Other Mail Services
iptables -A OUTPUT -p tcp -m multiport --dports 24,50,57,158,209,587,1109 -j REJECT --reject-with tcp-reset
iptables -A OUTPUT -p udp -m multiport --dports 24,50,57,158,209,587,1109 -j DROP
iptables -A INPUT -p tcp -m multiport --dports 80,443,1080,8001,8188,8388,8888 -j ACCEPT
iptables -A INPUT -p udp -m multiport --dports 80,443,1080,8001,8188,8388,8888 -j ACCEPT
service iptables save
iptables -nL
```



## 建立docker

```shell
yum install docker -y

sudo systemctl start docker
sudo systemctl enable docker

docker pull nginx # 拉取nginx

mkdir -p /home/work/nginx/conf
mkdir -p /Webserver/sharedsea.com
docker run --name nginx-docker -p 80:80 -p 8001:8001 -p 443:443 -v /home/work/nginx/conf/nginx.conf:/etc/nginx/nginx.conf:ro -v /home/work/nginx/conf/conf.d:/etc/nginx/conf.d:ro -v /Webserver:/Webserver:ro --restart=on-failure:10 -d nginx # /etc/nginx/nginx.conf /etc/nginx/conf.d

docker pull ilanyu/golang-reverseproxy

docker run --name idea-license-server -p 8888:8888 --restart=on-failure:10 -d ilanyu/golang-reverseproxy

docker ps
docker logs 5f2aefabf
docker exec -it 775c7c9ee1e1 /bin/bash 

```



## 安装shadowsocks

```shell
easy_install shadowsocks
mkdir /etc/shadowsocks
vi /etc/shadowsocks/shadowsocks.json
/usr/bin/ssserver -c /etc/shadowsocks/shadowsocks.json -d restart
echo '/usr/bin/ssserver -c /etc/shadowsocks/shadowsocks.json -d restart' >> /etc/rc.d/rc.local
```



### 安装ss5

```shell
cd ~
curl -O http://jaist.dl.sourceforge.net/project/ss5/ss5/3.8.9-8/ss5-3.8.9-8.tar.gz
tar -zxvf ss5-3.8.9-8.tar.gz
cd ss5-3.8.9
yum -y install gcc automake make pam-devel openldap-devel cyrus-sasl-devel openssl-devel
./configure
make && make install
cd /etc/opt/ss5/
cp ss5.passwd ss5.passwd.org
cp ss5.conf ss5.conf.org
mkdir /var/run/ss5/
echo 'auth     0.0.0.0/0               -              u' > /etc/opt/ss5/ss5.conf
echo 'permit  u        0.0.0.0/0       -       0.0.0.0/0       -       -       -       -       -' > /etc/opt/ss5/ss5.conf
echo 'snaker95 snaker95.' > /etc/opt/ss5/ss5.passwd
sed -i.bak 's/-t/-m -t/g' /etc/init.d/ss5 && systemctl daemon-reload
echo 'mkdir /var/run/ss5/' >> /etc/rc.d/rc.local 
service ss5 start && service ss5 status
tail -f /var/log/ss5/ss5.log
```





```shel
docker ps -a

docker start/stop UUID

docker rm UUID

docker exec -it UUID /bin/bash		
```




