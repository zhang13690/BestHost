# BestHost

## 项目说明

BestHost项目的初衷是为本地访问指定网站提供最佳的IP地址，即提供网站域名和本地访问该网站最快的IP之间的映射关系。
BestHost致力于提供更方便的、自动化的方式来完成这一过程在个人电脑上的设置。

## 项目背景和基本设计思路

我时长发现访问 github, idea 等 (SW-1) 技术相关网站时，速度较慢。有网友提供如下小技巧：

https://blog.csdn.net/weixin_44141495/article/details/108033395

基本思路大致如下：
1. 利用众多 DNS 提供商的服务，获取指定网站域名的 IP 地址。
可参考网站：
http://tool.chinaz.com/dns
https://www.ipaddress.com/
2. 本地访问获取的这些 IP 地址，找到本地访问最快的一个 IP 地址（如根据 ping 的时间来排序）
3. 将最快的这个 IP 地址与域名映射关系加入到本地计算机的 hosts 文件列表中。

该思路和我使用的一个软件 [UsbEAm Hosts Editor](https://www.dogfight360.com/blog/475/) 应该有相同之处，可惜不知它具体的实现方式。

项目设计也基于此基本思路。由于我熟悉Java，因此本项目使用Java编写。

一个重要的步骤是如何收集全球范围的 DNS 提供商，于是通过 bing 搜索 "全球DNS列表"，简单点击了两个网页，例如：
https://www.iplaysoft.com/public-dns.html
https://ip.cn/dns.html
中都可利用。后续可继续查找。

这是项目的初步想法，欢迎交流。

## 技术点

1. DNS原理。可大概了解。可参考网址：
https://www.cnblogs.com/linfangnan/p/12771157.html
https://zhuanlan.zhihu.com/p/143360037
https://blog.csdn.net/hunanchenxingyu/article/details/21488291

2. 使用 DNS 协议获取域名的 IP 地址。
项目的一个核心点就是要通过 DNS 服务器获取域名的 IP 地址。如何使用 Java 来建立与 DNS 服务器的通信，获取域名的 IP 地址呢？
自己去了解 DNS 协议并实现当然可以，可是因为我懒、没时间、想省事等接口，所以想借助 Java 类库直接实现。
找到这个合适的类库并会使用它还是花费了我一点时间的。我在 github 中搜索 "dns" 并筛选出 Java 语言，选择了
[dnsjava](https://github.com/dnsjava/dnsjava) 这个库。并摸索出一个简单的使用方法：

```java
import org.xbill.DNS.*;

import java.io.IOException;
import java.net.*;

public class IPByDNS {
    public static void main(String[] args) throws IOException {
        // 创建 SimpleResolver 即 DNS 服务商对象。
        // 例如这里的 DNS 地址是 8.8.8.8, 且 DNS 服务器默认均使用 53 端口
        SimpleResolver rm = new SimpleResolver(new InetSocketAddress("8.8.8.8", 53));
        // Lookup 对象用于从 DNS 查询域名信息。 这里指定查询域名为 microsoft.com
        Lookup lookup = new Lookup("microsoft.com");
        lookup.setResolver(rm); // 使用 rm 解析
        // 调用 run 方法进行获取信息，得到 Record 数组对象。
        Record[] records = lookup.run();
        // 遍历 s
        for (Record r : records) {
            // 对于返回的 IPv4 的地址信息，可以直接强转成 ARecord 对象。若是 IPv6 地址，则使用 AAAARecord 对象
            ARecord ar = (ARecord) r;
            System.out.println(ar.getAddress().getHostAddress()); // 输出 IP 地址
        }
        /*
        result:
            104.215.148.63
            40.76.4.15
            40.112.72.205
            40.113.200.201
            13.77.161.179
         */
    }
}
```

## 最近目标

// TODO
