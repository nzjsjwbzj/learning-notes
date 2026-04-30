test
test2

# 基于 STM32F429 与 FreeRTOS 的高可靠性 IoT 网关

本项目是一个工业级/智能家居双场景适用的 IoT 网关全栈设备端系统。基于 STM32F429 核心，搭载 FreeRTOS 实时操作系统，实现多传感器数据采集、本地 UI 渲染、异常掉线离线缓存、OneNET 平台接入以及 FUSE 平台的 OTA 远程静默升级。

## 🎯 核心特性
- **稳定的多线程架构**：采用 FreeRTOS 进行任务解耦（传感器采集、UI 刷新、TCP/MQTT 网络、守护进程）。
- **位图机制软件看门狗**：设计了精确到任务级的状态监控，防止单一线程死锁导致整个网关瘫痪。
- **动态心跳与防掉线机制**：结合 Paho-MQTT 与 ATK-MW8266D，实现断网指数级退避重连、基于流量侦测的动态 PING 节能保活。
- **离线数据容灾存储**：断网期间传感器数据自动封装备份至外置 SPI Flash (W25Qxx)，待网络恢复后按 FIFO 执行历史账单回补上报。
- **HMAC-SHA1 加密与 OTA 升级**：支持云端固件下发，通过 HTTP Ranged Requests (分块下载)、MD5 完整性校验实现安全可靠的系统更新。

## 🗂 系统架构图

```mermaid
graph TD
    subgraph 硬件外设层
        S1[DHT11 温湿度]
        S2[AP3216C 光/距]
        S3[W25QXX SPI Flash]
        S4[LCD & LED/Beep]
        S5[ESP8266 Wi-Fi]
    end

    subgraph FreeRTOS 任务层
        T1[Sensor Task]
        T2[LCD UI Task]
        T3[Net Task 核心轮询]
        T4[Watchdog Task]
        
        Q1[(数据队列 Queue)]
        Q2[(MQTT 上报队列)]
    end

    subgraph 协议与业务网络层
        P1[动态心跳 & 重连状态机]
        P2[Paho MQTT 封包解包]
        P3[离线数据滞留/回补管理]
        P4[OTA HTTP引擎 & HMAC鉴权]
    end

    subgraph 云端
        C1[OneNET 物模型]
        C2[FUSE OTA 发版中心]
    end

    S1 & S2 --> T1
    T1 -->|实时数据| Q1
    T1 -->|上报数据| Q2
    Q1 --> T2 --> S4
    
    Q2 --> T3
    S3 <-->|断网缓存| T3
    T3 <--> P1 & P2 & P3 & P4 <--> S5 <--> C1 & C2
    
    T4 -.->|位图打卡监控| T1 & T2 & T3
```
