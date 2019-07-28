# iptables 防火墙策略

## 1 清除已有iptables规则

```json
iptables -F
iptables -X
iptables -Z
```

## 2开放指定的端口
-A和-I参数分别为添加到规则末尾和规则最前面。
```json
#允许本地回环接口(即运行本机访问本机)
iptables -A INPUT -i lo -j ACCEPT
# 允许已建立的或相关连的通行
iptables -A INPUT -m state --state ESTABLISHED,RELATED -j ACCEPT
#允许所有本机向外的访问
iptables -A OUTPUT -j ACCEPT
# 允许访问22端口
iptables -A INPUT -p tcp --dport 22 -j ACCEPT
#允许访问80端口
iptables -A INPUT -p tcp --dport 80 -j ACCEPT
#允许访问443端口
iptables -A INPUT -p tcp --dport 443 -j ACCEPT
#允许FTP服务的21和20端口
iptables -A INPUT -p tcp --dport 21 -j ACCEPT
iptables -A INPUT -p tcp --dport 20 -j ACCEPT
#如果有其他端口的话，规则也类似，稍微修改上述语句就行
#允许ping
iptables -A INPUT -p icmp -m icmp --icmp-type 8 -j ACCEPT
#禁止其他未允许的规则访问
iptables -A INPUT -j REJECT  #（注意：如果22端口未加入允许规则，SSH链接会直接断开。）
iptables -A FORWARD -j REJECT

```

## 3屏蔽IP

```json
#如果只是想屏蔽IP的话“3、开放指定的端口”部分设置可以直接跳过。
#屏蔽单个IP的命令是
iptables -I INPUT -s 123.45.6.7 -j DROP
#封整个段即从123.0.0.1到123.255.255.254的命令
iptables -I INPUT -s 123.0.0.0/8 -j DROP
#封IP段即从123.45.0.1到123.45.255.254的命令
iptables -I INPUT -s 124.45.0.0/16 -j DROP
#封IP段即从123.45.6.1到123.45.6.254的命令是
iptables -I INPUT -s 123.45.6.0/24 -j DROP
```

## 4屏蔽或允许某IP访问指定端口
```json
#屏蔽某IP访问指定端口，以22端口为例命令是
iptables -I INPUT -s 123.45.6.7 -p tcp --dport 22 -j DROP
#允许某IP访问指定端口，以22端口为例命令是
iptables -I INPUT -p tcp --dport 22 -j DROP
iptables -I INPUT -s 123.45.6.7 -p tcp --dport 22 -j ACCEPT
```
## 5 删除查看已添加的iptables规则

```
1、iptables -L -n 
                v：显示详细信息，包括每条规则的匹配包数量和匹配字节数
                x：在 v 的基础上，禁止自动单位换算（K、M） vps侦探
                n：只显示IP地址和端口号，不将ip解析为域名
2、iptables -L -n --line-numbers 将所有iptables以序号标记显示

3、iptables -D INPUT 8 比如要删除INPUT里序号为8的规则(要删除OUTPUT的话就改成OUTPUT，以此类推)，执行：
```
## 6 保存开机启动
```
CentOS上可能会存在安装好iptables后，iptables并不开机自启动，可以执行一下： service iptables save 
systemctl enable iptables  ka 机子启动
```
## 最后 形成的iptables 策略规则如下
```json
Chain INPUT (policy DROP)
target     prot opt source               destination         
ACCEPT     all  --  0.0.0.0/0            0.0.0.0/0           
ACCEPT     tcp  --  0.0.0.0/0            0.0.0.0/0            tcp dpt:22
ACCEPT     tcp  --  0.0.0.0/0            0.0.0.0/0            tcp dpt:80
ACCEPT     tcp  --  0.0.0.0/0            0.0.0.0/0            tcp dpt:443
ACCEPT     icmp --  0.0.0.0/0            0.0.0.0/0            icmptype 8
ACCEPT     all  --  0.0.0.0/0            0.0.0.0/0            state RELATED,ESTABLISHED
ACCEPT     tcp  --  0.0.0.0/0            0.0.0.0/0            tcp dpt:3306
ACCEPT     tcp  --  0.0.0.0/0            0.0.0.0/0            state NEW tcp dpt:3306
REJECT     all  --  0.0.0.0/0            0.0.0.0/0            reject-with icmp-port-unreachable

Chain FORWARD (policy DROP)
target     prot opt source               destination         
REJECT     all  --  0.0.0.0/0            0.0.0.0/0            reject-with icmp-port-unreachable

Chain OUTPUT (policy ACCEPT)
target     prot opt source               destination         
ACCEPT     all  --  0.0.0.0/0            0.0.0.0/0           
ACCEPT     all  --  0.0.0.0/0            0.0.0.0/0            state RELATED,ESTABLISHED

```

