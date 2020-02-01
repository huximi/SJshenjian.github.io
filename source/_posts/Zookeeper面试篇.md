title: Zookeeper面试篇
date: 2020-01-31 17:23:25
category: [JAVA]
tag: [JAVA,面试]
---

### 12.1.0 zookeeper是什么？  

`A high-performance coordination service for distributed applications`  
Zookeeper是基于Google Chubby论文的开源实现，它主要用于解决分布式应用中经常遇到的数据管理问题，
如统一命名服务、状态同步管理、集群管理、配置管理等。由于Hadoop生态系统中有许多项目都依赖于zookeeper，
如Hive、HBase，似乎像一个动物管理员，故取名为zookeeper  

### 12.1.1 zookeeper提供了什么？  

1、文件系统 2、通知机制  

### 12.1.2 zookeeper文件系统  

zookeeper提供一个类似unix文件系统目录的多层级节点命名空间（节点称为znode）。与文件系统不同的是，这些节点都可以设置关联的数据，
而文件系统中只有文件节点可以存放数据而目录节点不行。  
zookeeper为了保证高吞吐和低延迟，在内存中维护了这个树状的目录结构，这种特性使得zookeeper不能用于存放大量的数据，
每个节点的存放数据上限为1M。  

### 12.1.3 zookeeper的四种类型的znode

PERSISTENT 持久化节点  
PERSISTENT_SEQUENTIAL 顺序自动编号持久化节点，这种节点会根据当前已存在的节点数自动加1  
EPHEMERAL 临时节点，客户端session超时这类节点会被自动删除  
EPHEMERAL_SEQUENTIAL 临时自动编号节点   

### 12.1.4 zookeeper通知机制  

client会对某个znode建立一个watcher事件，当znode发生变化时，zk会主动通知watch这个znode的所有client,client根据znode的变化来做业务上的改变。  

**watcher的特点**  
+ 轻量级：一个callback()函数   
+ 异步性：不会block正常的读写  
+ 主动推送：watcher被触发时，zk服务端主动将更新推送给客户端
+ 一次性：数据变化时，watcher只会被触发一次，如果客户端想要得到后续更新通知，需要在watcher触发后重新注册一个watcher.
+ 仅通知：仅通知变更类型，不附带变更后的结果
+ 顺序性：如果多个更新触发了多个watcher,那么watcher被触发的顺序与更新一致  

**使用watcher注意事项**
+ 由于watcher是一次性的，所以需要自己去实现永久watcher
+ 如果被watch的节点频繁更新，会出现“丢数据”的情况
+ watcher数量过多会导致性能下降  

### 12.1.5 zookeeper有哪些应用场景

1. 统一命名服务  
2. 配置管理  
3. 集群管理  
4. 分布式锁  
5. 队列管理  
6. 消息订阅  

### 12.1.6 zk命名服务

命名服务是指通过指定的名字来获取资源或者服务的地址。利用zk来创建一个全局的路径，即唯一的路径，这个路径就可以作为一个名字，指向集群中的集群、提供服务的地址或者一个远程对象等  

### 12.1.7 zk配置管理

程序分布式的部署在不同机器上，将程序的配置信息放在zk的znode上，当有配置发生改变时，也就是znode发生了改变，利用watcher通知给各个watch该znode的客户端，从而实现配置更改

### 12.1.8 zk集群管理

集群管理在于两点：机器加入和退出、选举master  
对于第一点，所有机器约定在父目录下创建临时节点，然后监听父目录下所有子节点的变化消息。一旦有机器挂掉，该机器与zookeeper服务器断开连接，其所创建的EPHEMERAL(临时节点)被删除，所有的机器收到通知:
某个兄弟目录被删除，新加机器也类似  
对于第二点，我们稍微改变下，所有机器创建EPHEMERAL_SEQUENTIAL(临时顺序编号节点)，每次选取编号最小的节点作为master  

### 12.1.9 zk分布式锁

有了zookeeper的一致性文件系统，锁的问题变得简单。锁服务可以分为两类：一类是保持独占，另一类是控制时序。  
第一类，我们将zookeeper上的一个znode看做是一把锁，通过createznode的方式实现。所有客户端都去创建/distribute_lock节点，成功创建的客户端也即拥有了这把锁，
用完删除掉distribute_lock节点即释放锁    
第二类，/distribute_lock节点已经预先存在，所有客户端在它下面创建临时顺序编号节点,和选master一样，编号最小的获得锁，用完删除，依次获取。  

**获取分布式锁的流程**  
在获取分布式锁的时候在locker节点下创建临时顺序节点，释放锁的时候删除该节点。    
1.客户端调用createznode节点在locker下创建临时顺序节点  
2.调用getChild("locker")获取locker下的所有子节点  
   2.1 如果发现自己创建的节点在所有的子节点中序号最小，则认为客户端获取到了锁  
   2.2 否则，说明自己还没有获取到锁，此时客户端需要找到比它小的那个节点，然后对其调用exist()方法，同时对其注册事件监听器。
   之后，被watch的节点删除后，则客户端的watcher会收到相应通知，此时再次判断自己创建的节点是否是locker子节点中最小，如果是，则获取到了锁，如果不是则重复以上步骤继续获取比
   自己小的那个节点并注册监听。  
   
 # TODO 插入图片

代码的实现主要基于互斥锁，获取分布式锁的重点逻辑在于BaseDistributeLock,实现了基于Zookeeper实现分布式锁的细节

### 12.2.0 zk队列管理

两种类型的队列。  
1. 同步队列。当一个队列的所有成员都聚齐时，这个队列才可用，否则等待所有成员到达  
实现：在约定目录下创建临时目录节点，监听节点数量是否是我们要求的数量  
2. 队列按照FIFO方式入队与出队。  
实现：在特定的目录下创建PERSISTENT_SEQUENTIAL节点，创建成功时watcher通知等待的队列，队列删除序号最小的节点用于消费。  
Zookeeper的znode用于消息存储，znode存储的数据就是消息队列中的消息内容，SEQENTIAL序号就是消息的编号，按序取出即可。  
由于创建的节点是持久的，所以不必担心队列消息的丢失问题

### 12.2.1 zk数据复制

Zookeeper做为一个集群提供一致的数据服务，自然，它要在所有机器间做数据复制  
数据复制的好处：  
1. 容错： 一个节点出错，不致于整个系统停止工作，别的节点可以接管它的工作  
2. 提高系统的扩展能力：把负载分布到多个节点或者增加节点提高系统的负载能力  
3. 提高性能： 让客户端本地访问就近节点，提高用户访问速度  

从客户端读写访问的透明度来说，数据复制集群系统分为以下两种：  
1. 写主(Write Master): 对数据的修改提高给指定的节点，读无此限制，可以读取任何一个节点。这种情况下客户端需要对读写进行区分，俗称读写分离。  
2. 写任意(Write Any): 对数据的修改可提交给任意节点，跟读一样。这种情况下，客户端对集群节点的角色与变化透明  

对zookeeper而言，它采用的方式是写任意。通过增加机器，它的读吞吐能力和响应能力扩展性非常好，而写，随着机器的增加吞吐能力肯定下降(这也是它建立observer的原因)，  
而响应能力，则取决于具体的实现方式，是延迟复制保持最终一致性，还是立即复制快速响应  

### 12.2.2 zk中zab的工作原理

ZAB是Zookeeper Atomic Broadcast(Zookeeper原子广播协议)的缩写，它是特别为Zookeeper设计的崩溃可恢复的原子消息广播算法。  
Zookeeper使用Leader来接收并处理所有事务请求，并采用ZAB协议，将服务器数据的状态变更以事务Proposal的形式广播到所有Follower服务器上。这种主备模型架构保证了同一时刻集群中只有一个服务器
广播服务器的状态变更，因此能够很好的保证事务的完整性与顺序性。  

ZAB协议有两种模式：恢复模式(Recovery)和广播模式(Broadcast)。当服务启动或者leader崩溃后，ZAB就进入了恢复模式，当leader选举出来并且大多数follower完成了与leader的状态同步以后，
恢复模式就结束了，ZAB开始进入广播模式。

### 12.2.3 zk是如何保证事务的顺序一致性的？

zookeeper采用了递增的事务id来标识。所有的proposal(提议)在提出的时候加上了zxid,zxid实际上是一个64位的数字。  
高32位是epoch,用来标识leader是否改变，如果有新的leader产生出来，epoch会自增；  
低32位用来递增计数。当新产生proposal时候，会依据数据库的两阶段过程，首先向其他的server发出事务执行请求，如果超过半数的机器都能执行并且能够成功，
那么就会开始执行。

### 12.2.4 zk集群下server工作状态

每个Server工作状态中有四种状态  
+ LOOKING: 当前server不知道谁是leader,正在搜寻
+ LEADER: 当前server角色是leader
+ FOLLOWING: 当前server角色是follower
+ OBSERVING: 当前server角色是observer

### 12.2.5 zk是如何选举leader的？

当leader崩溃或者leader失去大多数follower时，这时zk进入恢复模式，恢复模式需要重新选举出一个新的leader,让所有的server都恢复到一个正确的状态。zk的选举算法有两种：
一种是基于basic paxos算法实现的，一种是基于fast paxos算法实现，系统默认的选举算法是fast paxos.  
**basic paxos**  
（1） 选举线程由当前server发起选举的线程担任，其主要功能是对投票结果统计，并选出推荐的server  
（2） 选举线程首先向所有server发起一次询问(包括自己)  
（3） 选举线程收到回复后，验证是否是自己发起的询问(验证zxid是否一致),然后获取对方的id(myid),并存储到当前询问对象列表中，最后获取对方提议的leader
相关信息(id,zxid),并将这些信息存储到当次选举的投票记录表中  
（4） 收到所有server回复以后，就计算出zxid最大的那个server,并将这个server相关信息设置成下一次要投票的server  
（5） 线程线程将当前zxid最大的server设置为当前server要推荐的leader，如果此时获胜的server获得n/2+1的server票数，设置当前推荐的leader为获胜的server,
将根据获胜的server相关信息设置自己的状态，否则，继续这个过程，知道leader被选举出来

**fast paxos**    
fast paxos流程是在选举过程中，某server向所有server提议自己要成为leader,当其他server收到提议后，解决epoch和zxid的冲突，并接受对方的提议，然后向对方发送接受提议完成的消息，
重复这个流程，最后一定能选举出leader

# TODO 图片

### 12.2.6 zk同步流程

选完leader以后，zk就进入状态同步状态  
1) leader等待follower和observer连接  
2) follower连接leader,将最大的zxid发送给leader  
3) leader根据follower的zxid确定同步点  
4) 完成通知后通知follower已经成为uptodate状态  
5) follower收到uptodate消息后，又可以接收client的请求进行服务了  

**数据同步的4中方式**    
1) SNAP-全量同步  
+ 条件： peerLastZxid < minCommittedLog  
+ 说明： 证明二者数据差异太大，follower数据过于陈旧，leader发送SNAP指令给follower全量同步数据，即leader将所有数据全量发送给follower  
2) DIFF-增量同步  
+ 条件： minCommittedLog <= peerLastZxid <= maxCommittedLog  
+ 说明： 证明二者数据差异不大，follower上有一些leader上已经提交的提议proposal未同步，此时需要增量提交这些提议即可  
3) TRUNC-仅回滚同步  
+ 条件： peerLastZxid > maxCommittedLog  
+ 说明： 证明follower上有些提议proposal未在leader上提交，follower需要回滚zxid为maxCommittedLog对应的事务操作  
4) TRUNC-DIFF-回滚+增量同步  
+ 条件： minCommittedLog <= peerLastZxid <= maxCommittedLog
+ 说明： leader a已经将事务truncA提交到本地事务日志中，然后还未成功发起proposal协议进行投票就宕机了；然后集群中剔除leader a重新选举出leader b, 
新leader b提交了若干新的提议proposal，若原有leader a恢复后重新加入集群，需要先回滚truncA,然后增量同步leader b上的数据。  

### 12.2.7 分布式通知与协调  
对于系统调度来说：操作人员发送通知实际是通过控制台改变某个节点的状态，然后zk将这些变化发送给注册了这个节点watcher的所有客户端  
对于执行情况汇报：每个工作进程都在某个目录节点下创建一个临时节点，并携带工作的进度数据，这样汇总进程通过监控某个节点的变化来获取工作进度的实时的全局情况  

###  12.2.8 zk的session机制
zookeeper会为每个客户端分配一个session,类似于web服务器一样，用来标识客户端的身份  
**session的作用：**   
+ 客户端标识
+ 超时检查
+ 请求的顺序执行
+ 维护临时节点的生命周期
+ watcher通知    
 
**session的状态：**  
+ CONNECTING
+ CONNECTED
+ RECONNECTING
+ RECONNECTED
+ CLOSED

**session的属性：**    
+ SessionID: 会话ID，全局唯一
    - 高8位表示创建session时所在zk节点的id
    - 中间40位表示zk节点当前角色创建时的时间戳
    - 低16位是一个计数器，初始值为0
+ TimeOut: 会话超时时间
+ TickTime: 下次会话超时时间点
+ isClosing: 会话是否已经被关闭
