# BestHost

## 项目说明

BestHost 项目的初衷是为本地访问指定网站提供最佳的 IP 地址，即提供网站域名和 IP 的映射关系，此 IP 应是本地访问该网站最快的 IP 地址。
BestHost 还将考虑提升在 PC 上自动化设置 hosts 的体验。

## 项目背景和设计思路

由于某些原因，人们时常发现访问特定网站时速度极慢甚至无法访问。例如在中国访问 Github, JetBrains 等网站时就很可能出现这种情况。

有网友提供如下小技巧：
https://blog.csdn.net/weixin_44141495/article/details/108033395

其基本思路如下：
1. 利用网络上众多的 DNS 服务商，向其请求指定网站域名的 IP 地址。这时可得到多个 IP 地址。
2. 在本地计算机尝试访问这些 IP 地址，最终可找到本地访问该网站可用的并且是最快的一个 IP 地址（例如根据 ping 的耗时来排序）。
3. 将该网站域名与最快的 IP 的映射关系添加到本地计算机的 hosts 文件中。这样后续即可实现用"最快的 IP "访问特定的网站。

本项目的实现也是基于此思路。由于我熟悉 Java ，因此本项目使用 Java 编写，也希望能实现跨平台使用。
这就是本项目的初步想法，若有缺陷，欢迎交流和指正。

## 资源准备

### 1. DNS 服务商资源






提供商的服务，获取指定网站域名的 IP 地址。
可参考网站：
http://tool.chinaz.com/dns
https://www.ipaddress.com/

该思路和我使用的一个软件 [UsbEAm Hosts Editor](https://www.dogfight360.com/blog/475/) 应该有相同之处，可惜不知它具体的实现方式。


一个重要的步骤是如何收集全球范围的 DNS 提供商，于是通过 bing 搜索 "全球DNS列表"，简单点击了两个网页，例如：
https://www.iplaysoft.com/public-dns.html
https://ip.cn/dns.html
中都可利用。后续可继续查找。



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

1. 基本的功能实现
2. 收集 dns 服务器列表并验证。
3. JUnit单元测试验证上述2步骤
4. 如何使用管理员权限编辑 Windows 的 hosts 权限？结合编写 C 语言的程序？

// 后续需要考虑的工作
5. 爬取一个网站所要访问的所有域名，否则只解析指定域名的话，还是不太友好的，这个是一个大的功能
6. 是否需要对 dns 获取到的 ip 地址进行缓存，例如借助 sqlite3 缓存到本地。
7. 像 UsbEAm Hosts Editor 那样管理一类域名的资源，但是额外提供批量操作。








# 关于收集的 hosts 相关信息
https://ip.cn/dns.html
https://github.com/janmasarik/resolvers/blob/master/resolvers.txt
https://github.com/oneoffdallas/dohservers/blob/master/iplist.txt
https://cn.bing.com/search?q=dns大全&PC=U316&FORM=CHROMN
http://114.xixik.com/chinadns/#anchor4
https://dnsdaquan.com/











