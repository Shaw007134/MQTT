# What is MQTT?

### MQTT Introduction

##### 介绍

MQTT（Message Queuing Telemetry Transport） 是消息队列遥测传输的缩写，其是IBM开发的一个即时通讯协议，它是一种轻量级的、基于代理的“发布/订阅”模式的消息传输协议。



物联网(IoT)设备必须连接互联网。通过连接到互联网，设备就能相互协作，以及与后端服务协同工作。互联网的基础网络协议是TCP/IP。MQTT正是 基于 TCP/IP 协议栈而构建的，其已成为 IoT 通信的标准。



MQTT 最初由 IBM 于上世纪 90 年代晚期发明和开发。它最初的用途是将石油管道上的传感器与卫星相链接。顾名思义，它是一种支持在各方之间异步通信的消息协议。异步消息协议在空间和时间上将消息发送者与接收者分离，因此可以在不可靠的网络环境中进行扩展。虽然叫做消息队列遥测传输，但它与消息队列毫无关系，而是使用了一个发布和订阅的模型。在 2014 年末，它正式成为了一种 OASIS 开放标准，而且在一些流行的编程语言中受到支持（通过使用多种开源实现）。



##### 特点

为什么MQTT，不选择其他的通讯方式呢，例如HTTP Web服务，AMQP(高级消息排队协议)，XMPP(可扩展消息和状态协议)等。主要原因是在于IoT设备通信环境通常是带宽及计算资源受限，网络不可靠等，而MQTT协议轻量小巧，省流量，省电，灵活/扩展性强，致力于为 IoT 开发人员实现适当的平衡。

其遵循以下设计原则：

- 精简，不添加可有可无的功能；
- 发布/订阅（Pub/Sub）模式，提供一对多的消息发布，解除应用程序耦合。
- 允许用户动态创建主题，零运维成本；
- 把传输量降到最低以提高传输效率；
- 把低带宽、高延迟、不稳定的网络等因素考虑在内；
- 支持连续的会话控制；
- 理解客户端计算能力可能很低；
- 提供服务质量管理；
- 假设数据不可知，不强求传输数据的类型与格式，保持灵活性。

而譬如HTTP协议，其是一种同步且单向1-1重量级协议，客户必须发起连接并需要等待服务器响应，而在 IoT 领域，大量设备以及很可能不可靠或高延迟的网络使得同步通信成为问题。异步消息协议更适合 IoT 应用程序。传感器发送读数，让网络确定将其传送到目标设备和服务的最佳路线和时间。XMPP 一种对等即时消息 (IM) 协议，其高度依赖于支持 IM 用例的特性，比如存在状态和介质连接。使用XML格式文本传输数据，与 MQTT 相比，它在设备和网络上需要的资源都要多得多。

那为什么MQTT如此轻量且灵活呢？MQTT 协议的一个关键特性是发布和订阅模型。与所有消息协议一样，它将数据的发布者与使用者分离。

##### 发布和订阅模型

![image1](.\image1.png)

MQTT 协议在网络中定义了两种实体类型：一个消息代理和一些客户端。代理是一个服务器，它从客户端接收所有消息，然后将这些消息路由到相关的目标客户端。客户端是能够与代理交互来发送和接收消息的任何事物。客户端可以是现场的 IoT 传感器，或者是处理 IoT 数据的应用程序。

1. 客户端连接到代理。它可以订阅代理中的任何消息 “主题”。此连接可以是简单的 TCP/IP 连接，也可以是用于发送敏感消息的加密 TLS 连接。
2. 客户端通过将消息和主题发送给代理，发布某个主题范围内的消息。
3. 代理然后将消息转发给所有订阅该主题的客户端。

上图示例演示了MQTT的常见运用场景：

传感器将在 “sensor_data” 主题范围内发布读数，并订阅 “config_change” 主题。将传感器数据保存到后端数据库中的数据处理应用程序会订阅 “sensor_data” 主题。管理控制台应用程序能接收系统管理员的命令来调整传感器的配置，比如灵敏度和采样频率，并将这些更改发布到 “config_change” 主题。

##### 协议简介

通讯过程中，MQTT协议中有三种身份：发布者（Publish）、代理（Broker）（服务器）、订阅者（Subscribe）。其中，消息的发布者和订阅者都是客户端，消息代理是服务器，消息发布者可以同时是订阅者。

- 订阅（Subscription）

  订阅包含主题筛选器（Topic Filter）和最大服务质量（QoS）。订阅会与一个会话（Session）关联。一个会话可以包含多个订阅。每一个会话中的每个订阅都有一个不同的主题筛选器。

- 会话（Session）

  每个客户端与服务器建立连接后就是一个会话，客户端和服务器之间有状态交互。会话存在于一个网络之间，也可能在客户端和服务器之间跨越多个连续的网络连接。

- 主题名（Topic Name）

  连接到一个应用程序消息的标签，该标签与服务器的订阅相匹配。服务器会将消息发送给订阅所匹配标签的每个客户端。

- 主题筛选器（Topic Filter）

  一个对主题名通配符筛选器，在订阅表达式中使用，表示订阅所匹配到的多个主题。

- 负载（Payload）

  消息订阅者所具体接收的内容。

MQTT会构建底层网络传输：它将建立客户端到服务器的连接，提供两者之间的一个有序的、无损的、基于字节流的双向传输。

当应用数据通过MQTT网络发送时，MQTT会把与之相关的服务质量（QoS）和主题名（Topic）相关连。

###### MQTT客户端

一个使用MQTT协议的应用程序或者设备，它总是建立到服务器的网络连接。客户端可以：

- 发布其他客户端可能会订阅的信息；
- 订阅其它客户端发布的消息；
- 退订或删除应用程序的消息；
- 断开与服务器连接。

###### MQTT服务端

MQTT服务器以称为"消息代理"（Broker），可以是一个应用程序或一台设备。它是位于消息发布者和订阅者之间，它可以：

- 接受来自客户的网络连接；
- 接受客户发布的应用信息；
- 处理来自客户端的订阅和退订请求；
- 向订阅的客户转发应用程序消息。

MQTT提供了以下方法，用来实现上述的功能，具体客户端方法实现由不同的第三方库编写，所有 MQTT 库和工具都提供了直接处理这些消息的简单方法，并能自动填充一些必需的字段，比如消息和客户端 ID。

- Connect。等待与服务器建立连接。
- Disconnect。等待MQTT客户端完成所做的工作，并与服务器断开TCP/IP会话。
- Subscribe。等待完成订阅。
- UnSubscribe。等待服务器取消客户端的一个或多个topics订阅。
- Publish。MQTT客户端发送消息请求，发送完成后返回应用程序线程。

###### 协议

数据包组成：

- 固定头（Fixed header）。存在于所有MQTT数据包中，表示数据包类型及数据包的分组类标识如DUP、QoS、RETAIN，变长头部长度和消息体总大小等。

  ```
  QoS: 发布消息的服务质量，即：保证消息传递的次数
  00：最多一次，即：<=1
  01：至少一次，即：>=1
  10：一次，即：=1
  11：预留
  ```

- 可变头（Variable header）。存在于部分MQTT数据包中，数据包类型决定了可变头是否存在及其具体内容，通常用作包的标识，如。

- 消息体（Payload）。存在于部分MQTT数据包中，表示客户端收到的具体内容。

  分为CONNECT/SUBSCRIBE/SUBACK/UNSUBSCRIBE

  客户端发送一条 CONNECT 消息来连接代理。CONNECT 消息要求建立从客户端到代理的连接。CONNECT 消息包含以下内容参数。

  表 1. CONNECT 消息参数

| **参数**        | **说明**                                                     |
| --------------- | ------------------------------------------------------------ |
| cleanSession    | 此标志指定连接是否是持久性的。持久会话会将所有订阅和可能丢失的消息（具体取决于 QoS） 都存储在代理中。（请参阅 表 3 获取 QoS 的描述。） |
| username        | 代理的身份验证和授权凭证。                                   |
| password        | 代理的身份验证和授权凭证。                                   |
| lastWillTopic   | 连接意外中断时，代理会自动向某个主题发送一条 “last will” 消息。 |
| lastWillQos     | “last will” 消息的 QoS。（请参阅 表 3 来查看 QoS 的描述。）  |
| lastWillMessage | “last will” 消息本身。                                       |
| keepAlive       | 这是客户端通过 ping 代理来保持连接有效所需的时间间隔。       |

客户端收到来自代理的一条 CONNACK 消息。CONNACK 消息包含以下内容参数。

表 2. CONNACK 消息参数

| **参数**       | **说明**                                                     |
| -------------- | ------------------------------------------------------------ |
| sessionPresent | 此参数表明连接是否已有一个持久会话。也就是说，连接已订阅了主题，而且会接收丢失的消息。 |
| returnCode     | 0 表示成功。其他值指出了失败的原因。                         |

建立连接后，客户端然后会向代理发送一条或多条 SUBSCRIBE 消息，表明它会从代理接收针对某些主题的消息。消息可以包含一个或多个重复的参数。如表 3。

表 3. SUBSCRIBE 消息参数

| **参数** | **说明**                                                     |
| -------- | ------------------------------------------------------------ |
| qos      | qos（服务质量或 QoS）标志表明此主题范围内的消息传送到客户端所需的一致程度。 值 0：不可靠，消息基本上仅传送一次，如果当时客户端不可用，则会丢失该消息。值 1：消息应传送至少 1 次。值 2：消息仅传送一次。 |
| topic    | 要订阅的主题。一个主题可以有多个级别，级别之间用斜杠字符分隔。例如，“dw/demo” 和 “ibm/bluemix/mqtt” 是有效的主题。 |

客户端成功订阅某个主题后，代理会返回一条 SUBACK 消息，其中包含一个或多个 returnCode 参数。

表 4. SUBACK 消息参数

| **参数**   | **说明**                                                     |
| ---------- | ------------------------------------------------------------ |
| returnCode | SUBCRIBE 命令中的每个主题都有一个返回代码。返回值如下所示。 值 0 - 2：成功达到相应的 QoS 级别。（参阅 表 3 进一步了解 QoS。）值 128：失败。 |

与 SUBSCRIBE 消息对应，客户端也可以通过 UNSUBSCRIBE 消息取消订阅一个或多个主题。

表 5. UNSUBSCRIBE 消息参数

| **参数** | **说明**                   |
| -------- | -------------------------- |
| topic    | 此参数可重复用于多个主题。 |

客户端可向代理发送 PUBLISH 消息。该消息包含一个主题和数据有效负载。代理然后将消息转发给所有订阅该主题的客户端。

表 6. PUBLISH 消息参数

| **参数**   | **说明**                                                     |
| ---------- | ------------------------------------------------------------ |
| topicName  | 发布的消息的相关主题。                                       |
| qos        | 消息传递的服务质量水平。（请参阅 表 3 来查看 QoS 的描述。）  |
| retainFlag | 此标志表明代理是否保留该消息作为针对此主题的最后一条已知消息。 |
| payload    | 消息中的实际数据。它可以是文本字符串或二进制大对象数据。     |

##### 参考资料：

[初识 MQTT](https://www.ibm.com/developerworks/cn/iot/iot-mqtt-why-good-for-iot/index.html)