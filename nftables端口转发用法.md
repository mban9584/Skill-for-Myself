最近看到不少新手买了 po0 之后卡在端口转发配置问题，配半天也搞不定。
于是我直接用 claude 搓了一个交互式管理 nftables 端口转发的一键脚本，欢迎大家试用。
（来源：https://www.nodeseek.com/post-616160-1）
使用教程

## 1. 出口机搭建代理协议
出口机可以使用 RFC 在香港和日本的任意机器。考虑现在 rfc 落地机溢价高 的情况，直接使用 rfc jpco / jpco lite 也是不错的选择。

搭建代理协议这块我就不多说了，搭一个 ss2022 即可（方案很多，用自己习惯的就行）。没弄过的可以用下面的脚本：
curl -L https://github.com/mban9584/Skill-for-Myself/raw/refs/heads/main/nftables.sh -o nft.sh && chmod +x nft.sh && ./nft.sh
## 2. 境内腾讯云 po0 配置端口转发
通过以下指令直接下载并运行脚本：
curl -L http://script.gu.ax:2052/nftables.sh -o nft.sh && chmod +x nft.sh && ./nft.sh

(https://cdn.nodeimage.com/i/QPArLdaTSFIzbRhttJY3gTCnPj3bDvGP.webp)

第一次使用：
先选 选项1：安装 nftables，并自动开启 bbr 和 ipv4 端口转发。

如果之前已经配置过 nftables：
也需要选一次 选项1。脚本会把你之前的配置文件 备份并删除，之后由脚本维护一套新的配置，避免规则混在一起出现冲突。

配置端口转发：
准备工作完成后，即可通过 选项3 和 选项4 增减端口转发配置：
[转发图](https://cdn.nodeimage.com/i/PNEMbeiOozAH1srZxd4XFcPPb7okXlbu.webp)
按提示输入：

在 po0 上监听的端口
出口机 IP
出口机 ss 端口（不输入默认等于 po0 监听端口）
完成后，把 ss 链接里的 出口机 IP 和端口 替换为 po0 的公网 IP 和监听端口，导入客户端测试能否连通。一般到这一步就能用了。

如果出现问题，可以用脚本里的 诊断选项 跑一下找原因（不一定完全覆盖，我自测没出过问题，遇到疑难杂症请询问AI）。


————————————————
多层转发配置教程
————————————————
今天还有几个老哥遇到的问题是：搞不懂怎么 多中转一次，用自备落地机作为出口。下面简单写一下步骤。

链路结构：客户端 → po0 → rfc 中转机 → 落地机（出口）

1. 落地机搭建代理协议
参考上面的方式，这次把代理协议搭在 自备落地机 上。
同样记录：ss 链接、本机 IP、ss 端口。

2. rfc 中转机配置 nftables
po0 只能与 rfc 港日机通过腾讯云 CCN 内网专线互联，所以这里我们用 与出口机同地区 的港/日 rfc 机器作为中转。

同样下载脚本并用 选项1 安装并初始化 nftables 配置。
curl -L https://github.com/mban9584/Skill-for-Myself/raw/refs/heads/main/nftables.sh -o nft.sh && chmod +x nft.sh && ./nft.sh

然后按之前方式配置端口转发：目标指向 落地机 IP + ss 端口。

配置写入后，可以先把 ss 链接中的 IP 和端口替换为 rfc 中转机的 IP 和端口 做连通性测试。
如果不是中国线路优化机，这一步延迟会比较高，属于正常现象。


3. po0 配置 nftables
然后进入 po0，重复一遍之前步骤：下载脚本、安装、配置端口转发。

这里填入的是 rfc 中转机的 IP 和端口。为了方便记忆，建议 po0 使用和中转机 相同的端口。

不出意外的话，此时将 ss 链接中的 IP 改成 po0 的公网 IP 即可连通，延迟也会因此大幅下降。
接下来就可以开始体验po0了，顶尖~

后续维护
后续需要增减端口转发配置，都可以直接用脚本菜单操作，不再需要手动改 nftables 配置，避免出现冲突。
