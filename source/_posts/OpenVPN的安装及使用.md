---
title: OpenVPN的安装及使用
date: 2017-03-13 16:49:07
tags: OpenVPN
---

## 元信息

* 开发语言：C/C++
* 操作系统：跨平台，支持Linux、Windows、MacOS X等
* [github](https://github.com/OpenVPN/openvpn)

## 编译、运行、安装过程

### 安装方式

 源码安装

### 安装过程

* 安装c编译器及OpenVPN所需的插件

  gcc、g++、openssl、pam、lzo

  安装命令为：

  * sudo apt-get install gcc
  * sudo apt-get install g++
  * sudo apt-get install openssl
  * sudo apt-get install libssl-dev
  * sudo apt-get install libpam0g-dev  
  * sudo apt-get install liblzo2-dev  

* 从[官网](https://openvpn.net/index.php/download/community-downloads.html)下载源码

* 编译、安装

  * tar -zxf openvpn-2.3.14.tar.gz
  * cd openvpn-openvpn-2.3.14
  * ./configure
  * make
  * make install

### 实际操作

* 搭建CA，并为OpenVPN server端和client端产生证书和密钥

  使用[easy-rsa](https://github.com/OpenVPN/easy-rsa)来搭建CA。命令如下：

  * CA：
    * ./easyrsa init-pki
    * ./easyrsa build-ca
  * server:
    * ./easyrsa gen-req server
    * ./easyrsa sign-req server server
  * client:
    * ./easyrsa gen-req client1
    * ./easyrsa sign-req client client1
  * DH params:
    * ./easyrsa gen-dh

* OpenVPN server端配置

  在上一步中产生了所需要的文件，其中server端需要用到的有ca.crt、ca.key、dh.pem、server.crt、server.key

  在OpenVPN server端建立config文件夹，将以上文件拷贝到该文件夹下，此外 server端还需要配置文件server.conf，OpenVPN已经在安装包的sample/sample-config-files子目录中为我们提供了相关的示例文件server.conf，并且配置文件中的每个配置选项均有详细的英文说明。将server.conf文件拷贝到config目录中，然后再对其进行修改。

  server.conf文件中指定了监听的本机IP、端口号、CA证书的文件路径、server端的证书文件路径及私钥文件路径等，我们只需按照实际情况进行修改。

  OpenVPN自身的配置到此结束，但在使用前还必须开启Linux系统的路由转发功能：

    * vi /etc/sysctl.conf

  找到 “#net.ipv4.ip_forward=1” 这一行，删除那个 “#” 号，然后保存退出。接下来使转发生效：

    * sysctl -p  

  如果一切正常，你将只会看到以下结果：

    * net.ipv4.ip_forward=1

  然后设置iptables规则，通过配置NAT将VPN网段IP转发到eth0网卡：

    * iptables -t nat -A POSTROUTING -s 10.8.0.0/24 -o eth0 -j SNAT --to 10.108.112.6 （适用于固定IP的VPS）  

  或者:

    * iptables -t nat -A POSTROUTING -s 10.8.0.0/24 -o eth0 -j MASQUERADE （通用方法，自动获取eth0网卡的IP地址）  

  设置OpenVPN端口通过：

    * iptables -A INPUT -p TCP --dport 1194 -j ACCEPT  
    * iptables -A INPUT -m state --state ESTABLISHED,RELATED -j ACCEPT  

  最后，在服务端启动OpenVPN：

    * openvpn 配置文件（server.conf）路径

* OpenVPN client端配置

  client端可以有多个，在本次实验中只有一个client端：client1。客户端client1需要用到的文件有：ca.crt、client1.crt、client1.key。

  在OpenVPN client端建立config文件夹，将以上文件拷贝到该文件夹下，此外 client端还需要配置文件client.conf，OpenVPN已经在安装包的sample/sample-config-files子目录中为我们提供了相关的示例文件client.conf，并且配置文件中的每个配置选项均有详细的英文说明。将client.conf文件拷贝到config目录中，然后再对其进行修改。

  client.conf文件中指定了连接的远程服务器的实际IP地址和端口号、CA证书的文件路径、当前客户端的证书文件路径和私钥文件路径等，我们只需按照实际情况进行修改。

  在客户端启动OpenVPN：

    * openvpn 配置文件（client.conf）路径

  接下来，在client端ping服务端的虚拟地址：

    * ping 10.8.0.1

  如果能ping通，则说明客户端已经和OpenVPN服务端连接成功。

### 证书导入接口

  由上一节的实际操作过程可知，server端的证书导入接口在配置文件 server.conf 中；client端的证书导入接口在配置文件 client.conf 中。
