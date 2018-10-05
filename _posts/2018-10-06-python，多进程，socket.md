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

#服务器端


```py
import socket
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
#     data = cli.recv(1024) #该方法阻塞的。客户端结束，这里接收空字符串
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
        data = cli.recv(1024)  # 该方法阻塞的。客户端结束，这里接收空字符串
        if not data:
            print(data, "datakong")
            break
        print("Recieve Data:", data.decode('utf-8'), "port", addr)
        # time.sleep(1)
        cli.send(b"s")

    cli.close()
    print("服务器端结束")
```

#客户端

```py
import socket
import time
import threading

HOST = '127.0.0.1'
PORT = 3214



def plus():
    try:
        sk = socket.socket()
        sk.connect((HOST, PORT))  
        #先执行该行的进程，靠前排队，直到该进程的socket断开连接，才下一进程。不阻塞，在服务器端才排队
        #print("子线程%s:" % (threading.current_thread().getName())) #能体现多进程，一次性打印60行，因为上一行不阻塞
        data = b"hello"

        for i in range(10):
            print("子线程%s运算结束后，number = %s" % (threading.current_thread().getName(), str(i)))
            # 能体现多进程，因为上一行不阻塞

            # sk.sendall(str(i).encode())  
            # 参数必为二进制类型,不能注定发送空字节，发了服务器端不会接受，但是客户端关闭连接，服务器端能接受空字节
            # data = sk.recv(1024)  # 该方法阻塞的
            # print("Recv data:", data)
            # temp = input('Please input message\n')
            # data = temp.encode()

            time.sleep(10)
            sk.sendall(str(i).encode()) #能体现多进程，因为sendall不阻塞
            print("32") #第一次循环能体现多进程，打印60次，因为sendall不阻塞。但10秒之后不能，因为59个进程卡在下一行！！！
            data = sk.recv(1024)  # 该方法阻塞的
            print("Recv data:", data)
            time.sleep(0.1)

            # print("子线程%s运算结束后，number = %s" % (threading.current_thread().getName(), str(i)))
            # time.sleep(0.1)
            #可以多线程

    except socket.error as err:
        print(err)
        print("客户端结束except")
    finally:
        sk.close()
        print("客户端结束finally")



for i in range(60):
    t = threading.Thread(target=plus)
    t.start()
```
