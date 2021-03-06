### 分布式缓存集群的访问模型

![集群访问模型](https://cdn.jsdelivr.net/gh/Winniekun/cloudImg@master/uPic/image-20210815194130631.png)

随着数据的缓存变多，开始搭建Redis集群。如上，搭建了一个由三组主从复制的Redis构成的高可用的Redis集群。那么，我们就需要考虑如何将请求路由的不同的redis集群上，常规的路由算法如下：

1. 轮询
   - 保证了请求的绝对平衡，但是应用到分布式缓存中，缺点也是明显的，为了提高缓存的命中率，同一份数据需要在每个集群中都存在。这样就造成了数据的冗余。
2. 随机
   - 和轮询差不多
3. hash算法
   - 根据数据中的某个字段作为分片键，然后先对其进行hash，得到hashcode，再进行求余计算得到需要访问的Redis节点

虽然hash算法避免数据冗余，但是无法做到很好的扩展性，当集群数量新增或者减少的时候，因为是求余计算，就会降低缓存的命中率，为了解决这种情况，方式有很多种，譬如引用Redis的rehash的思想，或者**一致性hash算法**

## 原理

一致性hash可以说是一种很好的缓存设计方案。我们假设根据数据的id通过hash函数转换成的hash值范围$[0, 2^{32} - 1]$。假设将这些数字头尾相连，就可以想象成一个闭合的环形。那么一个数据根据id得出hash值之后，即可通过求余获取所在环的位置。然后顺时针寻找离这个位置最近的节点。

![image-20210815215411166](https://cdn.jsdelivr.net/gh/Winniekun/cloudImg@master/uPic/image-20210815215411166.png)



假设有三个redis集群，步骤如下：

1. Data1 通过hash函数获取hash值 key1， 然后求余得到所在环上的位置，接着顺时针寻找最近的节点，得到`machine2`

### Hash环数据倾斜

![image-20210815215443548](https://cdn.jsdelivr.net/gh/Winniekun/cloudImg@master/uPic/image-20210815215443548.png)

在`Machine2`和`Machine3`之间新增一个节点`Machine4`，这样使得原先要访问`machine3`的缓存数据，现在需要访问`Machine4`。不过原先`Machine1`和`Machine2`不受影响，也就意味着`Machine1` 和`Machine2`缓存数据量和负载压力是`Machine3`与`Machine4`的两倍。

或者换种理解方式，整个hash环上，随着节点的新增和删除导致整体分布不均衡，结果就是不同的机器负载不同，出现了数据的倾斜。

### 虚拟节点

为了解决数据倾斜的问题，一致性hash算法引入了虚拟节点的机制，将每台物理缓存服务器虚拟为一组虚拟缓存服务器，使得 Hash 环在空间上的分割更加均匀。具体的思路就是**对每一台服务器通过hash函数计算出不同的多个hash值，对多个hash值计算出的位置放置一个服务节点**

![虚拟节点技术](https://cdn.jsdelivr.net/gh/Winniekun/cloudImg@master/uPic/image-20210815223351379.png)

这样只是将虚拟节点的 Hash 值放置在 Hash 环上，在查找时，首先根据 Key 值找到环上的虚拟节点，然后再根据虚拟节点找到真实的缓存服务器。虚拟节点的数目足够多，就会使得节点在 Hash 环上的分布更加随机化，也就是增加或者删除一台缓存服务器时，都会较为均匀的影响原来集群中已经存在的缓存服务器。

### 缺陷

虽然一致性 Hash 算法已经十分完善，但是还是有很多不足的地方

1. Hash 环上的节点非常多或者更新频繁时，查询效率比较低下，（可以类比于二叉搜索树）
2. 整个 Hash 环需要一个服务路由来做负载均衡，存在单点问题

针对这两个问题，Redis 在实现自己的分布式集群方案时，采用了基于 P2P 结构的哈希槽算法

1. 使用哈希槽
   1. Redis Cluster 通过分片的方式将整个缓存划分为 16384 个槽，每个缓存节点就相当于 Hash 换上的一个节点，接入集群时，所有实例都将均匀占有这些哈希槽，当需要查询一个 Key 是，首先根据 Key 的 hashcode 对 16384 取余来得到 Key 属于哪个槽，并映射到缓存实例上。
   2. 为了方便描述，将 16384 抽象为 20个哈希槽单位，在增加缓存实例时，假设原来有 4 个节点，分配的哈希槽为 0-4，5-9，10-14，15-19 现在增加一个节点，Redis Cluster 的做法是将之前每台服务器上的一部分哈希槽移动到第四个节点上，更新后的哈希槽分配为 1-4，6-9，11-14，16-19 第四个节点为 0，5，10，15。删除也是同理
2. 去中心化
   1. 每个节点都保存有完整的哈希槽-节点的映射表，也就是说，每个节点都知道自己拥有哪些哈希槽，以及某个确定的哈希槽究竟对应着哪个节点。
   2. 无论向哪个节点发出寻找 Key 的请求，该节点都会通过求余计算该 Key 究竟存在于哪个哈希槽，并将请求转发至该哈希槽所在的节点。

## 实现

* [ ] TODO

### 使用场景

在数据库存储领域如果单表数据量很大，通常会采用分库分表，在缓存领域同样需要分库，一致性哈希算法在面对分布式缓存有着得天独厚的优势，因为它的产生就是为了解决分布式缓存扩容、缩容带来的缓存穿透问题。但现在一致性在分布式服务调用的负载算法竟然也提供了一致性哈希算法的实现。**一致性哈希算法在这些领域无法发挥其优势，比轮循、加权轮循、随机、加权随机算法等负载均衡算法相比，实现复杂，性能低下，运维管理复杂**。因为在**服务调用**等负载均衡算法中，多次的服务调用之间关联性不太强，在服务端执行扩容、缩容后，对于客户端来说其实并不关心路由到哪一台服务器，其关心的是能够有一台服务进行处理即可。

## references

* [一致性哈希，原来这么多学问！](https://mp.weixin.qq.com/s/Ti2AA5AKXzt6Fnqn2pXiwg)
* [一致性hash负载均衡算法的讨论](https://www.cnkirito.moe/consistent-hash-lb/)

