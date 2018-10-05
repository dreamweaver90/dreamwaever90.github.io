---
layout:     post
title:      python的多进程与socket
subtitle:   一些实例
date:       2018-10-05
author:     lengyu
header-img: img/post-bg-kuaidi.jpg
catalog: true
tags:
    - python
---

##服务器端
'import socket
import time

HOST = ''   #空字符串标示127.0.0.1
PORT = 3214

sk = socket.socket() # 默认使用IPV4和TCP

sk.bind((HOST,PORT))

sk.listen(100)

# cli, addr = sk.accept() # 等待连接(阻塞式),在连接到来之前会阻塞在这里
#
# print("Client Addr:", addr)
#
# while True:
#     print("18")
#     data = cli.recv(1024) #该方法阻塞的。客户端结束，这里接收空字符串
#     print("20")
#     if not data:
#         print(data,"datakong")
#         break
#     print("Recieve Data:", data.decode('utf-8'),"port",addr)
#     # time.sleep(1)
#     cli.send(b"s")
#
# cli.close()
# print("服务器端结束")
#

while True:
    cli, addr = sk.accept()  # 等待连接(阻塞式),在连接到来之前会阻塞在这里
    print("Client Addr:", addr)
    while True:
        print("18")
        data = cli.recv(1024)  # 该方法阻塞的。客户端结束，这里接收空字符串
        print("20")
        if not data:
            print(data, "datakong")
            break
        print("Recieve Data:", data.decode('utf-8'), "port", addr)
        # time.sleep(1)
        cli.send(b"s")

    cli.close()
    print("服务器端结束")'
