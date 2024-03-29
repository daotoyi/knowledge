---
title: "SNTP 对时原理"
date: "2023-09-24 18:57:00"
lastmod: "2023-09-24 22:08:38"
categories: ["电气"]
draft: false
---

目前国内 sntp 服务器一般要求精度在 ±50ms 量级，而且由于其受到网络复杂程度和网络环境限制较高，当网络环境较差且上级 sntp 对时服务器级别不高时很难保证对时精度。而国外工业及电力系统中则对 sntp 对时精度要求较高，其一般要求保护系统内 sntp 客户机系统时间以及外部 IO 信号报文时间戳相对基准时间误差不大于 ±1ms。


## sntp 对时原理 {#sntp-对时原理}

sntp 单播模式下，其以客户机／服务器模式进行通讯，对时服务器通过自身接收 GPS 信号及自身守时芯片确定自身的时间基准。客户机定期访问服务器获取服务器时间信息，并依此改变自身时钟至服务器时间，从而完成时间校准工作。

在时间同步过程的起始时刻，客户机向 sntp 服务器发送包含时间戳的对时请求数据并自身记录该时刻，服务器收到该请求后回复一包含接收到客户机请求包时刻和自身发出应答包时刻的数据包至客户机，客户机接收该包并记录接收时刻。

{{< figure src="https://raw.githubusercontent.com/daotoyi/picsbed/main/img/202309241854597.png" >}}

设 T1 为客户端向服务器端发出对时请求的自身绝对时刻，T2 为服务器端收到来自客户端对时请求包的绝对时刻，T3 为服务器端向客户端发出对时应答包的出口绝对时刻，T4 为客户端接收到服务器应答包的绝对时刻。

假设 1：请求数据包和服务器应答数据包在网络上的发收时间间隔一致（收发路由以及延时一致），则可算出单程网络延时。

假设 2：在一个时间同步周期内，客户机端内部时钟与服务器端时钟只存在小到可以在毫秒级忽略不记的误差。由以上两个假设可以推算出两边绝对时间偏差。

基于此，可以看出客户端同服务器端的时间偏差仅取决于请求报文与应答报文在网络上的延时与相关节点的绝对时间戳值。得到客户机端的绝对时间偏差后，客户端根据此偏差进行调整即可实现时间同步。


## sntp 对时过程中可能产生的外部误差 {#sntp-对时过程中可能产生的外部误差}

实际 sntp 时间同步处理过程中，由于期望达到 ±1ms 级误差，所以嵌入式系统的内部处理流程耗时将不能忽略。此时在 cpu 进行报文时间标记后转由端口发送至网络的过程中将产生一定的延时，同时当嵌入式系统端口处收到来自对时服务器的应答报文，cpu 将经过一段时间后才可能巡检到并处理该报文，此时由于巡检机制导致时间段的不稳定性，实际系统中的两组时间戳间隔（广义网络延时）将不再对等。此时通过 sntp 对时机制计算出的本地时钟相对服务器时间的偏移值会产生大小为到相对时间与到相对时间差值绝对值一半的误差。

此外，由 sntp 时钟源同步发出的脉冲信号输入保护设备后，DI 板件将该信号转发至 cpu 板卡处理形成上送 PC 端工具报文并标记该信号时间戳的过程中也会产生一定的延时，在对比实验中大约为 ±0.5ms 级，难以忽略。


## SNTP 服务器应用建议 {#sntp-服务器应用建议}

为了使 SNTP 服务器能够更好地在局域网中应用，尤其是在需要为大量网络设备（计算机，网络摄像机，服务器、交换机、监控设备等）需要时钟同步的情况下有以下建议：

1.  务必在本地局域网架设 SNTP 服务器，不要使用互联网上的 SNTP 服务器地址，因为 Internet 网络的时延不确定性，最大有几秒的误差，服务质量得不到保证。
2.  sntp 客户端对 sntp 服务器的授时请求周期一般要大于 60s，甚至更长时间，建议 5 分钟左右，以免造成 SNTP 服务器资源迅速消耗而不能及时响应其他客户端的请求。
3.  SNTP 服务器可以带的客户机数量是根据厂家 sntp 服务器的响应速度来确定的，网络中客户机数目过大时，应配置多台 SNTP 服务器，以达到要求的授时精度。一旦超过厂家规定的数量，授时的精确度就得不到保证，会有恶化的危险。
4.  需要高可靠授时应用时，最好配备多台 SNTP 服务器，实现冗余配置，利用 DNS 实现负载均衡和集群。
5.  计算机客户端一般能够自动识别服务器端的故障，一旦发现 sntp 服务器出现故障，会自动转向其他 sntp 服务器获取时间。