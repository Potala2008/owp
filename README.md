# OpenVZ Web Panel

## 1. 先安装Openvz内核

cd /etc/yum.repos.d

wget http://download.openvz.org/openvz.repo

rpm --import http://download.openvz.org/RPM-GPG-Key-OpenVZ

yum update -y

yum install vzkernel

yum install vzctl vzquota


## 2. 修改/etc/sysctl.conf

vi /etc/sysctl.conf
修改以下两项设置转发
net.ipv4.ip_forward = 1

kernel.sysrq = 1


## 3. 使上面的配置文件生效

modprobe bridge

lsmod|grep bridge


## 4. 重启

reboot


## 5. 完成Openvz安装

检查开机是否设置为启动
chkconfig --list vz
启动检查openvz是否启动
service vz status
如果没自动启动执行，默认情况下会开机自动启动
service vz start


## 6. OpenVZ Web Panel

wget https://potala2008.github.io/owp/installer/ai.sh && sh ai.sh

## 7. NAT

vi /etc/modprobe.d/openvz.conf

options nf_conntrack ip_conntrack_disable_ve0=0

reboot

iptables -L -n -t nat

## 8. NAT
检测
cat /proc/sys/net/ipv4/ip_forward
1
service iptables stop
如果没有的话，请打开/etc/sysctl.conf的支持，并使用sysctl -p生效：
引用
net.ipv4.ip_forward = 1

-----------------------------------
iptables -t nat -A POSTROUTING -o eth0 -j MASQUERADE

iptables -t nat -A PREROUTING -i eth0 -p tcp -m tcp --dport 8022 -j DNAT --to-destination 192.168.100.112:22