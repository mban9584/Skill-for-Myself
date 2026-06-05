Debian 使用

创建文件夹
mkdir -p /usr/local/opengfw

进入文件夹
cd /usr/local/opengfw

创建一个名字为config.yaml的文件并打开
vim config.yaml
粘贴需要输入的内容退出
io:
  queueSize: 1024
  rcvBuf: 4194304
  sndBuf: 4194304
  local: false # 如果需要在 FORWARD 链上运行 OpenGFW，请设置为 false

workers:
  count: 4
  queueSize: 16
  tcpMaxBufferedPagesTotal: 4096
  tcpMaxBufferedPagesPerConn: 64
  udpMaxStreams: 4096

# 指定的 geoip/geosite 档案路径
# 如果未设置，将自动从 https://github.com/Loyalsoldier/v2ray-rules-dat 下载
# geo:
#   geoip: geoip.dat
#   geosite: geosite.dat


添加屏蔽规则文件 rules.yaml（示例只屏蔽了https,http,tls,socks5，其他协议请自行ai进行补充）
- name: SNI mismatch
  action: block
  expr: tls?.req?.sni != nil && ip.dst not in concat(lookup(tls.req.sni), lookup(tls.req.sni, "1.1.1.1:53"), lookup(tls.req.sni, "8.8.8.8:53"))
- name: block wireguard by handshake response
  action: drop
  expr: wireguard?.handshake_response?.receiver_index_matched == true
- name: Block-Other-HTTP
  action: block
  expr: http != nil
- name: Block-Other-TLS
  action: block
  expr: tls != nil
- name: Block-SOCKS5
  action: block
  expr: socks != nil



## 文件修改名字
第一次上传了名字为“OpenGFW-linux-amd64”的文件
但是执行程序用的名字是“OpenGFW”
所以需要修改文件名字
ls -l /usr/local/opengfw/
如果看到了 OpenGFW-linux-amd64，将其重命名为 OpenGFW：

mv /usr/local/opengfw/OpenGFW-linux-amd64 /usr/local/opengfw/OpenGFW

赋予该文件可执行权限（这步必不可少！）：

chmod +x /usr/local/opengfw/OpenGFW



上传指定目录（示例目录为/usr/local/opengfw）
配置开启机启动
cd /etc/systemd/system/
vim opengfw.service
粘贴下边配置启动文件
[Unit]
Description=OpenGFW - Open-source GFW implementation
After=network.target

[Service]
Type=simple
User=root
WorkingDirectory=/usr/local/opengfw
ExecStart=/usr/local/opengfw/OpenGFW -c /usr/local/opengfw/config.yaml /usr/local/opengfw/rules.yaml
Restart=always
RestartSec=10
StandardOutput=journal
StandardError=journal

# 资源限制
LimitNOFILE=65535

[Install]
WantedBy=multi-user.target


设置成开机自启
systemctl daemon-reload
systemctl enable opengfw.service
systemctl restart opengfw.service

## 阻断国外IP连接
这块可根据自身业务配置，如果有需要可继续阅读，如果不需要直接跳过。
1、本地下载IP库
http://www.ipdeny.com/ipblocks/data/countries/cn.zone

2、上传IP库文件
/root/cn.zone

3、创建ipset规则
ipset create cnip hash:net maxelem 1000000

4、导入IP库数据
for i in $(cat /root/cn.zone); do
    ipset add cnip $i
done
5、添加iptables规则
# 清空规则（此操作会把已有规则清空，根据自己业务需求来）
iptables -F INPUT
# 1. 允许已建立连接
iptables -A INPUT -m conntrack --ctstate ESTABLISHED,RELATED -j ACCEPT
# 2. 允许本地回环
iptables -A INPUT -i lo -j ACCEPT
# 3. 允许管理IP
iptables -A INPUT -s PO0机器IP -j ACCEPT
# 4. 允许中国IP
iptables -A INPUT -m set --match-set cnip src -j ACCEPT
# 5. 允许SSH
iptables -A INPUT -p tcp --dport 22 -j ACCEPT
# 6. 默认拒绝
iptables -P INPUT DROP
6、持久化与恢复配置
ipset save > /etc/ipset.conf
ipset restore < /etc/ipset.conf
