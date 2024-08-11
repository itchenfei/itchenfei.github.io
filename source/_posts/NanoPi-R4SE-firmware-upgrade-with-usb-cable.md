---
title: NanoPi R4SE 使用USB线进行固件升级
date: 2024-08-11 22:14:19
tags: SBC
---
# 概述
NanoPi R4SE内置eMMC存储，提供了多种固件升级的方式，本文介绍的是通过USB烧写的方式,其他方式查看[文档](https://wiki.friendlyelec.com/wiki/index.php/NanoPi_R4SE/zh#.E7.83.A7.E5.86.99.E7.B3.BB.E7.BB.9F.E5.88.B0eMMC)。
USB烧写固件主要分为五步：
1. 下载驱动和固件
2. 安装驱动
3. 进入升级模式
4. 升级固件
5. 连接设备

# 下载固件
NanoPi R4SE提供百度网盘和Google Drive两种下载方式，选择一种即可。  
官方地址：https://download.friendlyelec.com/NanoPiR4SE  
1. 下载DriverAssitant: 打开链接后进入05_Tools文件夹，下载DriverAssitant_xx.zip
2. 下载固件: 打开链接后进入03_USB upgrade images文件夹，下载固件，推荐带有docker名称字样的固件，例如：rk3399-usb-friendlywrt-21.02-docker-20231031.zip

# 安装驱动
1. 解压DriverAssitant_xx.zip
2. 运行.exe文件安装驱动
3. 重启电脑

# 进入升级模式
1. 关机，拔掉TF卡和所有USB设备，拔掉电源
2. 卡针按住Mask，接通电源，等待3S后松手，PWR灯常亮，其他灯长灭，如果不是此状态，重复1~2步
3. 接USB线到第一个口
4. 系统设备管理器会加载 Class for rockusb devices
![device](class_for_rockusb_devices.png)

# 升级固件
1. 解压rk3399-usb-friendlywrt-23.05-docker-20231031.zip包
2. 在解压后的在文件夹内找到RKDevTool.exe，双击运行
3. 在弹出的页面中点击执行
![upgrade.png](upgrade.png)
4. 等待升级完成后，设备会自动重启

# 连接设备
1. 准备一条网线，连接NanoPi R4SE的LAN口和PC
2. PC端打开命令行，输入ipconfig, 查看以太网适配器以太网的IPv4地址，等待出现如图IP为192.168.2.1的网络接口
![nic.png](nic.png)
3. 打开网页端，输入`192.168.2.1`，进入LuCi页面配置，初始用户名密码为`root`和`password`