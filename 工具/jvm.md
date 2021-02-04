# JVM

Jvm调优



- 一、内存图

  ![img](/Users/liujiang/Documents/Typora/imgs/006tNc79ly1g3axlcq26xj30gp0asta1.jpg)

- 二、堆结构及分代

  - 堆内存是虚拟机管理的内存中最大的一块，也是垃圾回收最频繁的一块区域，我们程序的所有对象实例都放在堆内存中。

  - 分代是为了体改对象内存分配和垃圾回收的效率

  - 每个对象的生命周期都不一样，每次回收，都需要遍历，内存空间是连续的，每次回收产生碎片，浪费空间

  - 新生代

    - 新创建的对象
    - 新生代中的对象存活时间短，只需要在新生代区域中频繁进行GC

  - 老年代

    - 经过多次回收仍然存货下来的对象
    - 老年代中对象生命周期长，内存回收的频率相对较低，不需要频繁进行回收

  - 持久代（永久代）

    - 静态属性、类信息等
    - 永久代中回收效果太差，一般不进行GC

  - 根据不同年代的特点采用合适的垃圾回收算法，分代收集

  - 堆内存图

    ![img](/Users/liujiang/Documents/Typora/imgs/006tNc79ly1g3axlldiwrj30pa0bptb1.jpg)

  - young回收空间默认比例8：1：1

    ![img](/Users/liujiang/Documents/Typora/imgs/006tNc79ly1g3axlmuz74j30pa0fgjua.jpg)

    - 大部分都在eden中回收掉了，目的是让jvm采用复制算法回收新生代，设置比例为了充分利用内存空间，减少浪费，大对象直接放进老年代

    - 通过不断回收，看谁能进入老年代

      ![img](/Users/liujiang/Documents/Typora/imgs/006tNc79ly1g3axljippaj30hw0a8jzw.jpg)

- 三、垃圾回收算法及收集器

  - 引用计数

    - 比较古老的回收算法，原理是此对象有一个引用，即增加一个计数，删除一个引用则减少一个计数。垃圾回收时，只用收集计数为0的对象，此算法最致命的是无法处理循环引用的问题

  - 复制

    - 把内存空间划分两个相等的区域，每次只使用一个区域，垃圾回收时，遍历当前使用区域，把正在使用中的对象复制到另一个区域中。此算法每次只处理正在使用中的对象，因此复制成本较小，同时复制过去以后还能进行相应的内存整理，不会出现碎片问题。此算法的缺点是需要两倍的空间,from\to

    - 两个区域，左边是引用的，右边橘色是空的

      ![img](/Users/liujiang/Documents/Typora/imgs/006tNc79ly1g3axlmex5rj30hz06pdhm.jpg)

  - 标记-清除（mark-sweep）

    - 重点，第一阶段从引用根节点开始标记所有被引用的的对象，第二阶段遍历整个堆，把未标记的对象清除，此算法需要暂停整个应用，同时会产生内存碎片

    - 

      ![img](/Users/liujiang/Documents/Typora/imgs/006tNc79ly1g3axlkbm20j30iv06475w.jpg)

    - 

      ![img](/Users/liujiang/Documents/Typora/imgs/006tNc79ly1g3axli38foj30ic065jt3.jpg)

  - 标记-整理（mark-compact）

    - 此算法结合了标记清除和复制两个算法的优点，也是分两个阶段，第一阶段从根节点开始标记所有被引用对象，第二阶段遍历整个堆，把清除未标记对象并且存货对象压缩到堆的其中一块，按顺序排放，此算法避免了标记清除的碎片问题，同时也避免了赋值算法的空间问题

      ![img](/Users/liujiang/Documents/Typora/imgs/006tNc79ly1g3axlfqvktj30if0cyadv.jpg)

  - 还有很多算法...

  - 垃圾收集器

    - 垃圾回收算法的具体实现

      ![img](/Users/liujiang/Documents/Typora/imgs/006tNc79ly1g3axlcnnwhj30go04x77y.jpg)

      ![img](https://ws2.sinaimg.cn/large/006tNc79ly1g3axleblwcj30gg074774.jpg)

      ![img](/Users/liujiang/Documents/Typora/imgs/006tNc79ly1g3axlkxu74j30gn05ljt9.jpg)

    - 串行收集器

      ![img](/Users/liujiang/Documents/Typora/imgs/006tNc79ly1g3axlltrf0j30g8077mzf.jpg)

      ![img](https://ws4.sinaimg.cn/large/006tNc79ly1g3axles89dj30b300mglk.jpg)

    - 并行收集器

      ![img](https://ws2.sinaimg.cn/large/006tNc79ly1g3axlcwhxsj30g9097q6i.jpg)

    - Parallel Scavenge 收集器

      ![img](https://ws4.sinaimg.cn/large/006tNc79ly1g3axldukr7j30h70a6tdg.jpg)

    - 老年代收集器

      ![img](https://ws4.sinaimg.cn/large/006tNc79ly1g3axlh5hfrj30gz05zq4i.jpg)

    - Parallel Old收集器

      ![img](https://ws1.sinaimg.cn/large/006tNc79ly1g3axlg76ewj30gq01qq3p.jpg)

    - CMS收集器

      ![img](https://ws1.sinaimg.cn/large/006tNc79ly1g3axlf91pxj30gn07ogpn.jpg)

      ![img](https://ws3.sinaimg.cn/large/006tNc79ly1g3axlj0f7ij30gg032wfq.jpg)

      ![img](https://ws1.sinaimg.cn/large/006tNc79ly1g3axlhlhqpj30ha085adh.jpg)

      ![img](https://ws2.sinaimg.cn/large/006tNc79ly1g3axlikfe3j30g8050wi8.jpg)

      ![img](https://ws2.sinaimg.cn/large/006tNc79ly1g3axlgq974j30gh04hq6b.jpg)

    - 分区收集器 G1收集器

      ![img](https://ws2.sinaimg.cn/large/006tNc79ly1g3axldkck9j30gp0asta1.jpg)