+++
author = "jasm"
title = "pybluez简单使用"
date = "2022-11-09"
description = "在linux系统下,使用python开源库pybluez来实现一个蓝牙Advertiser"
tags = [
  "linux",
  "python"
]
+++

# 关于蓝牙开发的一些基本知识

- 每个蓝牙模块在出厂时候时都有个6byte的唯一标识（如C4:03:A8:86:DC:F5），即**蓝牙设备地址**，蓝牙设备相互连接就靠这个地址；
- 常用**蓝牙传输协议**有RFCOMM和L2CAP，处于蓝牙通讯的传输层，类似于网络传输协议中的TCP和UDP；
- 由于各自的特性不同，**RFCOMM**使用场景更广泛也更易用，**L2CAP**则使用在一些对数据大小和完整性要求不高，但要求传输速度更快的场景中；
- 有了蓝牙设备地址、蓝牙传输协议，再确定一个蓝牙端口，即完成连接，类似于网络连接需要host ip，协议，端口，不过一个蓝牙设备最多只有30个端口；
- 蓝牙在应用开发过程中，有两种角色，分别是central（中心设备）和peripheral（外部设备），手机连蓝牙耳机时，手机就是central，蓝牙耳机就是peripheral；
- 蓝牙连接过程中，先是peripheral开启自己的服务并brodcast出去，然后central开始scan周边的蓝牙服务，扫描到了周边的peripheral提供的蓝牙服务后，再去连接并开始通讯。

# 使用pybluez

## 安装踩坑
遵循[pybluez](https://pybluez.readthedocs.io/en/latest/install.html)的官方步骤去安装一般都会有问题。因为绝大部分系统都缺少关于蓝牙开发的依赖。按照以下步骤在ubuntu上安装。

```
# 先安装依赖
sudo apt-get install python3-dev
sudo apt-get install libbluetooth-dev

# 最后安装pybluez
sudo pip3 install pybluez
```

## 配置
到这里之后，尝试跑一下[官方的rfcomm server示例](https://github.com/pybluez/pybluez/blob/master/examples/simple/rfcomm-server.py)时，会报错`bluetooth.btcommon.BluetoothError: (2, 'No such file or directory')`

此时需要修改配置，使蓝牙以兼容模式运行，修改/etc/systemd/system/dbus-org.bluez.service文件，将其中的
`ExecStart=/usr/lib/bluetooth/bluetoothd`修改为`ExecStart=/usr/lib/bluetooth/bluetoothd -C`
然后执行`sudo sdptool add SP`,添加serial port服务。

这时，再次尝试跑一下官方示例，又会报错permission denied,尝试sudo运行时，又会被提示找不到包，因为pip安装时，是以普通用户的身份安装的。
此时，需要运行`sudo chmod o+rw /var/run/sdp`将sdp文件的读写权限改为other也可以读写。

最后尝试运行以下官方示例，终于跑通了，拿另外一个准备好的蓝牙central设备来连接开发板并发送消息，消息被成功接收。

## 实现一个简单的peripheral server
当开启周边设备蓝牙后，其实是开启了sdp服务，可以由其他主设备搜索到，但是并不能由主设备上的应用连接，因为周边设备上并没有运行的服务，就类似于http连接中，我们可以ping通别人的ip，但是并不能访问到任何跑在端口上的服务。根据官方示例，实现一个peripheral server也十分简单，而且看起来跟服务端开发也十分相像。

```
import bluetooth

# 示例化一个蓝牙连接socket
server_sock = bluetooth.BluetoothSocket(bluetooth.RFCOMM)
# 将socket绑定到任意本地蓝牙端口
server_sock.bind(("", bluetooth.PORT_ANY))
server_sock.listen(1)

port = server_sock.getsockname()[1]

# 随便生成的一个service_id，这个uuid会被当作服务的唯一标识
uuid = "94f39d29-7d6d-437d-973b-fba39e49d4ee"

# 将这个蓝牙服务通过sdp服务器广播出去
bluetooth.advertise_service(server_sock, "SampleServer", service_id=uuid,
                            service_classes=[uuid, bluetooth.SERIAL_PORT_CLASS],
                            profiles=[bluetooth.SERIAL_PORT_PROFILE],
                            # protocols=[bluetooth.OBEX_UUID]
                            )

print("Waiting for connection on RFCOMM channel", port)

# socket开始等待接收连接，当未收到连接时，程序会一直阻塞在这里
client_sock, client_info = server_sock.accept()
print("Accepted connection from", client_info)

try:
    while True:
        # 持续接收信息，并作处理
        data = client_sock.recv(1024)
        if not data:
            break
        print("Received", data)
except OSError:
    pass

print("Disconnected.")

# 主要设备上的应用主动断开连接后，关闭socket
client_sock.close()
server_sock.close()
print("All done.")
```


