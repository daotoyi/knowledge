+++
title = "资源下载链接"
date = "2023-06-17 10:39:00"
lastmod = "2023-06-17 10:40:01"
categories = ["常识"]
draft = false
+++

## BT {#bt}

BT 是目前最热门的下载方式之一，它的全称为“BitTorrent”简称“BT”，中文全称“比特流”。

-   BT 服务器是通过一种传销的方式来实现文件共享的，它的工作原理。举个例子来说吧，例如 BT 服务器将一个文件分成了 N 个部分，有甲、乙、丙、丁四位用户同时下载，那么 BT 并不会完全从服务器下载这个文件的所有部分，而是根据实际情况有选择地从其他用户的机器中下载已下载完成的部分。
-   BT 下载的灵魂：种子．无论何种 BT 客户端程序，默认设置都未对下载速度和上传速度进行限制，这是因为 BT 软件会给上传速度较快的用户优先提供服务，也就是说上传速度越快，下载速度也越快。
-   网上的 BT 下载链接都是由广大用户自己发布提供的，这样使得下载资料非常广，不受常规管理人员的限制。


## 磁力链 {#磁力链}

简单地说，磁力链接是一种特殊链接，但是它与传统基于文件的位置或名称的普通链接不一样，它只是通过不同文件内容的 Hash 结果生成一个纯文本的“数字指纹”，并用它来识别文件。它类似于生活消费品包装上常见的条码，不同的是这个“数字指纹”可以被任何人从任何文件上生成，这也就注定了“磁力链接”不需要任何“中心机构”的支持（例如：BT Tracker 服务器），且识别准确度极高。因此任何人都可以生成一个 Magnet 链接并确保通过该链接下载的文件准确无误。

更简单地说：类似【magnet:?xt=urn:sha1:YNCKHTQCWBTRNJIV4WNAE52SJUQCZO5C】这样以“magnet:?”开头的字符串，就是一条“磁力链接”，其在网页上的图标像一块磁铁，很容易辨别。


## BT 协议 {#bt-协议}

-   其实是一个协议簇，BEP-3 是其基本协议内容，其他的大部分都是围绕这个来进行扩展或补充，区别于点对点（point-to-point），它是用户群对用户群（peer-to-peer）要想从 BT 网络中下载一个资源，必须具备以下部分：
-   种子文件（也就是我们常说的种子，后缀是 .torrent，本质上是一个由 bencode 编码的文本文件，其把资源分成很多虚拟块，并记录每个块的 hash 值，另外上面还记录着其他信息，比如文件大小、名字、Tracker 服务器等）
-   BT 客户端（需要有专门解析 BT 协议的程序，这样才能下载，比如迅雷，电驴）
-   Tracker 服务器 （记录着 peer 和种子相关信息，起着中心调控的作用）
-   下载时，BT 客户端首先解析种子文件得到 Tracker 地址，然后连接 Tracker 服务器。Tracker 服务器回应下载者的请求，提供下载者其他下载者（包括发布者）的 IP。下载者再连接其他下载者，根据种子文件，两者分别告知对方自己已经有的块，然后交换对方所没有的数据。此时不需要其他服务器参与，分散了单个线路上的数据流量，因此减轻了服务器负担。


## DHT 和磁力链 {#dht-和磁力链}

-   只有通过 Tracker 服务器我们才能得到其他 peers 的信息来下载，但这同时也成了 BT 协议的一个弱点，如果 Tracker 服务器挂掉了或者被封被屏蔽，整个网络也就瘫痪了。Distributed hash table, 简称 DHT，这个协议就是用来弥补这个弱点的。
-   DHT 由很多节点组成，每个节点保存一张表，表里边记录着自己的好友节点。当你向一个节点 A 查询另外一个节点 B 的信息的时候，A就会查询自己的好友表，如果里边包含 B，那么 A 就返回 B 的信息，否则 A 就返回距离 B 距离最近的 k 个节点。然后你再向这 k 个节点再次查询 B 的信息，这样循环一直到查询到 B 的信息，查询到 B 的信息后你应该向之前所有查询过的节点发个通知，告诉他们，你有 B 的信息。
-   从 2009 年开始，很多 BT 服务器被关（版权，限制级），不仅很多种子文件无法找到，Tracker 服务器也断开解析工作，使得 BT 下载成为很大难题，而磁力链接很好地解决了这个问题，它利用 BT 种子中记录的数字指纹通过 DHT 网络进行搜索，获取下载者列表，与其他下载者通讯。
-   “tracker 服务器”决定了 BT 下载者能够和哪些别的 BT 源建立联系、并从这些 BT 源进行下载。一般来讲，一个 BT 种子文件往往只和某一个 BT 网站的“tracker 服务器”关联起来，也就是说，该 BT 种子文件内置只有一个“tracker URL”。但事实上，同一个 BT 种子文件可能在多个网站被重复发布了，区别仅仅在于其内置的“tracker URL”不同。
-   磁力连结的原理，其实是利用每一个 BT 种子的特征码，来当作搜寻种子的基础。透过这个特征码，在 BT 的网路中將种子拉回来，后续还是利用传统的 BT 方式下载档案。

<https://baijiahao.baidu.com/s?id=1580507259311937491&wfr=spider&for=pc>