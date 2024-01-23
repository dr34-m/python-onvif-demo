# python-onvif-demo

## 目标

使用Python管理支持`ONVIF`的设备。支持：

* 设备发现
* 获取RTSP地址
* 获取设备信息
* 截图
* 云台控制、缩放与聚焦
* 设置时间

<!--more-->

参考文档：[wsdl文档](https://www.onvif.org/onvif/ver20/util/operationIndex.html)、[Python API](https://pypi.org/project/onvif/) 本文严重参考并感谢：[《ONVIF with python》](https://blog.csdn.net/u010881576/article/details/116885774)

本文在上边的基础上，支持了多画面多码流的摄像头与NVR，并对更多功能做了详细说明，以下均基于`Python3.11`，其他版本可能略有不同

2024/1/23更新：适配NVR（多摄像头模式，可以独立控制每个摄像头），兼容了更多设备（部分设备的ONVIF有些限制，现在做了兼容）

## 实现

### 一、安装包

```shell
pip install onvif-zeep
# WSDiscovery 用于设备发现，不用则不再需要安装
pip install WSDiscovery
# requests 用于截图获取，不用则不需要安装
pip install requests
```

### 二、使用

基本类`onvifBase.py`

调用方

```python
import time
import json

from onvifBase import ws_discovery, OnvifClient, ptzChangeByClient


# 设备发现
print(ws_discovery())

client = OnvifClient('192.168.1.10', 80, 'admin', '123456', needSnapImg=False)
# 如果要控制特定摄像头，可以下边这样写
# client = OnvifClient('192.168.1.10', 80, 'admin', '123456', token="ProfileToken002", sourceToken= "VideoSourceToken002", nodeToken="NodeToken002", needSnapImg=False)

# 获取所有画面所有码流的RTSP地址、token(即ProfileToken)、sourceToken、nodeToken等信息
print(json.dumps(client.get_rtsp()))

# 获取设备信息
print(json.dumps(client.get_deviceInfo()))

# 设置时间
client.set_cam_time()

# 云台与聚焦控制
# 云台上移
ptzChangeByClient(client, 'Up', 1)
# 移动一秒
time.sleep(1)
# 然后停止
ptzChangeByClient(client, 'Up', 0)
```

