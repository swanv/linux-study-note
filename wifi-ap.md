
# Software access point (AP)

参考：https://wiki.archlinux.org/index.php/Software_access_point#Wi-Fi_device_must_support_AP_mode

## Requirements 需求

**Wi-Fi 设备必须支持 AP 模式**

你需要一个适配 nl80211 的无线设备，支持 AP操作模式。可以通过运行`iw list` 命令来确认。输出信息中 `Supported interface mode` 需要显示支持 `AP`: 

```
Supported interface modes:
    * IBSS
    * managed
    * AP
    * AP/VLAN
    * WDS
    * monitor
    * mesh point
```

**Wireless client and software AP with a single Wi-Fi device**

其中约束 `#channels <= 1` 表示你的软AP必须运行在和Wi-Fi客户端同样的 channel 上。在 `hostapd.conf` 中查看 `channel` 配置。

```
$ iw list
-------------------------------------------------
vaild interface combination:
    * #{ AP, mesh point } <= 8
      total <= 8, #channels <= 1
```

如果你想使用这种特性，比如有线网络连接不可用，你就需要创建两个独立的虚拟网络接口. 为物理设备 wlan0 创建虚拟网络如下： 为 wlan0_sta 自己和 AP/hostapd 分别创建虚拟网络接口：

```sh
# iw dev wlan0 interface add wlan0_sta type managed addr 12:34:56:78:ab:cd
# iw dev wlan0 interface add wlan0_ap type managed addr 12:34:56:78:ab:ce
```

随机 mac 地址可以使用 macchanger 来生成



## 使用 create_ap 创建 ap

create_ap 是一个快捷脚本，本质上还是使用 hostapd 来创建热点的。

我在 archlinux 上进行配置

https://github.com/oblique/create_ap

安装需要配置

```bash
pacman -S util-liunx procs hostapd iproute2 iw haveged dnsmasq iptable
```

```bash
pacman -S create_ap
```

```sh
create_ap wlan0 eth0 MyAccessPoint MyPassPhrase
```

以服务启动

```sh
systemctl start create_ap
```

开机启动

```sh
systemctl enable create_ap
```


发现要编译时安装时，很多基础套件没有， 使用以下命令可以安装需要的套件
```
pacman -S base-deval
```
