# onecloudopenwrt
玩客云直刷openwrt


- 扩容rootfs分区
```
resize2fs /dev/mmcblk1p2
```
## 公共国内外DNS

UDP传输时间短，加密统一选择：https

DNS查询：https://dns.ip.cn/

国内DNS:

阿里公共DNS：

223.5.5.5   类型选择：UDP

223.6.6.6   类型选择：UDP

阿里加密DNS：alidns_ip/dns-query   类型选择：https

阿里加密DNS：dns.alidns.com/dns-query   类型选择：https

DoT 地址:  dns.alidns.com

DoH 地址: https://dns.alidns.com/dns-query

腾讯公共DNS:

DNS：119.29.29.29  类型选择：UDP

腾讯加密DNS：doh.pub/dns-query  类型选择：https

腾讯加密DNS：sm2.doh.pub/dns-query  类型选择：https

DoT 地址:  dot.pub

DoH 地址: https://doh.pub/dns-query

DoH (国密 SM2,基于腾讯云政企国密解决方案) 地址:https://sm2.doh.pub/dns-query

360公共DNS

中国电信/铁通/移动：  101.226.4.6   218.30.118.6   类型选择：UDP

中国联通：  123.125.81.6      140.207.198.6   类型选择：UDP

百度公共DNS：180.76.76.76  类型选择：UDP

114DNS：114.114.114.114    114.114.115.115   类型选择：UDP

国外DNS：

CloudflareDNS：1.1.1.1   类型选择：UDP

Cloudflare加密DNS：1.1.1.1/dns-query   类型选择：tls

DoT 地址:  cloudflare-dns.com

DoH 地址: https://1dot1dot1dot1.cloudflare-dns.com/

Google DNS：

8.8.8.8      8.8.4.4    类型选择：UDP

谷歌加密DNS：https://dns.google/dns-query   类型选择：https

谷歌加密DNS：dns.google   类型选择：tls

Norton ConnectSafe： 199.85.126.10    199.85.127.10  类型选择：UDP

威瑞信：64.6.64.6   64.6.65.6  类型选择：UDP

Comodo安全DNS：  8.26.56.2    68.20.247.20  类型选择：UDP

DNS Watch： 84.200.69.80    84.200.70.40  类型选择：UDP

Quad9：

Quda9加密DNS：dns11.quad9.net/dns-query

DoT 地址:  dns.quad9.net

DoH 地址: https://dns.quad9.net/dns-query

协议类型知识点:

协议类型解析：UDP:53   tcp:53   tls:853   https:443
