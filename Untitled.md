总升级流程[文档中心](https://open.iot.10086.cn/doc/aiot/fuse/detail/1204)

# 全流程



![img](https://i-blog.csdnimg.cn/direct/02b89660448a4098a806bf6ed49e6ec2.png)

# 查询报文

POST /fuse-ota/XHm5ltr9qA/d0/version HTTP/1.1

Host: iot-api.heclouds.com

Content-Type: application/json

Authorization: version=2022-05-01&res=userid%2F498670&et=1774161817&method=sha1&sign=ggJ%2F%2BwY0V%2FwI9Xk5hYo0Um0ggLA%3D

Content-Length: 45



{"s_version": "V1.0", "f_version": "V1.0"}



## 响应

[Tcp client  来自服务端消息 ] HTTP/1.1 200 OK

Date: Sun, 22 Mar 2026 05:54:35 GMT

xxxxxxxxxx41 1graph TD2    subgraph 硬件外设层3        S1[DHT11 温湿度]4        S2[AP3216C 光/距]5        S3[W25QXX SPI Flash]6        S4[LCD & LED/Beep]7        S5[ESP8266 Wi-Fi]8    end9​10    subgraph FreeRTOS 任务层11        T1[Sensor Task]12        T2[LCD UI Task]13        T3[Net Task 核心轮询]14        T4[Watchdog Task]15        16        Q1[(数据队列 Queue)]17        Q2[(MQTT 上报队列)]18    end19​20    subgraph 协议与业务网络层21        P1[动态心跳 & 重连状态机]22        P2[Paho MQTT 封包解包]23        P3[离线数据滞留/回补管理]24        P4[OTA HTTP引擎 & HMAC鉴权]25    end26​27    subgraph 云端28        C1[OneNET 物模型]29        C2[FUSE OTA 发版中心]30    end31​32    S1 & S2 --> T133    T1 -->|实时数据| Q134    T1 -->|上报数据| Q235    Q1 --> T2 --> S436    37    Q2 --> T338    S3 <-->|断网缓存| T339    T3 <--> P1 & P2 & P3 & P4 <--> S5 <--> C1 & C240    41    T4 -.->|位图打卡监控| T1 & T2 & T3mermaid#mermaidChart0{font-family:sans-serif;font-size:16px;fill:#333;}@keyframes edge-animation-frame{from{stroke-dashoffset:0;}}@keyframes dash{to{stroke-dashoffset:0;}}#mermaidChart0 .edge-animation-slow{stroke-dasharray:9,5!important;stroke-dashoffset:900;animation:dash 50s linear infinite;stroke-linecap:round;}#mermaidChart0 .edge-animation-fast{stroke-dasharray:9,5!important;stroke-dashoffset:900;animation:dash 20s linear infinite;stroke-linecap:round;}#mermaidChart0 .error-icon{fill:#552222;}#mermaidChart0 .error-text{fill:#552222;stroke:#552222;}#mermaidChart0 .edge-thickness-normal{stroke-width:1px;}#mermaidChart0 .edge-thickness-thick{stroke-width:3.5px;}#mermaidChart0 .edge-pattern-solid{stroke-dasharray:0;}#mermaidChart0 .edge-thickness-invisible{stroke-width:0;fill:none;}#mermaidChart0 .edge-pattern-dashed{stroke-dasharray:3;}#mermaidChart0 .edge-pattern-dotted{stroke-dasharray:2;}#mermaidChart0 .marker{fill:#333333;stroke:#333333;}#mermaidChart0 .marker.cross{stroke:#333333;}#mermaidChart0 svg{font-family:sans-serif;font-size:16px;}#mermaidChart0 p{margin:0;}#mermaidChart0 .label{font-family:sans-serif;color:#333;}#mermaidChart0 .cluster-label text{fill:#333;}#mermaidChart0 .cluster-label span{color:#333;}#mermaidChart0 .cluster-label span p{background-color:transparent;}#mermaidChart0 .label text,#mermaidChart0 span{fill:#333;color:#333;}#mermaidChart0 .node rect,#mermaidChart0 .node circle,#mermaidChart0 .node ellipse,#mermaidChart0 .node polygon,#mermaidChart0 .node path{fill:#ECECFF;stroke:#9370DB;stroke-width:1px;}#mermaidChart0 .rough-node .label text,#mermaidChart0 .node .label text,#mermaidChart0 .image-shape .label,#mermaidChart0 .icon-shape .label{text-anchor:middle;}#mermaidChart0 .node .katex path{fill:#000;stroke:#000;stroke-width:1px;}#mermaidChart0 .rough-node .label,#mermaidChart0 .node .label,#mermaidChart0 .image-shape .label,#mermaidChart0 .icon-shape .label{text-align:center;}#mermaidChart0 .node.clickable{cursor:pointer;}#mermaidChart0 .root .anchor path{fill:#333333!important;stroke-width:0;stroke:#333333;}#mermaidChart0 .arrowheadPath{fill:#333333;}#mermaidChart0 .edgePath .path{stroke:#333333;stroke-width:2.0px;}#mermaidChart0 .flowchart-link{stroke:#333333;fill:none;}#mermaidChart0 .edgeLabel{background-color:rgba(232,232,232, 0.8);text-align:center;}#mermaidChart0 .edgeLabel p{background-color:rgba(232,232,232, 0.8);}#mermaidChart0 .edgeLabel rect{opacity:0.5;background-color:rgba(232,232,232, 0.8);fill:rgba(232,232,232, 0.8);}#mermaidChart0 .labelBkg{background-color:rgba(232, 232, 232, 0.5);}#mermaidChart0 .cluster rect{fill:#ffffde;stroke:#aaaa33;stroke-width:1px;}#mermaidChart0 .cluster text{fill:#333;}#mermaidChart0 .cluster span{color:#333;}#mermaidChart0 div.mermaidTooltip{position:absolute;text-align:center;max-width:200px;padding:2px;font-family:sans-serif;font-size:12px;background:hsl(80, 100%, 96.2745098039%);border:1px solid #aaaa33;border-radius:2px;pointer-events:none;z-index:100;}#mermaidChart0 .flowchartTitleText{text-anchor:middle;font-size:18px;fill:#333;}#mermaidChart0 rect.text{fill:none;stroke-width:0;}#mermaidChart0 .icon-shape,#mermaidChart0 .image-shape{background-color:rgba(232,232,232, 0.8);text-align:center;}#mermaidChart0 .icon-shape p,#mermaidChart0 .image-shape p{background-color:rgba(232,232,232, 0.8);padding:2px;}#mermaidChart0 .icon-shape rect,#mermaidChart0 .image-shape rect{opacity:0.5;background-color:rgba(232,232,232, 0.8);fill:rgba(232,232,232, 0.8);}#mermaidChart0 .label-icon{display:inline-block;height:1em;overflow:visible;vertical-align:-0.125em;}#mermaidChart0 .node .label-icon path{fill:currentColor;stroke:revert;stroke-width:revert;}#mermaidChart0 :root{--mermaid-alt-font-family:sans-serif;}云端协议与业务网络层FreeRTOS 任务层硬件外设层实时数据上报数据断网缓存位图打卡监控位图打卡监控位图打卡监控DHT11 温湿度AP3216C 光/距W25QXX SPI FlashLCD & LED/BeepESP8266 Wi-FiSensor TaskLCD UI TaskNet Task 核心轮询Watchdog Task数据队列 QueueMQTT 上报队列动态心跳 & 重连状态机Paho MQTT 封包解包离线数据滞留/回补管理OTA HTTP引擎 & HMAC鉴权OneNET 物模型FUSE OTA 发版中心

Content-Length: 71

Connection: keep-alive

Access-Control-Allow-Headers: *

Access-Control-Allow-Origin: *

Server: nginx

Pragma: no-cache



{"code":0,"msg":"succ","request_id":"399527bd39274e4e8f881e4714911a19"}

# 检测升级任务报文（TYPE是2）

GET /fuse-ota/XHm5ltr9qA/d0/check?type=2&version=V1.0 HTTP/1.1

Host: iot-api.heclouds.com

Content-Type: application/json

Authorization: version=2022-05-01&res=userid%2F498670&et=1774161817&method=sha1&sign=ggJ%2F%2BwY0V%2FwI9Xk5hYo0Um0ggLA%3D

## 响应

[Tcp client  来自服务端消息 ] HTTP/1.1 200 OK

Date: Sun, 22 Mar 2026 05:58:58 GMT

Content-Type: application/json;charset=UTF-8

Content-Length: 183

Connection: keep-alive

Access-Control-Allow-Headers: *

Access-Control-Allow-Origin: *

Server: nginx

Pragma: no-cache



{"code":0,"msg":"succ","data":{"target":"v3.0","tid":1317438,"size":8260,"md5":"b54809186a2dd720e80265402e8cb168","status":1,"type":1},"request_id":"8b9b21aff5f84af7ad9595c368613e16"}

注意：参数说明

```
{
	"code": 0,
	"msg": "succ",
	"request_id": "**********",
	"data": {
		"target": "1.2", // 升级任务的目标版本
		"tid": 12, //任务ID
		"size": 123, //文件大小
		"md5": "dfkdajkfd", //升级文件的md5
		"status": 1 | 2 | 3, //1 ：待升级， 2 ：下载中， 3 ：升级中
		"type": 1 | 2 // 1:完整包，2：差分包  
	}
}
```



# 查询升级状态（type=2)

GET /fuse-ota/XHm5ltr9qA/d0/1317438/check?type=2&version=V1.0 HTTP/1.1

Host: iot-api.heclouds.com

Content-Type: application/json

Authorization: version=2022-05-01&res=userid%2F498670&et=1774161817&method=sha1&sign=ggJ%2F%2BwY0V%2FwI9Xk5hYo0Um0ggLA%3D





## 响应

[Tcp client  来自服务端消息 ] HTTP/1.1 200 OK

Date: Sun, 22 Mar 2026 06:11:45 GMT

Content-Type: application/json;charset=UTF-8

Content-Length: 91

Connection: keep-alive

Access-Control-Allow-Headers: *

Access-Control-Allow-Origin: *

Server: nginx

Pragma: no-cache



{"code":0,"msg":"succ","data":{"status":5},"request_id":"bdc6376c84564d20ab37676c0fbf9ff3"}



注意：

```
"status":1 //1: 待升级、2: 下载中、3:升级中、4:升级成功、5: 升级失败、6:升级取消  
```

# 下载升级包

GET /fuse-ota/XHm5ltr9qA/d0/1317438/download?range=0-8259 HTTP/1.1

Host: iot-api.heclouds.com

Authorization: version=2022-05-01&res=userid%2F498670&et=1774161817&method=sha1&sign=ggJ%2F%2BwY0V%2FwI9Xk5hYo0Um0ggLA%3D

注意：

```
头部Range字段解释：Range=start-end，目前只支持如下几种模式
1、Range=start-, 获取第start+1个字节到最后的数据
例如：Range=0-, 获取所有数据
Range=2-,获取第3个到最后的数据
注意：如果start>=文件总长度,则默认start=0
2、Range=start-end, 获取第start+1个字节到第end+1个字节
例如：Range=0-99,获取前100个字节
注意：end>=文件总长度len，则默认end=len-1
start>end, start被设置为0
3、Range=-end,获取最后end个字节数据
例如：Range=-100,获取最后100个字节数据
注意：如果end>文件总长度len,则默认end=len（获取所有文件）
4、如果头部Range不存在、或者Range没有按照如上规则上传，都视为返回所有数据。
5、 Range:01-02,视为1-2
```



## 响应

[Tcp client  来自服务端消息 ] HTTP/1.1 200 OK

Date: Sun, 22 Mar 2026 06:24:39 GMT

Content-Type: application/octet-stream;charset=UTF-8

Content-Length: 8260

Connection: keep-alive

Access-Control-Allow-Headers: *

Access-Control-Allow-Origin: *

Content-Disposition: attachment; filename="ry"

Ota-Errno: 0

Server: nginx

Pragma: no-cache



�



# 上报下载进度

step后面的数字代表意义详见[文档中心](https://open.iot.10086.cn/doc/aiot/fuse/detail/1449)

POST /fuse-ota/XHm5ltr9qA/d0/1317437/status HTTP/1.1

Host: iot-api.heclouds.com

Content-Type: application/json

Authorization: version=2022-05-01&res=userid%2F498670&et=1774161817&method=sha1&sign=ggJ%2F%2BwY0V%2FwI9Xk5hYo0Um0ggLA%3D

Content-Length: 20



{"step": 10}

注意:

```
参数说明：

设备在下载升级包的过程中（分片下载），可以根据需要上报下载进度（设备处于“下载中”，才能上报step=[0,100]）；
如果设备上报的下载进度为100（即step:100），那么平台会将设备的升级状态从“正在下载”修改为“正在升级”状态；
注意：此时不用再传递101
只有当设备处于“正在下载”状态时，设备才能够使用该接口上报下载进度，其他状态将返回“invalid state”的错误；
step如果大于100，将作为上报状态使用（设备处于：待升级、下载中、升级中，这三个状态时，可以通过上报如下状态码完成升级流程。其他状态如：已取消，升级失败、升级成功时，不能上报如下状态）：

状态码	说明
101	升级包下载成功（设备状态变成：升级中）。
102	下载失败,空间不足（设备状态变成：升级失败）。
103	下载失败,内存溢出（设备状态变成：升级失败）。
104	下载失败,下载请求超时（设备状态变成：升级失败）。
105	下载失败,电量不足（设备状态变成：升级失败）。
106	下载失败,信号不良（设备状态变成：升级失败）。
107	下载失败,未知异常（设备状态变成：升级失败）。
201	升级成功，此时会把设备的版本号修改为任务的目标版本（设备状态变成：升级完成）。
202	升级失败,电量不足（设备状态变成：升级失败）。
203	升级失败,内存溢出（设备状态变成：升级失败）。
204	升级失败,升级包与当前任务目标版本不一致（设备状态变成：升级失败）。
205	升级失败,MD5校验失败（设备状态变成：升级失败）。
206	升级失败,未知异常（设备状态变成：升级失败）。
207	达到最大重试次数（设备状态变成：升级失败）。
特别说明:
设备上报100后不能再次上报101，两者都相当于升级包下载完成。
下载中状态可以上报下载中的状态码或者升级中的状态码； 升级中状态只能上报升级中的状态码。
```



## 响应

[Tcp client  来自服务端消息 ] HTTP/1.1 200 OK Date: Sun, 22 Mar 2026 06:32:12 GMT Content-Type: application/json;charset=UTF-8 Content-Length: 79 Connection: keep-alive Access-Control-Allow-Headers: * Access-Control-Allow-Origin: * Server: nginx Pragma: no-cache {"code":20,"msg":"task finish","request_id":"03e88f9bc9394c12ba59ea741c6a817a"}





# 上报最后结果

POST /fuse-ota/XHm5ltr9qA/d0/1317439/status HTTP/1.1

Host: iot-api.heclouds.com

Content-Type: application/json

Authorization: version=2022-05-01&res=userid%2F498670&et=1774166798&method=sha1&sign=77UkKsVnExbp1bq0YmtQy95euCY%3D

Content-Length: 15



{"step":201}



## 响应

[Tcp client  来自服务端消息 ] HTTP/1.1 200 OK

Date: Sun, 22 Mar 2026 07:39:12 GMT

Content-Type: application/json;charset=UTF-8

Content-Length: 71

Connection: keep-alive

Access-Control-Allow-Headers: *

Access-Control-Allow-Origin: *

Server: nginx

Pragma: no-cache



{"code":0,"msg":"succ","request_id":"20c0b0ac5dfe4193b69f3d7f74dd6468"}



# OTA注意事项



1.最好不要用MQTT协议的方式来触发，延迟很大，成功机会小

![img](https://i-blog.csdnimg.cn/direct/46a0c845108346eebefbc143eb56b8ec.png)



2.在下面可以下发onenetjson数据，向OTA平台发送http报文来触发OTA升级

![img](https://i-blog.csdnimg.cn/direct/c28a1bd0c7224a1fbf89d044aafabb96.png)

3.OTA鉴权密钥是日期20220501版的，ONENET提供的鉴权生成exe日期是2018，不能用来生成，要自己计算。在onenet平台这里也可以查看，但是设置的时间戳et只有几十分钟，要重新刷新

![img](https://i-blog.csdnimg.cn/direct/c28a1bd0c7224a1fbf89d044aafabb96.png)

4.OTA这里要选择MCU固件

![img](https://i-blog.csdnimg.cn/direct/5fb97ff71d044e8c9e68a5d19b8ea548.png)

5.在ota.h里面填字自己的信息

```c
/* ==================== OneNET OTA 服务器基础配置 ==================== */
#define OTA_FUSE_HOST           "iot-api.heclouds.com" // OneNET FUSE 平台的 API 地址
#define OTA_HTTP_PORT           80                     // HTTP 默认端口号

/* ==================== 产品与设备身份信息 ==================== */
#define OTA_PRODUCT_ID          "XXX"           // 你的产品 ID
#define OTA_DEVICE_NAME         "d0"                   // 设备名称
//#define OTA_DEVICE_ID           "527776559"            // 设备 ID
#define OTA_CURRENT_VERSION     "V5.7"                 // 单片机当前运行的固件版本号（用于给云端对比，判定是否需要升级）

/* ==================== 鉴权与加密配置 (非常核心) ==================== */
//#define OTA_ACCESS_KEY_B64      "i62LG9nPQyMvk4cxqc3ndbmRT3PFWwENE5PTxV+"
#define OTA_ACCESS_KEY_B64      "xxx" // OneNET API 的访问密钥 (主账号级别)
#define OTA_AUTH_FUSE_VER       "2022-05-01"           // OneNET 规定的鉴权算法版本
#define OTA_AUTH_FUSE_RES_RAW   "userid/自己的账户IDXXXX"        // 资源路径 (鉴权必须指定的被访问资源)
#define OTA_AUTH_METHOD         "sha1"                 // 哈希签名方法：采用 SHA1
#define OTA_AUTH_UNIX_NOW_BASE  1774170000UL           // 初始的基础 UNIX 时间戳，如果本地时间不对，代码会自动从服务器返回的 HTTP Date 中截取并修正它
#define OTA_AUTH_ET_TTL_SEC     3600UL                 // 生成的鉴权 Token 有效期 (3600秒 = 1小时)

/* ==================== 下载策略与体验配置 ==================== */
#define OTA_DOWNLOAD_CHUNK_SIZE 1024U                  // 断点续传时的块大小 (1KB)。单片机每次只向云端 HTTP 拉取 1KB 固件，避免内存溢出
#define OTA_PROGRESS_STEP_PCT   10U                    // 进度条上报步长。每累计下载满 10% 才向云端发送一次进度报告，降低通信负担
```

access_key选择见[文档中心](https://iot.10086.cn/doc/aiot/fuse/detail/1464)