# onecloudopenwrt
玩客云直刷openwrt


- 扩容rootfs分区是为了充分用满EMMC容量
```
resize2fs /dev/mmcblk1p2
```
## OPENWRT挂载NTFS硬盘U盘OPENWRT支持读写NTFS分区
前提条件
1、你的OpenWrt必须已经支持USB和USB存储

2、将您的U盘或者USB硬盘连接到您的OpenWrt路由器，该设备及其分区可以正常识别到/dev（使用fdisk -l命令查看磁盘）
![1](https://github.com/3981877/onecloudopenwrt/assets/60610978/bb28b2fb-f2b2-4dc6-a704-04335f7b95f9)

此图已经识别到1T的硬盘了
- 必需安装的包
1、ntfs-3g
![2](https://github.com/3981877/onecloudopenwrt/assets/60610978/df0a1158-4426-45bc-8523-0f529e9686eb)


2、fdisk 可选择的使用热插拔脚本时自动检测文件系统类型所必需的。

配置
安装好上面包就已经支持NTFS硬盘了，然而请注意，您只能将分区挂载到现有目录。您可以使用创建一个目录mkdir比如说
```
mkdir -p /mnt/usb
```
要手动挂载分区:/dev/sda1代表一整块硬盘，/dev/sd1代表第一分区
```
ntfs-3g /dev/sda1 /mnt/usb -o rw,big_writes
```
上面命令的意思就是把/dev/sda1第一分区挂载到/mnt/usb目录

要在启动时(插入硬盘)自动挂载分区，请编辑/etc/rc.local:
```
sleep 1

ntfs-3g /dev/sda1 /mnt/usb -o rw,lazytime,noatime,big_writes

exit 0
```
需要卸载:

执行下图命令
```
umount /dev/sda1
```
为了让OpenWrt能够自动挂载它:
```
ln -s /usr/bin/ntfs-3g /sbin/mount.ntfs
```
通过脚本自动挂载
现在，您可以根据命令挂载卷了，下一步是在自动插入时挂载它。

为了让我们的驱动器挂载到插件上，我们利用热插拔系统。将以下文件创建为/etc/hotplug.d/block/10-mount

```
#!/bin/sh
# Copyright (C) 2011 OpenWrt.org
sleep 10 #more apps installed, need more time to load kernel modules!
blkdev=`dirname $DEVPATH`
if [ `basename $blkdev` != "block" ]; then
device=`basename $DEVPATH`
case "$ACTION" in
add)
mkdir -p /mnt/$device
# vfat & ntfs-3g check
if [ `which fdisk` ]; then
isntfs=`fdisk -l | grep $device | grep NTFS`
isvfat=`fdisk -l | grep $device | grep FAT`
isfuse=`lsmod | grep fuse`
isntfs3g=`which ntfs-3g`
else
isntfs=""
isvfat=""
fi
# mount with ntfs-3g if possible, else with default mount
if [ "$isntfs" -a "$isfuse" -a "$isntfs3g" ]; then
ntfs-3g /dev/$device /mnt/$device
elif [ "$isvfat" ]; then
mount -o iocharset=utf8 /dev/$device /mnt/$device
else
mount /dev/$device /mnt/$device
fi
;;
remove)
umount -l /dev/$device
;;
esac
fi
NOW，每当你插入一个NTFS USB磁盘，它应该会自动安装。
```
挂载成功后的效果：

![3](https://github.com/3981877/onecloudopenwrt/assets/60610978/de3f5eab-b9f4-4ded-a99b-60f7e4037bd1)

创建的/mnt/usb目录结构

![4](https://github.com/3981877/onecloudopenwrt/assets/60610978/ad84b2e6-8c3b-49d3-97ff-a785f6875d0e)

硬盘的数据资料完全正常显示了

- OPENWRT 网络文件共享设置

![5](https://github.com/3981877/onecloudopenwrt/assets/60610978/b0837ad0-0bb8-4824-b03c-613c8621a461)


以下是是访问\\192.168.2.254 (这是OP的旁路由)
![6](https://github.com/3981877/onecloudopenwrt/assets/60610978/a596121e-44ad-483d-85b1-c0e0fdbff2ef)
![7](https://github.com/3981877/onecloudopenwrt/assets/60610978/96034edf-992e-4a91-bfef-a133bbdf5c7d)


# OpenWrt设置samba密码
- 一. 编辑Luci模板
注释掉invalid users = root行
![1](https://github.com/3981877/onecloudopenwrt/assets/60610978/f88fdf80-052b-4383-be80-2fce4d810639)

- 二. 添加用户
```
smbpasswd -a root #然后输入需要设置的密码
```
- 三. 在Luci中勾选用户
![2](https://github.com/3981877/onecloudopenwrt/assets/60610978/730b2dd7-8e4d-4421-9d48-2880e7859843)


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

中国电信/铁通/移动：  101.226.4.6,    218.30.118.6   类型选择：UDP

中国联通：  123.125.81.6,        140.207.198.6   类型选择：UDP

百度公共DNS：180.76.76.76,    类型选择：UDP

114DNS：114.114.114.114,      114.114.115.115   类型选择：UDP

国外DNS：

CloudflareDNS：1.1.1.1   类型选择：UDP

Cloudflare加密DNS：1.1.1.1/dns-query   类型选择：tls

DoT 地址:  cloudflare-dns.com

DoH 地址: https://1dot1dot1dot1.cloudflare-dns.com/

Google DNS：

8.8.8.8,        8.8.4.4    类型选择：UDP

谷歌加密DNS：https://dns.google/dns-query   类型选择：https

谷歌加密DNS：dns.google   类型选择：tls

Norton ConnectSafe： 199.85.126.10,      199.85.127.10  类型选择：UDP

威瑞信：64.6.64.6,     64.6.65.6  类型选择：UDP

Comodo安全DNS：  8.26.56.2,      68.20.247.20  类型选择：UDP

DNS Watch： 84.200.69.80,      84.200.70.40  类型选择：UDP

Quad9：

Quda9加密DNS：dns11.quad9.net/dns-query

DoT 地址:  dns.quad9.net

DoH 地址: https://dns.quad9.net/dns-query

协议类型知识点:

协议类型解析：UDP:53   tcp:53   tls:853   https:443
