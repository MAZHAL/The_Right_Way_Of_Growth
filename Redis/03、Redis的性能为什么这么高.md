# redis性能为什么这么高

-  完全基于内存  数据全部存储在内存中，读取没有磁盘IO 所以速度非常快

- Redis 采用单线程模型 没有上下文切换的开销 也没有竞态条件，不用考虑各种锁的问题 ，不存在加锁和释放锁操作  没有因为可能出现死锁而出现的性能消耗 

- Redis 项目中使用的数据结构是专门设计的 例如SDS(简单动态字符串) 是对C语言中的字符串频繁修改时，会频繁地进行内存分配，十分消耗性能，而SDS会使用空间预分配和惰性空间释放来避免这些问题的出现。 空间预分配技术: 对SDS进行修改时，如果修改后SDS实际使用长度为len，
         当len<1M时,分配的空间会是2*len+1，也就是会预留len长度的未使用空间，其中1存储空字符
         当len>1M时,分配的空间会是len+1+1M，也就是会预留1M长度的未使用空间，其中1存储空字符
         
- 采用多路复用IO模型  可以同时检测多个流的IO事件能力  在空闲时 会把当前进程阻塞掉      当有一个或者多个流有IO事件时 就从阻塞态唤醒 轮训那些真正发出了事件的流 并只依次顺序的处理就绪的流 可以让单个线程高效 的处理多个请求 （尽量减少网络IO的时间消耗）
