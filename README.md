# Skill-for-Myself
## SS协议
一个 shadowsocks-rust 2022 一键安装脚本
bash <(curl -sL https://raw.githubusercontent.com/mban9584/Skill-for-Myself/refs/heads/main/ss2022install.sh)

自用的各种工具
## po0dd.sh 一键 DD（VPS 无法访问 GitHub 时的安装方法）
本说明仅介绍 “VPS 不能访问 GitHub” 时，如何安装并使用 po0dd.sh 脚本一键重装为 Debian 12 (bookworm)。

⚠️ 警告：po0dd.sh 会整盘重装系统盘（如 /dev/vda、/dev/sda 等），所有数据会被清空且无法恢复，请务必提前备份。

前置条件
你的 VPS：
支持 KVM / Xen 等完整虚拟化（可以重写 GRUB，允许重装系统）
可以访问腾讯源：http://mirrors.tencent.com/debian/...
你有至少一种方式登录 VPS：
SSH 终端 或 面板提供的 Web Shell / 控制台
你有一台 能访问 GitHub 的设备（本地电脑 / 另一台服务器）
方法一：本地下载脚本，再上传到 VPS（推荐）
步骤 1：在本地下载 po0dd.sh
在你 本地电脑（可以访问 GitHub 的环境）中，打开终端，执行：

curl -o po0dd.sh https://raw.githubusercontent.com/vpsbuy/po0/refs/heads/main/po0dd.sh

或者：

wget -O po0dd.sh https://raw.githubusercontent.com/vpsbuy/po0/refs/heads/main/po0dd.sh

下载完成后，可以检查一下：

ls po0dd.sh

如果在当前目录看到了 po0dd.sh，说明下载成功。

步骤 2：把 po0dd.sh 上传到 VPS
你可以使用任意一种常见方式上传脚本文件：

方式 A：使用 scp（Linux / macOS / WSL 等）
在本地终端执行：

scp po0dd.sh root@你的VPSIP:/root/

解释：

root：VPS 登录用户名（大部分商家默认 root）
你的VPSIP：你的 VPS 公网 IP
/root/：把脚本传到 VPS 的 /root 目录
上传成功后，登录 VPS，在 VPS 上执行：

ls /root/po0dd.sh

如果能看到文件，说明上传成功。

方式 B：使用 SFTP / 图形工具上传（WinSCP、FinalShell 等）
打开你常用的 SSH 工具（如：
WinSCP
FinalShell
Xshell + Xftp
宝塔面板的 SFTP 功能 等）。
使用你的 VPS IP、用户名 root、密码登录。
找到 VPS 的 /root 目录（或任意你习惯放脚本的位置）。
在本地文件列表中找到刚下载好的 po0dd.sh。
用拖拽 / 右键上传的方式，把 po0dd.sh 传到 VPS。
上传完成后，在 VPS 终端里确认：

ls /root/po0dd.sh

步骤 3：在 VPS 上赋予执行权限
登录到 VPS（SSH 或 Web 控制台），执行：

cd /root
chmod +x po0dd.sh

至此，脚本已经可以在 VPS 上执行。

方法二：浏览器打开脚本 → 终端手动复制粘贴
如果你 没有方便的上传工具 / scp，也可以通过复制粘贴的方式，把脚本内容手动创建到 VPS 上。

步骤 1：本地浏览器中打开脚本源码
在你的电脑浏览器中打开：

https://raw.githubusercontent.com/vpsbuy/po0/refs/heads/main/po0dd.sh
页面会显示一大段纯文本的脚本内容。

在浏览器中操作：

按下 Ctrl + A（全选所有内容）
按下 Ctrl + C（复制）
步骤 2：在 VPS 中新建脚本文件
登录 VPS（SSH 客户端 / 商家 Web 终端均可），执行：

nano po0dd.sh

如果系统没有 nano，也可以用 vi 或 vim 替代。以下以 nano 为例说明。

步骤 3：粘贴脚本内容到编辑器
在终端中打开的 nano 编辑器里，直接粘贴刚才复制的全部脚本内容。

不同终端的粘贴方式可能略有不同：

有的支持右键菜单 → “粘贴”
有的支持 Ctrl + Shift + V
有的支持终端菜单中的 “Paste” 按钮
确保脚本内容完整无缺地粘进去。

步骤 4：保存并退出编辑器
在 nano 中：

按 Ctrl + O，回车（保存）
按 Ctrl + X（退出编辑器）
如果使用的是 vi / vim：

按 Esc
输入 :wq 再回车（保存并退出）
步骤 5：赋予执行权限
在 VPS 中执行：

chmod +x po0dd.sh

至此，po0dd.sh 已经成功保存在 VPS 上，并且可执行。

三、执行 po0dd.sh 进行 DD 安装
⚠️ 下面操作会清空系统盘，请确认你已经做好备份，并理解风险。

脚本的基本运行方式：

./po0dd.sh [可选参数]

常见几种使用方式：
最简单：随机密码 + 默认 SSH 端口 22

./po0dd.sh
脚本会生成一个随机 root 密码，并在终端打印出来；
安装完成后，SSH 端口为 22。
指定 root 密码，端口默认 22

./po0dd.sh -passwd MyStrongPwd123
root 密码设为 MyStrongPwd123；
SSH 端口为 22。
随机密码，自定义 SSH 端口（如 60022）

./po0dd.sh -port 60022
root 密码随机生成；
安装完成后使用 60022 端口 SSH 登录。
同时指定密码和端口

./po0dd.sh -passwd MyPwd2025 -port 60022
脚本运行过程中，会显示当前检测到的系统盘、即将执行的操作，并要求你输入：

YES
来确认整盘重装。必须是大写 YES，否则脚本会自动退出。

四、重启并等待自动安装完成
脚本执行完，会问你是否立即重启：

是否立刻重启？(y/N):
输入 y 或 Y，回车即可立刻 reboot

也可以稍后自己执行：

reboot
重启之后，VPS 会自动从新写入的 GRUB 启动项进入 Debian 安装程序（无人值守）。
整个安装过程会自动完成，结束后系统会再次重启进入全新的 Debian 12 系统。

五、安装完成后的 SSH 登录方式
根据你运行脚本时的参数不同，登录方式略有差别：

如果没有指定 -port（默认 SSH 端口 22）：

ssh root@你的IP
如果指定了端口，例如：

./po0dd.sh -port 60022
那么登录时需要加上 -p 参数：

ssh -p 60022 root@你的IP
关于 root 密码：

如果使用了 -passwd MyStrongPwd 参数：
密码就是你传入的这个字符串；
如果没有指定 -passwd：
脚本运行过程中会在终端打印一个随机密码；

系统安装完成后，该密码也会被写入新系统中的文件：

cat /root/initial_root_password.txt
如 SSH 暂时不通，可以先通过商家提供的 VNC / 控制台登录进系统，再查看这个文件获取密码。

六、快速小结
VPS 不能访问 GitHub 时：

优先推荐：在本地下载 po0dd.sh → 用 SFTP/scp 上传到 VPS；
或者：浏览器打开 raw 页面 → 全选复制 → VPS 上 nano po0dd.sh 粘贴保存。
在 VPS 上给脚本加执行权限：

chmod +x po0dd.sh
执行脚本进行无人值守 DD 安装：

./po0dd.sh
./po0dd.sh -passwd 自定义密码
./po0dd.sh -port 自定义端口
./po0dd.sh -passwd 自定义密码 -port 自定义端口
安装完成后，用对应端口和密码 SSH 登录即可。




## 关于转发
PortChannel Zero (Po0 腾讯云) — 从开机到配置 nftables 专线转发全流程教程（小白向）
前言：关于 Po0
Po0（PortChannel Zero）是RFC陈总推出的广州、上海腾讯云CCN服务器。可利用 腾讯云 CCN（云联网） 与 RFCHOST T1 的内网互通，搭建 低延迟、高稳定性 的商业级专线，中转流量至 RFCHOST 落地机。
可能是目前MJJ能买得起的最强专线 xhj026

🛒 产品传送门（带AFF）：
• 腾讯云广州 - Spring Special
• 腾讯云华东（上海） - Spring Special

需通过腾讯云接口实名
配置：独立IP 2C 2G 200M 500G单向流量，1188元/年
另有众多月付套餐可供选择，，用券后最低仅79元/月起，月付优惠码：「常驻九折优惠券」
核心原理
入口：腾讯云 12 线 BGP（国内互联性极佳）
出口：RFCHOST HK.T1 / JP.T1
（可通过RFC 内网可延伸至 SG T1 / US T1（画饼中））
Nftables中转方式：通过 DNAT + SNAT：

入站：将访问 PO0 中转端口的流量内网转发（陈总神秘黑科技）至落地机
出站：强制使用腾讯云 ↔ RFCHOST 内网路由，绕开公网拥堵
预期表现
路径	延迟（参考）
广州 → RFC HK T1	~6ms
广州 → RFC JP T1	~50ms
上海 → RFC HK T1	~24ms
上海 → RFC JP T1	~30ms
SLA：腾讯云级别稳定性，理论0掉线率0丢包

准备工作
已购买RFC HK或JP T1落地机（目前HK CO和JP CO也通PO0）；已购买PO0上海或广州机器
已完成 落地机配置
已完成 落地ss协议搭建
为避免触犯论坛规则，这里不对落地机配置作具体展开
第一阶段：安全加固（SSH 密钥登录 & 修改端口）
默认系统：Debian 12
1. 本地生成 SSH 密钥
Windows (PowerShell) / Mac / Linux:
打开终端，输入以下命令（一路回车即可）：
ssh-keygen -t ed25519 -C "po0_key"

查看并复制公钥：

cat ~/.ssh/id_ed25519.pub

2. 服务器写入公钥
# 创建目录
mkdir -p ~/.ssh
chmod 700 ~/.ssh
# 写入公钥
echo "ssh-ed25519 AAAA...你的公钥内容... po0_key" >> ~/.ssh/authorized_keys
# 设置权限
chmod 600 ~/.ssh/authorized_keys

3. 修改 SSH 配置
# 编辑配置文件：
nano /etc/ssh/sshd_config

# 找到以下配置项并修改（如果找不到，可在文件末尾添加）：
# 1. 修改默认端口 (防止简单扫描)
Port 12956（自行修改）
# 2. 禁止密码登录 (强制使用密钥)
PasswordAuthentication no
# 3. 允许密钥登录
PubkeyAuthentication yes

重启 SSH 服务

systemctl restart ssh

第二阶段：Debian 12 初始化
1. 更新系统
apt update && apt upgrade -y

2. 开启 IP 转发（必做）
echo "net.ipv4.ip_forward=1" >> /etc/sysctl.conf
sysctl -p

3. 安装 Nftables（debian12系统内已默认安装，这步可省略）
apt install nftables -y

第三阶段：部署 Nftables 专线转发
编辑配置文件（清空原内容）：

nano /etc/nftables.conf

如果仅有一台落地机需要转发：

#!/usr/sbin/nft -f
# =======================================================
# 📝 变量定义区 (务必根据实际情况修改)
# =======================================================
define DEST_IP       = 82.40.xx.xx # 1. RFCHOST 落地机公网 IP
define DEST_PORT_OUT = 33333       # 2. RFCHOST 落地机服务端口
define RELAY_PORT_IN = 10086       # 3. PO0中转端口
define RELAY_LAN_IP  = 10.100.xx.xx  # 4. PO0内网 IP
# =======================================================
# 清空旧规则
flush ruleset

# --- 核心转发逻辑 (NAT表) ---
table ip nat {
    # 1. 入站转发 (DNAT)
    # 目的: 将访问PO0 10086端口 的流量仍给落地机
    chain prerouting {
        type nat hook prerouting priority dstnat; policy accept;
        
        # 同时转发 TCP 和 UDP 
        meta l4proto { tcp, udp } th dport $RELAY_PORT_IN dnat to $DEST_IP:$DEST_PORT_OUT
    }

    # 2. 出站伪装 (SNAT) - 专线核心
    # 目的: 将源地址改为内网 IP，强制流量走腾讯云-RFCHOST的内网路由
    chain postrouting {
        type nat hook postrouting priority srcnat; policy accept;
        
        # 仅匹配发往落地机的流量，执行 SNAT
        ip daddr $DEST_IP meta l4proto { tcp, udp } th dport $DEST_PORT_OUT snat to $RELAY_LAN_IP
    }
}

# --- 性能优化逻辑 (Filter表) ---
# 目的: 防止 MTU 不匹配导致的断流
table ip filter {
    chain forward {
        type filter hook forward priority 0; policy accept;
        
        # 调整 TCP MSS，解决某些情况下速度慢/断流”的问题
        ip daddr $DEST_IP tcp flags syn tcp option maxseg size set 1452
    }
}
如果有两台落地机需要转发：

#!/usr/sbin/nft -f
# =======================================================
# 📝 变量定义区 (务必根据实际情况修改)
# =======================================================
# --- 公共配置 ---
define RELAY_LAN_IP  = 10.100.xx.xx  # PO0内网 IP

# --- 线路 1 配置 (例如RFC HK T1) ---
define PORT_IN_1     = 10086       # 中转端口 1
define DEST_IP_1     = 172.81.xx.xx  # 1号落地机 IP
define DEST_PORT_1   = 33333       # 1号落地机端口

# --- 线路 2 配置 (例如RFC JP CO) ---
define PORT_IN_2     = 20086       # 中转端口 2
define DEST_IP_2     = 82.40.xx.xx   # 2号落地机 IP 
define DEST_PORT_2   = 44444         # 2号落地机端口
# =======================================================
# 清空旧规则
flush ruleset

# --- 核心转发逻辑 (NAT表) ---
table ip nat {
    # --- 入站转发 (DNAT) ---
    chain prerouting {
        type nat hook prerouting priority dstnat; policy accept;
        
        # [线路 1] 流量进入端口1 -> 转给落地机1
        meta l4proto { tcp, udp } th dport $PORT_IN_1 dnat to $DEST_IP_1:$DEST_PORT_1
        
        # [线路 2] 流量进入端口2 -> 转给落地机2
        meta l4proto { tcp, udp } th dport $PORT_IN_2 dnat to $DEST_IP_2:$DEST_PORT_2
    }

    # --- 出站伪装 (SNAT) ---
    chain postrouting {
        type nat hook postrouting priority srcnat; policy accept;
        
        # [线路 1] 发往落地机1的流量 -> 改源IP为内网IP (走专线)
        ip daddr $DEST_IP_1 meta l4proto { tcp, udp } th dport $DEST_PORT_1 snat to $RELAY_LAN_IP
        
        # [线路 2] 发往落地机2的流量 -> 改源IP为内网IP (走专线)
        ip daddr $DEST_IP_2 meta l4proto { tcp, udp } th dport $DEST_PORT_2 snat to $RELAY_LAN_IP
    }
}

# --- 性能优化逻辑 (Filter表) ---
# 目的: 防止 MTU 不匹配导致的断流
table ip filter {
    chain forward {
        type filter hook forward priority 0; policy accept;
        
        # 调整 TCP MSS，解决某些情况下速度慢/断流”的问题
        ip daddr { $DEST_IP_1, $DEST_IP_2 } tcp flags syn tcp option maxseg size set 1452
    }
}
如果有三台及以上落地机需要转发，则自行再增加n组定义变量，复制 dnat 和 snat 的那两行规则，修改变量即可，这里不再展开阐述。 xhj009

应用并自启：

# 检查语法 (无输出则为正常)
nft -c -f /etc/nftables.conf

# 应用配置
nft -f /etc/nftables.conf

# 设置开机自启
systemctl enable nftables
systemctl restart nftables
第四阶段：验证
ss客户端配置：

Address：PO0 公网 IP
Port：PO0 中转端口
其余参数：保持落地机一致
到此教程结束
