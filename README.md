# v2ray
# v2ray


# 客户端
mac os client：
* https://install.appcenter.ms/users/clashx/apps/clashx-pro/distribution_groups/public
* https://github.com/yichengchen/clashX/releases

windows client：
* https://github.com/Dreamacro/clash/releases


基于v2ray部署科学上网服务；可以使用国外主机或购买腾讯云轻量云主机；测试后香港延迟50ms，硅谷延迟180ms，新加波、日本、首尔基本在200ms以上。[https://www.v2ray.com/](https://www.v2ray.com/)
# 基础信息
| 资源 | 选型 | 其他 |
| --- | --- | --- |
| 云主机 | 腾讯云轻量云主机：32元/月满足使用 | 云主机防火墙开通所有端口；或限制只某些IP访问 |
| 域名 |  万网 或 DnsPod |  | |
| 证书 | 使用acme.sh调用阿里云、dnspod密钥签署证书 Let'sEncrypt |  |

# 安装部署
## 证书
证书用于Nginx Web伪装站点使用，使用脚本部署时必须提前提供
```c
acme.sh --issue --dns dns_ali -d *.fetchow.cn -d fetchow.cn

# 提前准备aliyun key信息
export Ali_Key=""
export Ali_Secret=""
--dns dns_ali

```
默认证书信息位置`/root/.acme.sh/(domain name)`；

- PRIVATE KEY：*.fetchow.cn.key；修改名称v2ray.key
- CERTIFICATE（建议使用证书链）：fullchain.cer；修改名称v2ray.pem
```c
[root@10-10-110-154 ~]# tree .acme.sh/\*.fetchow.cn/
.acme.sh/*.fetchow.cn/
├── ca.cer
├── *.fetchow.cn.cer
├── *.fetchow.cn.conf
├── *.fetchow.cn.csr
├── *.fetchow.cn.csr.conf
├── *.fetchow.cn.key
└── fullchain.cer
```
## 部署
安装基础软件；检查域名解析正确；域名用于部署伪装站点
```c
root@VM-4-8-debian:~# apt-get install curl wget git
root@VM-4-8-debian:~# dig silicon01.fetchow.cn

; <<>> DiG 9.11.5-P4-5.1+deb10u8-Debian <<>> silicon01.fetchow.cn
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 19144
;; flags: qr rd ra; QUERY: 1, ANSWER: 1, AUTHORITY: 0, ADDITIONAL: 0

;; QUESTION SECTION:
;silicon01.fetchow.cn.          IN      A

;; ANSWER SECTION:
silicon01.fetchow.cn.   600     IN      A       43.153.86.239

;; Query time: 149 msec
;; SERVER: 183.60.83.19#53(183.60.83.19)
;; WHEN: Tue Mar 07 22:04:06 CST 2023
;; MSG SIZE  rcvd: 54

root@VM-4-8-debian:~# git clone https://github.com/airsmon/v2ray.git
```
检查证书
```c
root@VM-4-8-debian:~# tree /root/
/root/
├── v2ray.key
├── v2ray.pem
└── v2ray.sh
```
部署v2ray服务；当前使用第9种方式：trojan

1. 输入域名
```c
chmod +x v2ray.sh
./v2ray.sh

#############################################################
#                   v2ray一键安装脚本                      #
# 作者: 网络跳越(hijk)                                      #
# 网址: https://hijk.art                                    #
# 论坛: https://hijk.club                                   #
# TG群: https://t.me/hijkclub                               #
# Youtube频道: https://youtube.com/channel/UCYTB--VsObzepVJtc9yvUxQ #
#############################################################
  1.   安装V2ray-VMESS
  2.   安装V2ray-VMESS+mKCP
  3.   安装V2ray-VMESS+TCP+TLS
  4.   安装V2ray-VMESS+WS+TLS(推荐)
  5.   安装V2ray-VLESS+mKCP
  6.   安装V2ray-VLESS+TCP+TLS
  7.   安装V2ray-VLESS+WS+TLS(可过cdn)
  8.   安装V2ray-VLESS+TCP+XTLS(推荐)
  9.   安装trojan(推荐)
  10.  安装trojan+XTLS(推荐)
 -------------
  11.  更新V2ray
  12.  卸载V2ray
 -------------
  13.  启动V2ray
  14.  重启V2ray
  15.  停止V2ray
 -------------
  16.  查看V2ray配置
  17.  查看V2ray日志
 -------------
  0.   退出
 当前状态：未安装

 请选择操作[0-17]：9

 V2ray一键脚本，运行之前请确认如下条件已经具备：
  1. 一个伪装域名
  2. 伪装域名DNS解析指向当前服务器ip（）
  3. 如果/root目录下有 v2ray.pem 和 v2ray.key 证书密钥文件，无需理会条件2

 确认满足按y，按其他退出脚本：y
 请输入伪装域名：silicon01.fetchow.cn
 伪装域名(host)：silicon01.fetchow.cn
 检测到自有证书，将使用其部署

 请输入v2ray监听端口[强烈建议443，默认443]：3306
 v2ray端口：3306

 请设置trojan密码（不输则随机生成）:
 trojan密码：9iNRZH3gCkUYtun7

 请选择伪装站类型:
   1) 静态网站(位于/usr/share/nginx/html)
   2) 小说站(随机选择)
   3) 美女站(https://imeizi.me)
   4) 高清壁纸站(https://bing.imeizi.me)
   5) 自定义反代站点(需以http或者https开头)
  请选择伪装网站类型[默认:高清壁纸站]1
 伪装网站：

  是否允许搜索引擎爬取网站？[默认：不允许]
    y)允许，会有更多ip请求网站，但会消耗一些流量，vps流量充足情况下推荐使用
    n)不允许，爬虫不会访问网站，访问ip比较单一，但能节省vps流量
  请选择：[y/n]n
 允许搜索引擎：n

 是否安装BBR(默认安装)?[y/n]: y
```
## 服务检查
检查服务端口

- nginx服务
- v2ray服务
```c
root@VM-4-8-debian:~# ss -tanlp
State                  Recv-Q                 Send-Q                                 Local Address:Port                                 Peer Address:Port
LISTEN                 0                      128                                          0.0.0.0:22                                        0.0.0.0:*                     users:(("sshd",pid=913,fd=3))
LISTEN                 0                      128                                          0.0.0.0:80                                        0.0.0.0:*                     users:(("nginx",pid=24439,fd=6),("nginx",pid=24438,fd=6),("nginx",pid=24437,fd=6))
LISTEN                 0                      128                                          0.0.0.0:81                                        0.0.0.0:*                     users:(("nginx",pid=24439,fd=8),("nginx",pid=24438,fd=8),("nginx",pid=24437,fd=8))
LISTEN                 0                      128                                             [::]:22                                           [::]:*                     users:(("sshd",pid=913,fd=4))
LISTEN                 0                      128                                                *:3306                                            *:*                     users:(("v2ray",pid=24442,fd=3))
LISTEN                 0                      128                                             [::]:80                                           [::]:*                     users:(("nginx",pid=24439,fd=7),("nginx",pid=24438,fd=7),("nginx",pid=24437,fd=7))
```
查看v2ray信息
```c
root@VM-4-8-debian:~# ./v2ray.sh showInfo

 V2ray运行状态：已安装 V2ray正在运行
 V2ray配置文件:  /etc/v2ray/config.json
 V2ray配置信息：
   协议:  trojan
   IP/域名(address):  silicon01.fetchow.cn
   端口(port)：3306
   密码(password)：
   传输协议(network)： tcp
   底层安全传输(tls)：TLS
```
# 客户端
将_**config.yaml**_文件上传至公有云对象存储或云主机，达到类似机场订阅链接效果；优点：方便动态更新，缺点：针对个人使用，泄露信息。

对于个人使用或小范围使用，不建议将config.yaml公开，一般而言，少量人使用端口封的概率较低（不浏览敏感信息）

请注意，针对以下配置文件，不要使用新版核心的ClashX客户端，测试新版本客户端有可能针对配置文件做了一些调整导致无法代理，具体状态表现为没有流量经过ClashX；目前建议Github client中提供的客户端程序包；

手机端；IOS可申请美区账号，购买ShadowRocket；安卓没有手机，无法测试。
## 配置文件
主要修改**_proxies_**信息；如果**_proxies_**中**_name_**与下方**_proxy-groups_**中的**_proxies_**不一致，请修改保持一致；**_proxy-groups_**中的**_proxies_**表示使用哪个代理主机服务。

注意：以下配置文件样例不包含**_rules_**，完整配置文件请见Github config.yaml；一般而言，config.yaml rules无需修改；只需要修改**_proxies_**以及**_proxy-groups_**中的**_proxies_**信息即可。
#### proxies

- name：名称，后续分组使用
- server：v2ray主机IP地址
- port：v2ray服务端口
- type：协议类型
- password：v2ray随机密码
- sni：域名
- alpn：["h2", "http/1.1"]
```yaml
port: 7890
socks-port: 7891
allow-lan: true
mode: Rule
log-level: info
external-controller: :9090
proxies:
  - {name: "hk-02", server: , port: 443, type: vmess, uuid: , alterId: 0, cipher: auto, tls: true, skip-cert-verify: false, network: ws, ws-path: , ws-headers: {Host: }}
  - {name: "trojan", server: , port: 80, type: trojan, password: , sni: , alpn: ["h2", "http/1.1"]}
dns:
  enable: false
  ipv6: false
  listen: 127.0.0.1:53
  default-nameserver:
    - 8.8.8.8
  nameserver:
    - https://dns.alidns.com/dns-query
  fallback:
    - https://dns.google/dns-query
proxy-groups:
  - name: Proxy
    type: select
    proxies:
      - "hk-02"
      - "trojan"
  - name: ♻️ 自动选择
    type: url-test
    url: http://www.gstatic.com/generate_204
    interval: 300
    proxies:
      - "hk-02"
      - "trojan"
  - name: 🌍 国外媒体
    type: select
    proxies:
      - Proxy
      - ♻️ 自动选择
      - 🎯 全球直连
      - "hk-02"
      - "trojan"
  - name: 🌏 国内媒体
    type: select
    proxies:
      - 🎯 全球直连
      - Proxy
  - name: Ⓜ️ 微软服务
    type: select
    proxies:
      - 🎯 全球直连
      - Proxy
      - "hk-02"
      - "trojan"
  - name: 📲 电报信息
    type: select
    proxies:
      - Proxy
      - 🎯 全球直连
      - "hk-02"
      - "trojan"
  - name: 🍎 苹果服务
    type: select
    proxies:
      - Proxy
      - 🎯 全球直连
      - ♻️ 自动选择
      - "hk-02"
      - "trojan"
  - name: 🎯 全球直连
    type: select
    proxies:
      - DIRECT
  - name: 🛑 全球拦截
    type: select
    proxies:
      - REJECT
      - DIRECT
  - name: 🐟 漏网之鱼
    type: select
    proxies:
      - Proxy
      - 🎯 全球直连
      - ♻️ 自动选择
      - "hk-02"
      - "trojan"
```

参考：

- [https://v2xtls.org/](https://v2xtls.org/)
