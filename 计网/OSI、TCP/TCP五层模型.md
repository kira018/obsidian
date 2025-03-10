# tcp概念
进程之间的通信需要进行网络传输，也会涉及分布式事务的问题，分布式事务中就有跨jvm导致的分布式事务，如果是同属于一个jvm则不需要进行网络通信。

## 应用层
对于我们来说能接触到的东西比如，电脑、手机上的软件。
当应用于应用之前需要通信，就会把数据传输到下一层，就是传输层。
## 传输层
传输层负责给应用层提供通信。
其中有俩个传输协议Tcp和Udp。
Tcp和UDP区别：
tcp安全，保证数据能够成功发送，支持超时重传，流量控制等功能。
而UDP只保证数据发送，而不保证数据能功成功抵达，相对的UDP性能就更好，实时性更高。
当传输的数据超过mss（TCP最大报文长度）的时候就会讲传输的数据分段，**这样做的目的是为了防止当网络出现波动之后，导致整个报文丢失而需要重新传输，分包之后只需要重新传输丢掉的那个分段就可以了**
## ## 网络层

给你个场景：
你是`配送员`，传输层是`用户`告诉你起点A和终点B，而你配送需要`地图`来规划路线才能完成最终的配送。
传输层告诉你起点和终点，比如计算机A需要跟计算机B进行通信，所以起点和终点已经知道了。
剩下的就是路线哪里来？这个时候就需要`地图`也就是`网络层`，网络层就是用来规划路线，并且计算一个最优路线。
>路线哪里来？最优的路线是怎么做到的？

如果你只知道一个起点和终点，你确实可以做到从很多路线中进行`配送`或者说`寻址`，但是寻址的效率呢？全国这么多ip地址，怎么才能让你更快的寻址呢？实现更快寻址这个目的网络层做了什么？

**网络层把ip分为网络号和主机这俩个东西**，网络号可以理解为城市，主机可以理解为详细地址精确到门牌号。这样就可以通过网络号先筛选一大部分范围，在通过主机在这个范围内精确查找。

> 主机和网络号怎么计算

网络号=IP**按位与**子网掩码
主机=IP**按位与**子网掩码的反码
两台设备并不是用一条网线连接起来的，而是通过很多网关、路由器、交换机等众多网络设备连接起来的，那么就会形成很多条网络的路径，你可以理解为现在你知道了最优路线，但是你不知道具体往哪个**方向**走。

如图所示 ：
![image.png](https://raw.githubusercontent.com/kira018/img/main/202502131200332.png)

对于人类来说一眼就知道往下走三格再往左走俩格就可以让A跟B进行连接。
但是计算机不知道，所以需要路由算法来进行路由指明方向。
这就是网络层最后一个作用。

------------------------------------
**总结一下**
网络层通过IP告诉计算机怎么进行寻址并且以速度最快的方式找到传输对象的具体位置，并且通过路由算法来确定自己该往哪个方向才能按照最优路线来走。
网络层俩要素：
- **`IP`**：IP用来告诉计算机寻址路径，并且是以最效率的方式来进行寻址
- **`路由`**：路由是告诉计算机这个路线该用什么方向才能到达，是上还是下。

寻址之后找到对应的交换机或者路由器，在这个环境下面
> 以太网寻址问题

现在只通过ip进行寻址还不够，一个主机可能会被多个计算机使用，比如一个路由器有多个计算机连接，只根据ip寻址只能寻址到路由器，也就是你的门牌号，门里面有多个计算机没法寻址到具体的计算机。
对于以太网环境下要怎么进行访问呢？
这个就是网络接口层做的事情了。
## 网络接口层
网络接口层通过使用MAC头部解决以太网寻址问题的，他包含接收方和发送方的MAC地址信息。
每个电脑都有自己的一个MAC地址，传输的时候就可以通过MAC头部来获取对方MAC地址进行传输。
通过这种方式可以做到在某个以太网内根据MAC地址来精准到门里的某个计算机
> MAC 发送方和接收方如何确认?

**发送方**的 MAC 地址获取就比较简单了，MAC 地址是在网卡生产时写入到 ROM 里的，只要将这个值读取出来写入到 MAC 头部就可以了。
**接收方**的 MAC 地址需要 `ARP` 协议帮我们找到路由器的 MAC 地址。
ARP 协议会在以太网中以**广播**的形式，对以太网所有的设备喊出：“这个 IP 地址是谁的？请把你的 MAC 地址告诉我”。

然后就会有人回答：“这个 IP 地址是我的，我的 MAC 地址是 XXXX”。

如果对方和自己处于同一个子网中，那么通过上面的操作就可以得到对方的 MAC 地址。然后，我们将这个 MAC 地址写入 MAC 头部，MAC 头部就完成了。
> 好像每次都要广播获取，这不是很麻烦吗？

放心，在后续操作系统会把本次查询结果放到一块叫做 **ARP 缓存**的内存空间留着以后用，不过缓存的时间就几分钟。

也就是说，在发包时：

- 先查询 ARP 缓存，如果其中已经保存了对方的 MAC 地址，就不需要发送 ARP 查询，直接使用 ARP 缓存中的地址。
- 而当 ARP 缓存中不存在对方 MAC 地址时，则发送 ARP 广播查询。
 再给大家贴一下每一层的封装格式：

![img](https://cdn.xiaolincoding.com/gh/xiaolincoder/ImageHost3@main/%E6%93%8D%E4%BD%9C%E7%B3%BB%E7%BB%9F/%E6%B5%AE%E7%82%B9/%E5%B0%81%E8%A3%85.png)

网络接口层的传输单位是帧（frame），IP 层的传输单位是包（packet），TCP 层的传输单位是段（segment），HTTP 的传输单位则是消息或报文（message）。但这些名词并没有什么本质的区分，可以统称为数据包。
# 键入网址到网页显示，这期间发生了什么
