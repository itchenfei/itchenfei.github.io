---
title: Pyserial的基础用法
date: 2022-03-30 19:30:05
tags: [pyserial]
categories: python
---
[TOC]

# serial与pyserial

## 出现原因

**ModuleNotFoundError: No module named 'serial'**

新的环境导入serial运行时候会出现`ModualNotFoundError`,习惯`pip install serial`。

安装完成后又会出现如下提示：

**AttributeError: module 'serial' has no attribute 'Serial'**

其实是我们装错了库，应该装的是pyserial，不过pyserial库的方法和serial库正好重名了。

serial库是一个序列化/反序列化json和yaml文件的库，跟我们的串口操作无关。

## 解决办法

1. 卸载serial库，后卸载pyserial库

   ```bash
   pip uninstall serial -y
   ```

2. 关闭命令行窗口！必要，刷新命令行环境。

3. 重新安装pyserial库

   ```bash
   pip install pyserial -y
   ```

# 串口打开

Windows上串口名称一般类似COM1，Linux上串口名称一般类似/dev/ttyUSB1

## 使用参数默认打开端口

使用Python打开串口进行通信仅需要实例化`serial.Serial`类。

实例化`Serial`类需要传入串口名称，波特率，超时时间。

```python
import serial  # 导入Pyserial的serial

# 使用波特率115200，超时时间0.8，打开端口名称是COM3的端口
s = serial.Serial('COM3', baudrate=115200, timeout=0.8)
print(s.is_open)
```

## 手动配置串口后打开

```python
import serial  # 导入Pyserial的serial

s = serial.Serial()  # 实例化Serial类，但是不传参数
s.port = 'COM3'  # 设置打开的端口为COM3
s.baudrate = 115200  # 设置波特率115200
s.timeout = 0.8  # 设置超时时间0.8
print(s.is_open)  # False 默认未打开
s.open()  # 打开配置好的端口
print(s.is_open)  # True 已经手动打开
```



# 编码和解码

当我们在PyCharm输入并保存为`test.py`

```python
# -*- encoding=utf-8 -*-

print("hello world")
```

这个print语句就是Unicode字符串，当我们保存这个py文件时，系统会将文件内容编译成utf-8格式的字节码，然后保存编译后的字节码到硬盘，这个过程就是编码(encode)过程；

当计算机读取`test.py`时，如果在test.py的文件头部看到编码的类型，则会使用指定的编码类型进行解码，这里使用的是使用指定的 `utf-8`编码格式进行解码，这个读取文件的过程其实就是解码(decode)的过程。

Python3如果不指定编码和解码格式，默认都是utf-8格式。

串口也需要进行编码和解码：

1. 在将字符串写入串口之前，首先要将字符串(encode)编译成为utf-8格式的字节码
2. 从串口中读取数据之后，使用utf-8将字节码解码(decode)成为原始的字符串

# 串口的写入

## 一般AT指令的写入

在我们打开AT、UART或DEBUG口后，可以直接使用write方法写入命令，write命令写入后会返回已经写入的字节数量。

```python
import serial

s = serial.serial_for_url('loop://', baudrate=115200, timeout=0)
nums = s.write('0123456789'.encode())
print(nums)
```

## 控制符的写入

在某些发送短信等特殊的AT可能需要我们模拟`CTRL+Z`，我们要写入对应操作的ascii对应的Unicode编码，然后在进行编码，例如：

```python
import serial

s = serial.serial_for_url('loop://', baudrate=115200, timeout=0)
nums = s.write(chr(0x1A).encode())
print(nums)
```

具体的对应关系可以参考：

https://en.wikipedia.org/wiki/ANSI_escape_code

https://invisible-island.net/xterm/ctlseqs/ctlseqs.html

# 串口的读取

## 串口的读取方式

pyserial的串口主要有五种读取方式`read`、`readline`、`readlines`、`readall`、`read_until`。

这里只介绍read和readline。

1. read(i=1)，读取i个字节，默认为1
2. readline，读取一行数据，一行的判断标准是\n换行符

读取到的内容需要解码成原始字符串

```python
import serial

s = serial.serial_for_url('loop://', baudrate=115200, timeout=0)
nums = s.write("0123456789".encode('utf-8'))  # 将0123456789编码成utf-8格式后，写入串口
print(s.readline().decode('utf-8'))  # 从串口中读取一行，并解码成原始的字符串
```



## 串口读取方式和串口设置的timeout关系

串口初始化时，timeout有三种设置方式，和read和readline分别有如下的对应关系。

1. timeout=0.8，这个0.8是任意float类型的数

   a.  read(100)，如果在0.8s内读到了100个字节，直接返回读到的字节；如果超过0.8s，读到多少字节就返回多少字节

   b. readline()，如果0.8秒内读到了\n，则直接返回读到的一行；如果超过0.8s，读到多少字节就返回多少字节

2. timeout=0

   a. read(100)，缓冲buffer内如果200字节，则只返回100字节，如果仅有99字节，则立即返回99字节

   b. readline()，缓冲buffer内如果有\n，则返回到\n，如果没有\n，则返回buffer内的全部内容

3. timeout=None

   a. read(100)，一直读，直到100个字节后返回，如果不到100个字节，则会一直阻塞状态

   b. readline()，一直读，直到读到\n，返回读到内容，如果没有\n，则会一直是阻塞状态

