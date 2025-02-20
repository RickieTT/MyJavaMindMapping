# java知识体系

## 基础知识

### 面向对象和过程的区别

- 面向对象

  - 概念

    - 会先抽象出对象，再用对象执行方法的方式去解决

  - 特点

    - 易维护

      - 封装

    - 易复用

      - 继承

        - 子类拥有父类对象所有的属性和方法（包括私有属性和私有方法），但是父类中的私有属性和方法子类是无法访问，只是拥有。
        - 子类可以拥有自己属性和方法，即子类可以对父类进行扩展。
        - 子类可以用自己的方式实现父类的方法。

      - 多态

        - 对象类型和引用类型之间具有继承（类）/实现（接口）的关系；
        - 引用类型变量发出的方法调用的到底是哪个类中的方法，必须在程序运行期间才能确定；
        - 多态不能调用“只在子类存在但在父类不存在”的方法；
        - 如果子类重写了父类的方法，真正执行的是子类重写的方法，如果子类没有重写父类的方法，执行的是父类的方法。

    - 可扩展

      - 由于模块化的设计，使得代码方便扩展

- 面向过程

  - 概念

    - 将解决过程拆分成一个一个方法，通过方法的执行从而解决问题

### 接口和抽象类

- 继承和实现：一个类只能继承一个类（包括抽象类），因为 Java 不支持多继承。但一个类可以实现多个接口，一个接口也可以继承多个其他接口。
- 成员变量：接口中的成员变量只能是 public static final 类型的，不能被修改且必须有初始值。抽象类的成员变量可以有任何修饰符（private, protected, public），可以在子类中被重新定义或赋值。
- java8及以上 接口新增

  - default

    - 用于提供接口方法的默认实现，可以在实现类中被覆盖。这样就可以在不修改实现类的情况下向现有接口添加新功能，从而增强接口的扩展性和向后兼容性。

  - static
  - private

### 拷贝

- 浅拷贝

  - 堆中创建新的对象，但是指向同一个地址

- 深拷贝

  - 堆中创建不同对象，但是指向不同的地址

- 引用拷贝

  - 创建另一个引用，但是指向的是同一个堆中地址

## 异常

### 异常种类

- Exception

  - Checked Exception

    - 如果受检查异常没有被catch 或者throws处理的话 就没办法通过编译
    - 除了RuntimeException及其子类外，其他的Exception都属于受检查异常

  - Unchecked Exception

    - 不受检查异常，在java代码编译的过程中，即使不处理，不受检查异常，也可以正常通过编译

      - Runtime Exception

        - NullPointerException
        - IllegalArgumentException
        - NumberFormatException
        - ArrayIndexOutOfBoundsException
        - ClassCastException
        - ArithmeticException
        - SecurityException
        - UnsupportedOperationException
        - 。。。

- Error

### 捕获异常

- try-catch-finally

  - 
  - finally

    - 不要在finally中用return，否则会忽略try中的return
      如果try语句里有return，返回的是try语句块中变量值。 
      a.如果有返回值，就把返回值保存到局部变量中；
      b.执行jsr指令跳到finally语句里执行；
      c.执行完finally语句后，返回之前保存在局部变量表里的值。
      **如果try，finally语句里均有return，忽略try的return，而使用finally的return.
    - finally的代码不一定会执行

      - 在finally之前被虚拟机禁止运行，就会出现异常
        比如在try中抛出异常
      - 要执行finally的线程突然挂掉

## 集合

### List

- 线程安全

  - Vector
  - CopyOnWriteArrayList

    - checkForComodification

      - 会上Sychronized锁（也是因为锁机制相对于1.6发生了改变）
      - 利用写时复制的思想，在添加时会生成一个新的List副本，对这个副本进行增删，再指向新的副本地址

        - 体现出了 fail-safe 安全失败 这样的机制

- 线程不安全

  - ArrayList

    - 底层类型为数组

      - checkForComodification

        - modCount和expectedModCount
        - [迭代器中 有 expectedModCount 参数，在增强for循环中，会先让expectedModCount = modCount。如果在增强for循环中，有进行增删的操作，modCount就会加一，就会使得checkForComodification中判断expected和modCount不相等，然后出现异常](https://blog.csdn.net/weixin_43636205/article/details/130551559)

        - 在增删等方法前 都会去做这样一个判断
        - 体现了 fail-fast（快速失败）的机制

      - 到10扩张

  - LinkedList

    - 底层类型为链表

### Set

- 无序，不可重复，无序是因为hash函数

  - 线程安全

    - CopyOnWriteArraySet

  - 线程不安全

    - HashSet

      - 底层HashMap

    - LinkedHashSet

      - 底层时HashMap 并且通过双向链表的方式维护插入的顺序

    - TreeSet

      - 红黑树

### Map

- 线程安全

  - HashTable

    - 1.8的结构与1.7相似

  - ConCurrentHashMap

    - 1.7

      - Segment + ReentrantLock

        - Segment 的结构和 HashMap 类似，是一种数组和链表结构，一个 Segment 包含一个 HashEntry 数组，每个 HashEntry 是一个链表结构的元素，每个 Segment 守护着一个 HashEntry 数组里的元素，当对 HashEntry 数组的数据进行修改时，必须首先获得对应的 Segment 的锁。也就是说，对同一 Segment 的并发写入会被阻塞，不同 Segment 的写入是可以并发执行的。

    - 1.8

      - cas + sychronized

        - Java 8 中，锁粒度更细，synchronized 只锁定当前链表或红黑二叉树的首节点，这样只要 hash 不冲突，就不会产生并发，就不会影响其他 Node 的读写，效率大幅提升。

    - 为什么key和value不能用null

      - 有歧义

        - 无法区分 null这个键是否存在其中
        - 还是根本没有这个键

- 线程不安全

  - HashMap

    - 1.7

      - 头插
      - 数组+链表

    - 1.8

      - 尾插
      - 数组+链表+红黑树

        - 在链表长度达到8时 会变为红黑树

          - 泊松分布 6亿分之1
          - 时间复杂度o(n) 变为了 o(logn)

      - 长度为什么是2的幂次方

        - 位运算效率更高

          - 通过 hash值 分别和 新旧Map长度-1 进行与运算 确定下标位置

        - 更好的保证hash值均匀分布

    - 没有使用volatile 无法保证可见性

      当多个线程对不同的Node进行修改的时候 会出现覆盖或者丢失的情况

    - 扰动函数的不同

      - 1.7

        -  h ^= k.hashCode();   
           h ^= (h >>> 20) ^ (h >>> 12);
            return h ^ (h >>> 7) ^ (h >>> 4);

           - 多次位移 多次异或 
             操作较多 相对于1.8而言性能较低

      - 1.8

        -  (h = key.hashCode()) ^ (h >>> 16)

           - 一次位移 一次异或
             操作少，相对于1.7而言效率较高

      - 扰动函数的目的：为了让高位的hashcode值可以充分利用，增加hash的随机性

    - get的整个过程

      - 1 计算hash值

        - 先用hashcode
        - 再用扰动函数

      - 2 确定桶的位置

        - int index = (table.length - 1) & hash;

      - 3 查找链表 / 红黑树
      - 4 返回找到的结果

## 并发

### 锁

- 线程

  - 实现线程的方式

    - 说到底其实都是start方法，不论是通过那种方式

      - new Thread
      - Callable

        - 有返回值

      - Runnable

        - 无返回值

  - 线程池

- sychronized

  - 最底层 是调用内核态的mutex方法
  - 字节码文件中的指令

    - monitorenter

      - 代表进入

    - monitorexit

      - 有两个 代表正常和异常情况下都能推出

  - 可重入
  - 阻塞
  - 锁升级（1.6之后的优化）

    - 无锁
    - 偏向锁 （JDK15之后废除）

      - 引入偏向锁的目的：在没有多线程竞争的情况下，尽量减少不必要的轻量级锁的执行。轻量级锁的获取及释放依赖多次CAS原子指令，而偏向锁只依赖一次CAS原子指令。但在多线程竞争时，需要进行偏向锁撤销步骤，因此其撤销的开销必须小于节省下来的CAS开销，否则偏向锁并不能带来收益。JDK 1.6中默认开启偏向锁，可以通过-XX:-UseBiasedLocking来禁用偏向锁
      - 后续只需要看 ThreadID是否是自己即可
      - 偏向锁撤销

        - 当调用了对象的hashcode的时候 偏向锁的对象Markword中存储的事线程id 如果调用hashCode会导致偏向锁被撤销 （101 -> 001）

          - 轻量级锁会在Lock Record中记录hashCode
          - 重量级锁会在Monitor 中记录hashCode

        - 调用 wait/notify

          - 因为需要将线程放入WaitSet中，只有重量级锁才有

        - 在正常情况下，当多个线程需要同一把锁，此时会从偏向锁升级为轻量级锁

      - 重偏向

        - 当对象被多个线程访问，但没有竞争，这时偏向了线程T1的对象仍然有机会重新偏向T2，重偏向对象的ThreadId
        - 当一个线程，撤销偏向锁这个操作超过20次之后，JVM会觉得可能偏向错了，于是会给在这些对象枷锁时 重新偏向当前的加锁线程

      - 锁消除

        - 发生在JIT（及时编译），检测到一个对象只会被一个线程访问，就会消除这个对象上的锁

      - 锁粗化

        - 当JVM发现一段代码对同一个对象反复进行加锁和解锁，会将这些锁操作合并为一个更大的锁块，以减少锁的粒度，降低锁的开销

    - 轻量级锁

      - 在第一次加锁时，会用CAS去进行和Object的Mark word进行操作
      - 但是当锁重入的时候 还会在第二次继续用CAS去尝试替换已经持有 lock record的object
      - 适用场景

        - 低竞争：只有一个线程尝试获取锁，或者多个线程交替获取锁（没有真正的竞争）。
        - 短时间持有锁：锁的持有时间较短，适合快速获取和释放。

      - 优点

        - 低开销：在无竞争或低竞争场景下，轻量级锁的开销远低于重量级锁。
        - 快速获取和释放：通过 CAS 操作实现锁的获取和释放，避免了操作系统内核态的开销。

    - 重量级锁

- AQS

  - 结构

    - Node

      - SHARED（共享）
      - EXCLUSIVE（排他锁）
      - prev
      - next
      - waitStatus

        - 四种状态

          - CANCELLED = 1 线程节点因为某种原因被取消
          - SIGNAL = -1 负责去唤醒下一个节点
          - CONDITION = -2 条件队列
          - PROPAGATE = -3 共享锁

        - 初始值为0 可以当作是Initial状态 

      - 包装到Node节点的线程 thread
      - state

        - state > 1时代表的是锁重入的次数
        - 1代表锁被占用
        - 0代表锁空闲

  - 实现

    - ReentrantLock

      - 可打断

      - 可重入

        - 锁重入了两次，但是只释放1次，会出现下个线程无法使用锁的情况，因为锁没有释放完全

      - 锁超时

      - 公平/非公平

        - 非公平锁

          - 获得锁 lock

            - acquire

              - CAS 设置state， 设置为1 

                - 成功

                - 失败

                  - acquire(1)

                    - tryAcquire 尝试获取锁

                      - 成功

                      - 失败

                        - 执行addWaiter方法

                          - 目的：如果头节点为空的话 设置哨兵节点Dummy，不为空有任务在排队的话，让节点排队

                          - java8

                            - 先判断有节点的情况(pred != null, 这里是表示尾节点不为空，也就是已经有节点在排队了)下，能否cas加入队列成功

                              - 成功

                                - 返回 线程节点

                              - 失败

                            - enq()

                              for (;;) {
                                  Node t = tail;
                                  if (t == null) { // Must initialize
                                      if (compareAndSetHead(new Node()))
                                          tail = head;
                                  } else {
                                      node.prev = t;
                                      if (compareAndSetTail(t, node)) {
                                          t.next = node;
                                          return t;
                                      }
                                  }
                              }


                              - 无尾节点的情况，也就是没有线程节点在阻塞等待中

                                - 再次cas 设置哨兵节点

                              - 有尾节点的情况，也就是有线程节点在阻塞等待中

                                - 再次cas 新增尾节点

                          - java9以后 

                            - 由java8可以看出 判断逻辑重复，代码出现了冗余，因此优化了这里的代码

                              for (;;) {
                                  Node oldTail = tail;
                                  if (oldTail != null) {
                                      node.setPrevRelaxed(oldTail);
                                      if (compareAndSetTail(oldTail, node)) {
                                          oldTail.next = node;
                                          return node;
                                      }
                                  } else {
                                      initializeSyncQueue();
                                  }
                              }


                              - 由于代码一定会先进行一次 是否节点在排队 的判断，而大多数的时候，都是有线程在排队的，那么在java9之后，就会先判断有节点的情况

                        - AcquireQueue()

                          - 方法内for(;;)

                            - 再次tryAcquire()

                              - 成功
                              - 失败

                                - shouldParkAfterFailedAcquire

                                  - 获得了前驱节点的状态 如果为-1 就代表着需要唤醒线程节点 ，从而 return true
                                  - 如果不为-1，这个时候就用CAS去赋值为-1，等待cas赋值成功

                                    - 也因此会再次执行for循环

                                - parkAndCheckInterrupt

          - 释放锁 unlock

            - release(1)

              - tryRelease 表示如果尝试释放锁成功

                - 成功

                  - tryRelease内部逻辑

                    - c 为 state - releases  这里release 为 1
                    - free 为false 代表没有完全释放锁
                    - 当c 为 0的时候 代表所释放

                      - setFree(true)
                      - 释放锁

                  - 判断头节点是否为空（即是否有任务线程在等待） && 头节点waitStates 是否不为0 （不为0的话才能去释放）

                    - 如果waitState < 0 那么需要用cas将状态改为0
                    - 根据头节点的后继，判断 后继节点是否存在 ｜｜ ws为CANCEL

                      - 如果waitState > 0 需要跳过ws为CANCEL状态的节点 因为有的任务已经被取消了

                    - 如果后继节点不为null， 就 unpark

                - 失败 return false

        - 公平锁

      - 默认是非公平锁

  - 抽象类

    - 作为模版框架给其他类使用，不希望通过 创建对象的方式去使用

- 分布式锁

  - Redis的分布式锁实现

    - 自己开发分布式锁的思路

      - 按照JUC中的Lock规范来进行开发，因为java里的锁都是以这个规范来开发的
      - lock逻辑（保证独占 可重入 高可用）

        - 加锁

          - 给key设置一个值 并设置过期时间（类似于setIfAbsent(key,value,timeout,Time.Unit)） 保持原子性，如果不保证原子性，当一个容器挂了之后，并且还没加上锁，那么就会出现死锁的情况

        - 自旋

          - 也是为了防止出现虚假唤醒！！ 比如在wait的时候，没有notify / notifyAll就将线程唤醒了
          - 要保证有几十毫秒的空档期，防止cpu被打满

        - 续期

          - 如果实际业务时间超过了过期时间，就会出现删除了别人的锁，这样是错的！！！

        - 重试

      - unlock逻辑

        - key不能乱删 防止第一个删除第二个的key

          - 如果实际业务时间超过了过期时间，就会出现删除了别人的锁
            因此删除锁只能删除自己的，并且需要保证原子性（先看看解锁的是不是客户端），可以使用lua脚本，通过eval命令保证原子性

            - lua中数组下标从1开始
            - 

      - java

        - DefaultRedisScript

    - Redis + lua

      - 独占性

      - 可重入性

        - hset lockname uuid:线程id 重入次数
        - 可以用工厂模式实现其他分布式锁

          - zookeeper
          - mysql

      - 防死锁

        - 自带过期时间

          - 续期问题

      - 继承Lock接口，保证锁的设计和Java锁的实现方法设计类似，满足接口规范

      - lua保证原子性，是因为在解锁的时候需要先判断后删除，但如果是在两条语句中去实现的话，就可能会出现误删

      - lua脚本编写

        - 加锁

          - 先判断这个Key是否存在（Exists key）

            - 返回0 就表示不存在
            - 返回1就表示存在，已经有锁，需要判断锁是不是当前线程自己的

              - 用hexists hash结构是为了可重入

                - 0就表示不是自己的
                - 1就表示是自己的

                  - 需要自增1次表示重入

          - 代码

            // 如果key不存在 或者 hexits 判断是当前线程使用的锁 ，就加锁 / 重入锁
            if redis.call('exists', KEYS[1]) == 0 or redis.call('hexits', KEYS[1], ARGV[1]) == 1 then
            	redis.call('hincrby', KEYS[1], ARGV[1], 1)
            	redis.call('expire', KEYS[1], ARGV[2])
            	return 1
            else 
            	return 0
            end

            KEYS[1] 代表锁的名称
            ARGV[1] uuid:threadid 在Redisson中代表 客户端id+threadId
            ARGV[2] 代表过期时间

          - java代码中 在加锁成功之后，会开启一个定时任务去续期这个分布式锁（看门狗机制）

            private void renewExpiration() {
                ExpirationEntry ee = EXPIRATION_RENEWAL_MAP.get(getEntryName());
                if (ee == null) {
                    return;
                }

                Timeout task = commandExecutor.getConnectionManager().newTimeout(new TimerTask() {
                    @Override
                    public void run(Timeout timeout) throws Exception {
                        ExpirationEntry ent = EXPIRATION_RENEWAL_MAP.get(getEntryName());
                        if (ent == null) {
                            return;
                        }
                        Long threadId = ent.getFirstThreadId();
                        if (threadId == null) {
                            return;
                        }
                
                        // 续期锁的过期时间
                        RFuture<Boolean> future = renewExpirationAsync(threadId);
                        future.onComplete((res, e) -> {
                            if (e != null) {
                                log.error("Can't update lock " + getName() + " expiration", e);
                                return;
                            }
                            if (res) {
                                // 递归调用，继续续期
                                renewExpiration();
                            }
                        });
                    }
                }, internalLockLeaseTime / 3, TimeUnit.MILLISECONDS); // 每隔 lockWatchdogTimeout / 3 执行一次
                ee.setTimeout(task);

            }


            - 

        - 解锁

          - 判断是不是自己的锁（hexists）

            - Hexists key uuid:threadId

              - 返回0 说明根本没有锁 程序返回nil
              - 不为0 说明肯定是有锁 且是自己的锁
                因为是可重入锁，所以直接调用hincrby -1 表示每次减1，解锁一次
                直到变为0 可以删除该锁的key 通过del删除锁key

          - 代码

            if redis.call('hexits', KEYS[1], ARGV[1]) == 0 then
              return nil
            elseif redis.call('hincrby' , KEYS[1], ARGV[1], -1) == 0 then
            	return redis.call('del', KEYS[1])
            else 
            	return 0
            end

    - Redisson

      - 之前Redis+lua方案的缺点

        - 线程1在对某个数据使用锁的时候，master挂了，一台slave 成为了master，此时新的master不含有线程1加的锁，因此线程2也能对那个加锁的数据操作

      - Redlock

        - 旨在解决Redis单点故障的问题

          - 假设有5个实例  都是master  使用相同的key和具有唯一性的value获取锁
          - 客户端使用当前时间 减去 开始获取锁的时间， 就可以得到锁使用的时间 当且仅当从大多数的redis节点都获取到了锁，并且使用的时间小于锁失效的时间 才算成功
          - 如果取得了锁， key真正有效时间 等于 有效时间 减去 获取锁所使用的时间
          - 因为某些原因导致获取锁失败，客户端应该在所有的Redis实例上解锁（即使某些redis实例根本没有加锁成功，也防止某些节点获取到锁但是客户端没有得到响应而导致接下来的一段时间不能被重新获取锁）

      - 注意事项

        - 加锁

          - 默认是30s
          - 流程

            - 通过exists判断 如果锁不存在，则设置值和过期时间 加锁成功
            - hexists 判断锁已经存在，并且锁的是当前线程，则证明是可重入锁，加锁成功
            - 锁已经存在，但锁的不是当前线程 则证明有其他线程持有锁，返回当前锁的过期时间
              （命令为：pttl key），枷锁失败

        - 可重入
        - 续期

          - renewExpiration 方法 并且每隔10s检查 并续期
          - 内部有 renewExpirationAsync方法 内部通过lua实现 去续期

        - 解锁 

          - 高并发下可能会出现解锁的时候出现别的线程尝试去解锁本线程

            - isLock() && isHeldByCurrentThread()

          - 异步方法 unlockInnerAsync

            - 释放锁的线程和存在锁的线程不是同一个 返回null
            - 通过hincrby -1 先释放一次锁 若剩余次数还大于0 说明重入了 刷新过期时间
            - 若剩余次数<= 0  则删除key 并且发布锁释放的消息，解锁成功

      - 看门狗

        - 加锁成功时 会起一个watchdog线程 后台扫描，定期检查 每 过期时间（默认30秒）  / 3 的时候会查一下，还在锁住，就续期
        - renewExpiration

      - 有效性和安全性

        - 互斥

          - 任何时刻 只有一个client可以获取到锁

        - 释放死锁

          - 即使锁定资源的服务崩溃，仍可以释放锁

        - 容错性 

          - 只要多数redis节点在使用 client就可以获取和释放锁

- Mysql

  - 表锁

    - 元数据锁（MDL）

      - MDL读锁

        - 进行CRUD操作
        - 读读兼容

      - MDL写锁

        - 表的结构变更
        - 写锁优先级高于读锁，出现MDL写锁等待，会阻塞表所有的CRUD操作

  - 行锁

- 死锁

  - 四个必要条件

    - 互斥条件

      - 该资源任意一个时刻都只由一个线程占用

    - 请求与保持条件

      - 一个线程因请求资源而阻塞时，对已获得的资源保持不放

    - 不剥夺条件

      - 已获得的资源在未使用完之前不能被其他线程强行剥夺，只能自己使用

    - 循环等待条件

      - 形成头尾相接的循环等待资源关系

  - 检测死锁

    - jmap

- 乐观锁 悲观锁

  - 乐观锁

    - 乐观锁会假设冲突很少发生，访问共享资源时，不加锁，等真正需要修改的时候再去检查资源是否有冲突，如果冲突的话再去回滚 或者 重试 即：
      只有在真正需要修改资源时才检查冲突，减少了不必要的锁竞争，提高了性能。
    - 举例：CAS 只能保证单个变量的原子性，无法保证多个变量或操作的原子性。 强一致性只能通过悲观锁的机制
    - 使用场景：适用于读操作多于写操作的场景，或者冲突概率较低的场景。
    - public final native boolean compareAndSwapInt(Object var1, long var2, int var4, int var5); 
      可以发现是底层方法控制cas

      - CAS 的原子性是由 硬件指令 保证的。现代 CPU 提供了专门的指令来实现 CAS 操作，
      - x86 架构：CMPXCHG 指令。

ARM 架构：LDREX 和 STREX 指令。
			- 确保一下操作的原子性：
读取内存值：读取变量的当前值。
比较值：将读取的值与预期值进行比较。
更新值：如果值匹配，则更新为新值。

		- 
	
	- 悲观锁
	
		- 悲观锁会假设并发冲突很可能发生，因此在访问数据时，会先加锁，确保其他线程无法修改数据。 即：

在访问共享资源时总是先加锁，确保在事务期间没有其他线程可以修改该资源。
		- 适用场景： 适用于写操作多于读操作的场景，或者对数据一致性要求极高的场景。

### ThreadLocal

- 使用场景

  - 数据库连接

    - 因为每个线程都需要独立的数据库连接

  - 用户会话管理

    - ThreadLocal来存储每个用户的会话信息， 确保每个线程只能访问自己的会话数据

  - 事务管理

    - 存储事务状态，确保每个线程只能操作自己的事务

- 结构

  - 每个Thread里会有ThreadLocalMap

    - 对应的Entry的key就是ThreadLocal的弱引用
    - value是强引用
    - ThreadLocalMap 用的是线性探测法 去解决 hash冲突的问题。 线性探测法就是发现出现hash冲突的时候，会向后进行探测，找到第一个有空闲位置的

- 如果出现异常无法执行到remove

### 线程池

- 优点

  - 降低资源消耗
  - 提高响应速度
  - 对线程进行统一管理

- 创建方法

  - Executor工具类中的Executors

    - 不推荐！四种类型的线程池都有对应缺点，现有的场景基本都需要自定义线程池

  - ThreadPoolExecutor

    - 主要参数

      1 可以先让救急线程去执行，再去执行阻塞队列（Tomcat）
      2 当阻塞队列类型是DelayedWorkQueue 的时候 ，有可能会出现 核心线程任务结束，但是阻塞队列是延迟队列，可能还未被消费

      - 核心线程数
      - 最大线程数
      - 存活时间
      - 时间单位
      - 阻塞队列

        - 有界队列
        - 无界队列

          - 可以按照优先级排列

            - PriorityBlockingQueue

      - 线程工厂
      - 拒绝策略

        - AbortPolicy

          - 抛出异常来拒绝新任务

        - CallerRunsPolicy

          - 会让调用exeute方法的线程，去执行被拒绝的任务
          - 风险：如果被拒绝的任务时十分耗时的任务，并且处理提交任务的是主线程，可能会导致主线程阻塞，影响程序运行

        - DiscordPolicy

          - 不处理，直接丢弃

        - DiscordOldestPolicy

          - 此策略将丢弃最早的未处理的任务请求

- 线程出现异常

  - execute

    - 线程终止，并且将异常打印到控制台/日志
    - 创建新的线程进行替换，从而保证线程数不变

  - submit

    - 如果在任务执行中发生异常，这个异常不会直接打印，而是会被封装在返回的Future对象中，调用get方法时，可以捕获到一个Exception
    - 线程不会终止，而是继续在线程池中，准备后续任务

- 动态线程池的实现

- 线程池状态

  - RUNNING 运行中

    - 接受新任务并且处理队列中的任务
    - 触发条件：线程池初始化之后的默认状态
    - 高3位为111 对应的十进制为负数

  - SHUTDOWN关闭

    - 不再接受新任务
    - 调用 shutdown()方法
    - 高3位 000

  - STOP 停止

    - 不接受新任务，中断正在执行的任务，并且丢弃队列中的任务
    - 高3位 001

  - TIDYING

    - 所有任务已经终止， 工作线程数为零。 过渡状态，准备执行terminated() 钩子方法
    - 触发条件

      - 线程池状态为SHUTDOWN 并且 任务队列为空，工作线程数为0

    - 高3位 010

  - TERMINATED（终止）

    - terminated() 方法执行， 线程池完全终止
    - 触发条件 TIDYING 状态 terminated() 方法
    - 高3位 位011

### CompletableFuture

- Future

  - Future接口定义了操作异步任务执行一些方法，比如获取异步任务的执行结果，取消任务的执行，判断任务是否被取消，判断任务执行是否完毕等。
  - 特点：多线程 / 有返回值 / 异步任务

    - 多线程 + 异步

      - new Thread() 有个Runnable的方法
      - Runnable 有个 子类为 RunnaleFuture

    - 有返回值

      - Callable

    - 综上可得 FutureTask(Callable callable)

      - 也因此FutureTask实现了RunnableFuture接口
        并且构造方法中注入 Callable

  - 优缺点

    - 优点
    - 缺点

      - 一旦调用get方法去获取return的结果，如果那个线程没有计算完成，很容易导致程序阻塞
      - isDone轮询

        - 会耗费无用的CPU资源

      - 也因此FutureTask不是很友好 要么阻塞 要么轮询

- CompletableFuture

  - 提供了一种类似于观察者模式的机制，当任务完成的时候通知监听的一方

  - 实现了Future 和 CompletionStage<T>

  - 核心四个静态方法（也是证明了完全可以替代Future接口）

    - runAsync 无返回值

      - public static CompletableFuture<Void> runAsync(Runnable runnable)
      - public static CompletableFuture<Void> runAsync(Runnable runnable, Executor executor)

    - supplyAsync 有 返回值

      - public static <U> CompletableFuture<Void> supplyAsync(Supplier<U> supplier)
      - public static <U> CompletableFuture<Void> supplyAsync(Supplier<U> supplier, Executor executor)

    - 对于Executor executor 的说明

      - 如果用没有指定Executor的方法，会用默认的
        ForkJoinPool.commonPool()作为指定线程池
      - 如果指定线程池 则使用我们自定义的或者特别指定的线程池执行异步代码

  - 减少阻塞和轮询的演示

    ExecutorService threadPool = Executors.newFixedThreadPool(3);
    try {
        CompletableFuture.supplyAsync(() -> {
            System.out.println(Thread.currentThread().getName() + " -- come in"  + " " + new Date());
            int result = ThreadLocalRandom.current().nextInt(10);
            try {
                Thread.sleep( 1 * 1000);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }

            System.out.println("1s 后出结果: " + result  + " " + new Date());
            return result;
        },threadPool).whenComplete((v,e) -> {
            if (e == null) {
                System.out.println(" compute complete, the value is " + v + " " + new Date());
            }
        }).exceptionally( e -> {
            System.out.println("excepetion is : " + e.getCause() + "\t" + e.getMessage()  + " " + new Date());
            return null;
        });

    } catch (Exception e) {
        e.printStackTrace();
    } finally {
        // 需要手动关闭
        threadPool.shutdown();
    }


    - whenComplete() 当任务完成后，可以处理完成后的相关逻辑
    - 自定义线程池记得关闭，否则程序会一直退不掉！！！
    - 默认的ForkJoinPool类似于守护线程，当主进程任务结束后，这个线程池也会退出，因此推荐用自定义线程池！！！！

  - CompletableFuture 优点

    - 异步任务结束时，可以通过 whenComplete 之类的方法 回调某个对象的方法
    - 设置好回调后，不需要再关心异步任务的执行，异步任务可以按序执行
    - 异步任务出错时，会通过exceptionally()设置  自动回调某个对象方法

  - 真实需求: 电商网站比价

    public static List<String> getPriceByCompletableFuture(List<NetMall> list, String productName){
        // 各查各的 但一起同步去查
        return list.stream().map(netMall -> CompletableFuture.supplyAsync(() ->
                        String.format(productName + " in %s price is %.2f"
                                , netMall.getNetMallName()
                                , netMall.calPrice(productName))))
                .collect(Collectors.toList())
                .stream()
                .map(s -> s.join())
                .collect(Collectors.toList());
    }


    - 基础了解

      - 函数式编程

        - Lambda + Stream + Chain链式调用 + 函数式表达式

          - Runnable 

            - 无参数 无返回值

          - Function

            - 接受一个参数 并且有返回值

          - Consumer

            - 有一个参数 无返回值

          - BiConsumer

            - 两个参数 无返回值

          - Supplier

            - 无参数 但有返回值

          - 小总结

    - CompletableFuture join

      - 编译期间不会做检查型异常的处理

  - 常用方法

    - 获得结果和触发计算

      - 获取结果

        - get
        - get(long timeout, TimeUnit unit)

          - 过时不候，比如get(2, TimeUnit.Second) 但执行时间为3s 那么2s后会强制抛出异常

        - join()

          - 编译期间不会做检查型异常的处理
          - 作用和get几乎一样

        - getNow(T valueIfAbsent)

          - 没有计算完成的情况下，给一个替代的结果
          - 立即获取结果不阻塞

            - 计算完，给return的结果
            - 没计算玩 给valueIfAbsent

      - 主动触发计算

        - public boolean complete(T value)

          - 代表是否打断

            - true 代表打断了执行的结果，直接返回value
            - false 代表没有打断执行 返回return的结果

    - 对计算结果进行处理

      ExecutorService threadPool = Executors.newFixedThreadPool(3);
      CompletableFuture.supplyAsync(() -> {
                  try {
                      TimeUnit.SECONDS.sleep(1);
                  } catch (InterruptedException e) {
                      e.printStackTrace();
                  }
                  System.out.println("111");
                  int i = 1/0;
                  return 1;
              },threadPool).handle((f,e) ->{
                  System.out.println("222");
      //            int i = 1/0;
                  System.out.println("22221");
                  return  2;
              }).handle((f,e) ->{
                  System.out.println("333");
                  return 3;
              }).whenComplete((v, e) -> {
                  if (e == null) {
                      System.out.println("-- result -- "+ v);
                  }
              }).exceptionally(e -> {
                  e.printStackTrace();
                  System.out.println(e.getMessage());
                  return null;
              });
              System.out.println(Thread.currentThread().getName() + " 线程先去忙其他任务"  + " " + new Date());
              threadPool.shutdown();

      

      - thenApply

        - 计算结果存在依赖关系，这两个线程串行化
        - 碰到异常就不会执行后续的代码，会直接抛出

      - handle

        - 计算结果存在依赖关系，这两个线程串行化
        - 更像try/finally 就算出现异常 也会去执行handle内部的代码
        - 如果抛出NullPointer异常 那么很有可能是之前前面的出错了，导致没有return对应的值传到下层，接着下层handle获取对应的值的时候 获取不到 ，然后后续又要去使用这个值，但是是Null 所有很有可能会抛出这样的异常

    - 对计算结果进行消费

      - 接受任务的处理结果，并且消费处理，因此无返回结果

      - thenAccept

        - 接收到上面的return 消费处理，之后无返回结果 

      - 任务之间的执行顺序

        - thenRun

          - thenRun(Runnable runnable)

            System.out.println(CompletableFuture.supplyAsync(() -> "result").thenRun(()->{}).join());
            执行结果 为null 

          - 任务A执行完 执行任务B，并且B不需要A的结果

        - thenAccept

          - thenAccept(Consumer action)
          - 任务A执行完 执行任务B，并且B需要A的结果
            但是任务B无返回值

        - thenApply

          - thenApply(Function fn)
          - 任务A执行完 执行任务B，并且B需要A的结果
            同时任务B有返回值

        - 线程池运行选择

          - 没有传入自定义线程池 都用默认的ForkjoinPool
          - 传入了自定义线程池

            - 执行第一个任务的时候 传入了一个自定义的线程池 以thenRun为例 thenApply thenAccept同理

              - 调用thenRun方法执行第二个任务时 则第二个任务和第一个任务共用一个线程池
              - 调用thenRunAsync方法执行第二个任务时 第一个任务用的是自己传入的线程池
                第二个任务之后用的都是ForkJoinPool线程池

                - 有可能处理太快 系统会优化切换原则，直接使用main线程处理

              - 源码会调用 uniRunStage(asyncPool,action)

                - 底层会判断： 会ForkJoinPool给上层

    - 对计算速度进行选用

      - applyToEither

        - 谁快返回谁

    - 对计算结果合并

      - thenCombine

    - allOf

      - 接受一个 CompletableFuture 数组作为参数，并返回一个新的 CompletableFuture<Void>。这个新实例会在所有输入的 CompletableFuture 完成时被完成

### 并发与并行

- 并发

  - 在同一个实体上的多个事件
  - 在一台处理器上 “同时” 处理多个任务

    - 比如抢票，多个请求对一个资源发起“集火攻击”

  - 同一时刻，其实是只有一个事件在发生

- 并行

  - 在不同实体上的多个事件
  - 在多台处理器上同时处理多个任务

    - 比如泡面 边烧水 边拆包装 各干各的

  - 同一时刻 大家都在做事情，但你做你的 我做我的

- 进程 线程 管程

  - 进程

    - 系统中运行的应用程序

  - 线程

    - 一个进程内有一个或者多个线程，大多数线程进行调度的基本单元

  - 管程

    - Monitor，也就是平时说的Sychronized
    - 

- 用户线程和守护线程

  - 用户线程
  - 守护线程

    - 一种特殊的线程，为其他线程服务的，在后台默默地完成了一些系统性的服务，比如垃圾回收线程就是最典型的例子
    - 守护线程作为一个服务线程，没有服务对象就没有必要继续运行了，如果用户线程全部结束了 意味着程序需要完成的业务操作已经结束了，系统可以退出了，所以当系统只剩下守护线程的时候，JVM会自动退出
    - 线程的Deamon属性

## 反射

### 为什么反射慢

- 1 JVM需要时间预热，遇到反射JIT无法知道真正需要什么
- 2 必须去发现被调用 / 创建的所有内容

### 概念

- Java 反射（Reflection）是一种强大的机制，允许程序在运行时动态地获取类的信息并操作类或对象。反射的核心是通过 java.lang.reflect 包中的类和接口（如 Class、Method、Field 等）来实现对类的动态访问。

## 框架

### Spring

- ioc

  - 依赖注入（di）
  - Bean

    - 单例

      - 默认是单例的
      - lazy-init懒加载，默认是false，设置为true的话，会在getBean进行加载

    - 多例
    - 创建顺序

      - 默认是按照xml文件里编写的顺序加载的，不配置depends-on的话 不存在依赖关系
      - 如果bean的内部有引用关系也没关系，整个加载顺序是，先将对应的bean注入容器，之后再用setter方法，将引用关联上

    - 生命周期

      - 执行构造器，创建bean实例
      - 执行set相关方法，对属性赋值
      - 初始化

        - 1 Aware 接口的依赖注入

          - BeanNameAware：注入当前 bean 对应 beanName；
            BeanClassLoaderAware：注入加载当前 bean 的 ClassLoader；
            BeanFactoryAware：注入当前 BeanFactory 容器的引用。

        - 2 BeanPostProcessor

          - postProcessBeforeInitialization

            - 对传入的bean进行修改

        - 3 InitializingBean
        - 4init-method方法的实现，可以在xml中的bean里的init-method属性进行配置
        - 5 BeanPostProcessor的postProcessAfterInitialization方法

      - 销毁

        - 1 如果实现DisposableBean接口 就去调用里面的destroy方法
        - 2 配置destroy-method

  - BeanFactory
  - FactoryBean

- aop

  - JDK代理

    https://blog.csdn.net/weixin_44328487/article/details/144150689

    - InvocationHandler

      - 接口的所有方法都会通过InvocationHandler中 invoke方法去执行
      - public interface InvocationHandler {

    /**

     * 当你使用代理对象调用方法的时候实际会调用到这个方法
       */
       public Object invoke(Object proxy, Method method, Object[] args)
       throws Throwable;
       }

------


		- 必须去使用接口，不能直接代理类，因为动态生成的代理类会继承Proxy类，如果代理的是没有实现接口的类的话，就会继承2个类，但是java是只能继承一个类的，因此会报错
		- 通过newProxyInstance(ClassLoader loader,
	             Class<?>[] interfaces,  InvocationHandler h) 去创建代理对象
	
			- loader :类加载器，用于加载代理对象。

interfaces : 被代理类实现的一些接口；
h : 实现了 InvocationHandler 接口的对象；

	- CGLib代理


​	  

	  这个例子清楚地展示了 CGLib 代理的继承特性：
	  
	  CGLib 通过 Enhancer 创建一个被代理类的子类作为代理类。
	  从输出可以看到：
	  代理类的名称类似 Animal$$EnhancerByCGLIB$$123456
	  代理类的父类就是我们的目标类 Animal
	  代理对象确实是 Animal 的实例（instanceof 返回 true）
	  代理的工作原理：
	  当调用代理对象的方法时，会先触发拦截器的 intercept 方法
	  在拦截器中，通过 proxy.invokeSuper(obj, args) 调用父类（原始类）的方法
	  可以在方法调用前后添加自定义逻辑
	  
	  这就是为什么 CGLib 不能代理 final 类和方法的原因：
	  
	  final 类不能被继承
	  final 方法不能被重写
	  
	  这种通过继承实现的代理方式比 JDK 动态代理更灵活，因为：
	  
	  不需要目标类实现接口
	  可以直接访问被代理类的所有方法
	  生成的字节码更接近直接调用，性能通常更好


​	  

	  https://javaguide.cn/java/basis/proxy.html#_3-2-cglib-%E5%8A%A8%E6%80%81%E4%BB%A3%E7%90%86%E6%9C%BA%E5%88%B6
	  
		- new Enhancer()
		- setSuperclass()
	
			- 因此不可能是final类，因为final类不能被继承
	
		- 不需要实现目标接口
		- import net.sf.cglib.proxy.Enhancer;

public class CglibProxyFactory {

    public static Object getProxy(Class<?> clazz) {
        // 创建动态代理增强类
        Enhancer enhancer = new Enhancer();
        // 设置类加载器
        enhancer.setClassLoader(clazz.getClassLoader());
        // 设置被代理类
        enhancer.setSuperclass(clazz);
        // 设置方法拦截器
        enhancer.setCallback(new DebugMethodInterceptor());
        // 创建代理类
        return enhancer.create();
    }

}
		- 使用步骤

			- 1 定义一个类
			- 2 自定义MethodInterceptor 并且实现intercept方法，类似于invoke方法
			- 3 通过enhancer对象的create()创建代理对象

- 事务传播

  - 支持当前事务

    - Required

      - 如果当前方法没有事务，新建一个事务，如果已经存在一个事务，则加入到这个事务中

    - Supports

      - 支持当前事务，如果当前没有事务，就以非事务的方式执行

    - Mandatory

      - 使用当前事务，如果当前没有事务，就抛出异常

  - 不支持当前事务

    - Requires_New

      - 新建事务执行，如果当前存在事务，把当前事务挂起

        - 挂起指的是connection的挂起

    - Not_Supported

      - 以非事务方式执行操作，如果当前存在事务，就把当前事务挂起

    - Never

      - 以非事务方式执行，如果当前存在事务，则抛出异常

- 扩展机制

  - 依赖注入

    - 将已经注入ioc的bean，让其他对象去用
    - 通过反射 找到对应类中有哪些是用@Autowired @Resource注解的
    - 找到之后，从ioc中get对应名字 / 类型的bean
    - 最后set到对应的，被注解的对象中

  - BeanPostProcessor

    - Bean后置处理器
    - 实现InitializingBean

      - afterPropertiesSet()，这个方法就相当于是初始化，在这里加入初始化业务

  - AOP

### Springboot

- 自动配置

  - EnableAutoConfiguration

    - ConfigurationClassParser

      - processConfigBeanDefinitions
      - parse.parse()

        - public void parse(Set<BeanDefinitionHolder> configCandidates) 

          - BeanDefinitionHolder 是 Spring 中用于封装 BeanDefinition 及其名称的类。
          - configCandidates 是一个集合，包含了需要解析的配置类。

      - this.deferredImportSelectorHandler.process();

        - 这是对延迟配置类的处理，主要作用是将某些配置类的加载推迟到所有其他配置类处理完毕之后，以确保这些配置类能够基于完整的上下文信息进行加载。

    - AutoConfigurationImportSelector.class

      - process

        - 通过spi机制，搜集对应的类信息

          - autoConfigurationEntries 存的就是对应类信息
          - getAutoConfigurationEntry
          - getCandidateConfigurations
          - loadFactoryNames

            - 获取所有配置类名

          - loadSpringFactories

            - "META-INF/spring.factories"会解析所有的类

          - 

        - public void process(AnnotationMetadata annotationMetadata, DeferredImportSelector deferredImportSelector)

      - selectImports

        - 创建bean，并且注入容器中

- 如何写一个starter

  - 1 创建一个starter 工程
  - 2 引入 springboot starter 依赖
  - 3 创建 AutoConfiguration类
  - 4 在 这个starter工程的 resources 包下 创建 META-INF/spring.factories文件

- 启动流程

  - 启动入口：位于带有 @SpringBootApplication 注解的类中。
  - 在 SpringApplication.run() 方法中，Spring Boot 会初始化 SpringApplication 实例。
  - 运行run方法

    - 创建应用上下文ApplicationContext（ioc）
    - 加载配置文件
    - 注册BeanDefinition
    - 刷新上下文（refresh方法）

  - 自动配置
  - 服务器启动（Tomcat）
  - 启动完成

    - Spring Boot 会发布 ApplicationReadyEvent 事件

- 拦截器和过滤器

  - 拦截器

    - 流程
    - URI和URL

      - 

    - 注册拦截器

      - 注入bean的方式
      - 实现WebMvcConfigurer类的addInterceptors方法
      - 要实现HandlerInterceptor接口

        - preHandle
        - postHandle
        - afterCompletion

  - 过滤器

### Spring Cloud

- 分布式事务

  - 一个系统被拆分为多个小的微服务，每个微服务都可能存在不同的机器上，并且每个微服务都有一个单独的数据库给自己使用，此时一组操作可能会涉及到多个微服务以及多个数据库

    - 例子：下订单，订单服务中，订单+1，商品库存服务库存-1

  - 理论

    - 2PC（两阶段事务提交）

      - 

    - 3PC（三阶段提交）
    - TCC(补偿事务)

  - 实现方式

    - Seata

### RPC

### 分布式

- CAP原则

  - 

- BASE原则

  - 

## 数据库

### Mysql

- 索引

  - 类型
  - 结构

- 事务

  - 特性
  - 隔离级别

- 日志

  - Undo log
  - redo log

    - Mysql实例挂了或者宕机，重启时会通过redo log恢复数据，记录的是：在某个数据页上做了什么修改，属于InnoDB存储引擎
    - 刷盘时机

      - 查询数据是以页为单位的，更新表数据也是如此。存在Buffer Pool中

        - 

    - 存储形式

      - 以日志文件组的形式出现

        - write pos 是当前记录的位置，一边写一边后移
        - checkpoint 是当前要擦除的位置，也是往后推移

    - 为什么不每次把修改后的数据页直接刷盘，还要用redolog呢？

      - 一个数据页大小为16KB，刷盘比较耗时，并且修改可能只改了几十Byte。所以用redolog的形式记录修改内容，性能也会远远超过刷数据页的方式

  - bin log

    - 是逻辑日志，记录的内容是语句的原始逻辑，属于MySQL 的Server层
    - 三种格式

      - statement

        - 记录的是sql原文
        - 存在的问题： 要是使用的是函数的话，会重新计算

      - row

        - 记录的是具体的操作数据
          update_time=now()变成了具体的时间update_time=1627112756247，条件后面的@1、@2、@3 都是该行数据第 1 个~3 个字段的原始值（假设这张表只有 3 个字段）。
        - 缺点就是需要记录的数据太多，占用空间多，恢复和同步更消耗IO

      - mixed

        - 上述两种的结合，Mysql就判断这条sql是否会引起数据不一致，是的话就会用row 不会就用statement

    - 写入机制

      - 

        - write，是指把日志写入到文件系统的 page cache，并没有把数据持久化到磁盘，所以速度比较快
        - fsync，才是将数据持久化到磁盘的操作

      - 事务执行过程中，先把日志写到binlog cache
      - 事务提交时，把binlog cache 写到binlog
      - sync_binlog

        - 由sync_binlog控制写入和同步时机， 默认为1
        - 为0的时候，表示每次提交事务都只write，由系统自行判断什么时候执行fsync。 这样宕机的话，page cache里的binlog会丢失

- 使用

  - 慢查询

    - -- 查看慢查询是否开启
      SHOW VARIABLES LIKE 'slow_query_log';

-- 开启慢查询日志
SET GLOBAL slow_query_log = 1;

-- 查看慢查询日志文件位置
SHOW VARIABLES LIKE 'slow_query_log_file';
		- -- 查看当前慢查询阈值（单位：秒）
SHOW VARIABLES LIKE 'long_query_time';

-- 设置慢查询阈值为2秒
SET GLOBAL long_query_time = 2;

-- 记录未使用索引的查询
SET GLOBAL log_queries_not_using_indexes = 1;
		- 配置慢查询文件，在my.ini添加

			- [mysqld]

# 开启慢查询日志

slow_query_log = 1

# 设置慢查询日志文件位置

slow_query_log_file = /var/log/mysql/slow-query.log

# 设置慢查询阈值（秒）

long_query_time = 2

# 记录未使用索引的查询

log_queries_not_using_indexes = 1

# 设置每分钟记录到慢查询日志的未使用索引的查询数量

log_throttle_queries_not_using_indexes = 10

	- explain
	
		- select_type
	
			- SIMPLE：简单查询
	
				- EXPLAIN SELECT * FROM users;
	
			- SUBQUERY：子查询
	
				- EXPLAIN SELECT * FROM users WHERE id = (SELECT id FROM orders);
	
			- DERIVED：派生表（FROM子句中的子查询）
	
				- EXPLAIN SELECT * FROM (SELECT * FROM users) AS t;
	
			-  UNION：联合查询
	
				- EXPLAIN SELECT * FROM t1 UNION SELECT * FROM t2;
	
		- type
	
			- system：表只有一行记录
			- const：主键或唯一索引等值查询
	
				- e.g. EXPLAIN SELECT * FROM users WHERE id = 1;
	
			- eq_ref：唯一索引扫描，JOIN时使用
	
				- e.g. EXPLAIN SELECT * FROM t1,t2 WHERE t1.id = t2.id;
	
					- eq_ref 是使用主键或唯一索引时产生的访问方式，通常使用在多表联査中。比如，对两张表进行联查，关联条件是两张表的 user id 相等，且 user id 是唯一索引，那么使用 EXPLAIN 进行执行计划查看的时候，type 就会显示 eq_ref。
	
			- ref：非唯一索引扫描
	
				- e.g. EXPLAIN SELECT * FROM users WHERE name = 'Tom';
	
					- ref 表示 MySQL 将通过非唯一索引扫描找到匹配的行，或者通过索引列与非唯一列的比较来查找匹配的行。
					- 这种访问方式用于使用非唯一索引或前缀索引的等值查询。
	
			- range：范围扫描
	
				- e.g. EXPLAIN SELECT * FROM users WHERE age > 20;
	
			- index：索引全扫描
	
				- e.g. EXPLAIN SELECT id FROM users;
	
					- index 表示 MySQL 会扫描整个索引来满足查询条件。与全表扫描不同的是，index 只扫描索引树，而不读取表的行数据。
					- 这是比全表扫描稍微好一点的访问方式，但仍然效率不高，通常用于没有合适的 WHERE 子句条件时
	
			- ALL：全表扫描（最差）
	
		- possible_keys：可能用到的索引
		- key：实际使用的索引
		- ref：显示索引的哪一列被使用了
		- rows：预计要读取的行数
		- filtered：表示返回结果的行数占需读取行数的百分比

- Buffer Pool

  - 默认大小 128M

    - 可以通过innodb_buffer_pool_size进行修改

  - 以页的方式进行存储

    - 页管理

      - 空闲页 Free Page

      - 脏页 Dirty Page

      - 干净页 Clean Page

      - 淘汰算法 LRU

        - LRU 最近最少使用算法

          - 基本原则是，维护一个固定长度双向链表，内部存放对应的节点数据，如果用到了，就会放到链表最前列，如果没用到，并且插入新节点时，链表长度 大于 规定的固定长度，那么就会将链表最后一位存放数据的节点淘汰

            class LRUCache {
                private static class Node {
                    int key;
                    int value;
                    Node prev;
                    Node next;

                    Node (){}
                
                    Node (int key, int value) {
                        this.key = key;
                        this.value = value;
                    }
                }
                
                private final int capacity;
                
                private final Node dummy = new Node();
                // 用map是为了更快的查询出对应的值
                private Map<Integer, Node> cache = new HashMap<>();
                
                // 初始化整个LRU缓存
                public LRUCache (int capacity){
                    this.capacity = capacity;
                    dummy.prev = dummy;
                    dummy.next = dummy;
                }
                
                // 使用对应的值
                public int get(int key){
                    if (!cache.containsKey(key)) {
                        return -1;
                    }
                    // 将该key对应的数据在链表中先删除，后插入cache头部
                    Node node = cache.get(key);
                    // 删除
                    remove(node);
                    // 插入头部
                    pushFront(node);
                    return cache.get(key).value;
                }
                
                // 插入节点
                public void put(int key, int value){
                    // cache中不存在使用数据的情况下
                    if (!cache.containsKey(key)) {
                        Node node = new Node(key,value);
                        cache.put(key, node);
                        pushFront(node);
                    } else {
                        // cache中存在使用数据的情况下
                        // 删除对应的节点
                        remove(cache.get(key));
                        Node node = new Node(key,value);
                        cache.put(key, node);
                        pushFront(node);
                    }
                    // 此时已经将节点放进去了 那么 就应该用 > 进行判断 
                    if (cache.size() > capacity) {
                        Node lastNode = dummy.prev;
                        // 删除节点
                        remove(lastNode);
                        // 缓存删除
                        cache.remove(lastNode.key);
                    }
                
                }


                // 删除节点
                private void remove(Node node) {
                    node.next.prev = node.prev;
                    node.prev.next = node.next;
                }
                
                // 插入到链表头部位置
                private void pushFront(Node node){
                    // 如果是 现在是 只有dummy节点， 那么就是 dummy <- node
                    // 如果现在是 不只有dummy节点 那么就是   node -> dummy <=> oldnode <=> dummy
                    node.prev = dummy;
                    // 如果是 现在是 只有dummy节点 dummy <=> node
                    // 如果现在是 不只有dummy节点 那么就是   oldnode <- node -> dummy <=> oldnode <=> dummy
                    node.next = dummy.next;
                    // 如果是 现在是 只有dummy节点 dummy <=> node <- dummy
                    // 如果现在是 不只有dummy节点 那么就是   oldnode <- node <=> dummy <=> oldnode <=> dummy
                    node.prev.next = node;
                    // 如果是 现在是 只有dummy节点 dummy <=> node <=> dummy
                    // 如果现在是 不只有dummy节点 那么就是    dummy <=> node <=> oldnode <=> dummy
                    node.next.prev = node;
                }

            }

        - 因为有的数据是热数据 可能会被只访问一次的冷数据给挤掉

        - 因此分成了冷数据区域和热数据区域 冷热比为3: 7

          - 热数据的判定：在Lru链表中存在超过1s中为热数据

  - 控制块

- 锁

  - 如何加的行级别的锁

    - 唯一索引等值查询

      - 查询的记录存在

        - 临键锁退化为记录锁
        - 如果有其他事务请求操作的话，会进行阻塞
        - select * from performance_schema.data_locks\G 可以查询加了什么锁

          - LOCK_TYPE

            - TABLE
            - RECORD 行锁

          - LOCK_MODE

            - IX
            - X, REC_NOT_GAP代表X型记录锁

          - LOCK_STATUS

            - GRANTED 锁已经被成功授予
            - WAITING 锁正在等待被授予
            - TIMEOUT 锁请求超时
            - KILLED 锁请求被终止
            - PENDING锁请求正在处理中
            - VICTIM 锁请求被选为死锁的牺牲者
            - ABORTED 锁请求被中止
            - PREEMPTED 锁请求被抢占

      - 查询的记录不存在

        - 临键锁退化为间隙锁

          - 如果id 为 1，5，6，要去查id为2
          - 那么如果不退化为间隙锁，会导致5被锁住，但实际上查不到5

    - 唯一索引范围查询

      - 大于或者大于等于

        - 大于

          - select * from id > 15 for update查出来只有 id为20的数据
          - 首先会加上(15,20]临键锁，因为查到了20
          - 由于20是唯一一条数据，那么也还会添加(20, +∞]

        - 大于等于

          - select * from id >= 15 for update查出来 id为15 和20的两条数据
          - 15会为记录锁
          - 会加上(15,20]临键锁，因为查到了20
          - 也还会添加(20, +∞]

      - 小于或者小于等于

        - 小于 且 查询条件不存在记录中

          - select * from id < 6 for update,查询出有id为 1， 5的 数据
          - 会加上(-∞,1],(1.5]的临键锁
          - 还会加上(5,10)的间隙锁，10的原因是：大于6的第一条数据的id为10

        - 小于 且 查询条件存在记录中

          - select * from id < 5 for update,查询出有id为 1的 数据
          - 会加上(-∞,1]的临键锁
          - 会加上(1.5)的间隙锁

        - 小于等于 且 查询条件存在记录中

          - select * from id <= 5 for update,查询出有id为 1， 5的 数据
          - 会加上(-∞,1],(1.5]的临键锁

    - 非唯一索引等值查询

      - 子主题 1

    - 非唯一索引范围查询

  - 死锁

    - innodb_lock_wait_timeout 默认50秒
    - 主动开启死锁检测 innodb_deadlock_detect，检测到之后 会有个error

- select的流程

  - server层

    - 1 连接器

      - show processlist 可以展示有多少个客户端在连接
      - 参数 wait_timeout 表示最大空闲时长
      - max_connections 最大连接数

    - 2 解析sql

      - 解析器

        - 词法分析
        - 语法分析

          - 判断sql语句是否符合语法

    - 3 执行sql

      - 预处理器

        - 检查sql查询语句中的表或者字段是否存在
        - select *  扩展为表上所有的列

      - 优化器

        - 优化器主要负责将sql查询语句的执行方案确定下来 可以用explain来查看

      - 执行器

        - 执行sql语句

          - 全文索引
          - 索引下推

            - 在索引经过> < 这种情况下失效时，会进行索引下推，判断对应的列的值是否属于这个范围
            - 如果无索引下推

              - 1 以select * from user where age > 20 and reward = 100000; 为例
              - 2 先去查到满足条件的第一条二级索引记录，这里是定位到age > 20的第一条记录
              - 3 到对应的二级索引B+树中去找到这条记录并获取id，然后执行回表操作，将记录返回给 Server层
              - 4 Server层判断失效的字段是否满足对应的条件，成立发给客户端，否则跳过该记录
              - 5 接着就是想存储引擎要下一条记录，然后回表操作，将完整的记录返回给Server层
              - 6 往复循环，直到存储引擎把表中数据读完

            - 有索引下推的话

              - 1 以select * from user where age > 20 and reward = 100000; 为例
              - 2 先去查到满足条件的第一条二级索引记录
              - 3 存储引擎根据二级索引的B+树 快速定位到这条数据，但先不回表！！ 而是先判断索引中包含的列的条件（这里是 reward列的条件）是否成立

                - 成立 执行回表操作，将数据返回给Server层
                - 不成立，则跳过这个索引记录

              - 4 此时 Server层会判断其他的查询条件（这里没有其他的条件） 是否成立，如果成立则会发给客户端，否则跳过该记录，向存储引擎索要下一条记录

  - 4 引擎层

- 索引下推

  - 每查到一条数据都需要进行回表，而不是查到所有的主键之后再回表
  - 下推指的就是将server层推到引擎层，让引擎层来进行数据筛选，
    如果没有下推，则会根据查找的数据，从引擎层传到server层，让server层来进行筛选，相当于是筛选了多遍
    下推的话，会讲所有的筛查步骤在存储引擎层完成，这样就会尽可能的少返回数据给server层，

    - 
    - 

### Redis

- 数据类型
- 持久化

  - aof

    - 
    - 重写机制

      - 
      - 重写机制的优点：

      - 后台重写

        - 
        - 写时复制

          - 1
          - 2
          - 3
            所以有两个步骤会阻塞主进程
            1） fork的时候 要复制页表
            2） 写时复制的时候，要拷贝物理内存

        - 为了解决在写时复制时，主进程正在修改key-value 出现数据不一致的问题

          - AOF重写缓冲区 追加的内容会写进去

  - rdb

    - 
    - 优点：恢复速度快
    - 缺点：快照频率不好掌控

      - 太高： 频繁创建子进程会带来额外的性能开销
      - 太低：两次快照间发生宕机，会丢失较多数据

  - aof+rdb

    - 
    - 开启后

      - 1 fork出来的子进程会将主进程共享的内存以RDB的方式写入到AOF中
      - 2 主线程的操作命令会写到重写缓冲区中
      - 3 完成后通知主进程将含有RDB格式和AOF格式的AOF文件替换掉旧的AOF文件

    - 优点

      - 重启Redis加载数据时，由于前半部分是RDB内容，这样加载会很快

- 高可用

  - 主从复制
  - 哨兵机制

- 缓存问题

  - 缓存击穿
  - 缓存穿透
  - 缓存雪崩
  - 缓存和Mysql数据一致性

### Mongo

## JVM

### 双亲委派机制

- 启动类加载器 Bootstrap ClassLoader
- 扩展类加载器 Extension ClassLoader
- 应用类加载器 Application ClassLoader
- 自定义类加载器

  - 可以破坏双亲委派机制

### 垃圾回收器

### JVM组成

- 
- 

  - 线程不共享

    - 程序计数器

      - 每个线程都会通过程序计数器记录当前要执行的字节码指令的地址
      - 程序计数器不会出现内存溢出，因为每个线程只存储了一个固定长度的内存地址

    - 栈

      - java虚拟机栈
      - 本地方法栈
      - 在HotSpot中只用了一种栈来保存方法信息
      - 栈帧组成

        - 局部变量表

          - 存放运行过程中所有的局部变量

        - 操作数栈

          - JVM执行指令的过程中用来存放临时数据的一块区域
          - 编译期的时候就可以确定操作数栈的最大深度，从而在执行时分配正确的内存大小

            - 

        - 帧数据

          - 动态链接

            - 

          - 方法出口

            - 

          - 异常表的引用

            - 

      - 栈内存溢出

        - 

          - -Xss 设置栈内存大小
          - -XX:ThreadStackSize=1024

  - 线程共享

    - 堆

      - 堆内存会溢出，并且会抛出outOfMemory
      - 三个值

        - use

          - 当前已经使用的堆内存

        - total

          - JVM已经分配的可用堆内存

            - 默认是系统内存的 1/ 64

              - -Xms

        - max

          - 可以分配的最大堆内存

            - 默认是系统内存的1/4

              - -Xmx

      - 可以利用arthas监控堆内存大小

        - dashboard -i

      - 随着堆中的对象在哪个多，当total可以使用的内存即将不足的时候，JVM会继续分配内存给堆
      - 是不是used == total == max时，堆内存就溢出了呢？

        - 不是，和垃圾回收器有关

      - 

        - 建议将-Xms 和 -Xmx设置成一样的，这样就可以total向JVM再次申请

      - 
      - 字符串常量池

        - 

    - 方法区

      - 类的元信息

        - InstanceKlass对象，在类的加载阶段完成
        - 存放类的基本信息，常量池 字段 方法
          虚方法表

      - 运行时常量池

        - 每个常量数据都可以通过内存地址快速访问
        - JDK7之前

          - 放在堆中 叫做永久代

        - JDK8及以后

          - 元空间，操作系统维护的直接内存中
          - 默认情况下 只要不超过操作系统承受上限，就可以一直分配

            - 不能超过操作系统内存的上限
            - -XX:MaxMetaspaceSize = 值 设置最大大小

## 设计模式

### 设计原则

- 单一职责原则

  - 目的是：各行其职，一个类负责一个职责
  - 注意事项

    - 降低类的复杂度，一个类只负责一项职责
    - 提高类的可读性，可维护性
    - 降低变更引起的风险
    - 如果类或者方法过于简单，可以考虑在代码级违反此原则，只有类中的方法数量足够少，可以在方法级别上保持单一职责原则

- 接口隔离原则

  - 客户端不需要依赖他不需要的接口

    - 错误依赖 B去实际上只要实现接口1的1 2 3方法，D去实现接口1的1 4 5方法。但上图这样子，B不得不去实现接口1的4 5 方法和D不得不实现接口1的2 3 方法
    - 正确依赖， B去实现接口1的1方法 和接口2的2 3方法，D去实现接口1的1方法和接口3的4 5方法

- 依赖倒转原则
  （Dependence Inversion Principle）

  - 高层模块不应该依赖于低层模块，都应该依赖其抽象

    - 比如要一个Person类要去实现receive方法
      而receive方法需要在一个接口中去让多个实现类实现（比如Email Weixin QQ等）

  - 抽象不应该依赖细节，细节应该依赖抽象

  - 依赖倒转的中心思想是面向接口编程

  - 实现方式

    - 通过接口传递实现依赖

      public class DependencyInversion {
          public static void main(String[] args) {
              ChangHongTV changHongTV = new ChangHongTV();
              OpenAndClose openAndClose = new OpenAndClose();
              openAndClose.openTv(changHongTV);
          }
      }

      public interface ITV {
          public void open();
      }

      public interface IOpenAndClose {
          public void openTv(ITV tv);
      }

      public class ChangHongTV implements ITV{
          @Override
          public void open() {
              System.out.println("open changhongtv");
          }
      }

      public class OpenAndClose implements IOpenAndClose{
          @Override
          public void openTv(ITV tv) {
              tv.open();
          }
      }

    - 通过构造器进行依赖传递

    - 通过setter方法进行依赖传递

  - 注意事项

    - 低层模块尽量都要有抽象类或者接口，或者两者都有，程序稳定性更好
    - 变量的声明类型是抽象类或者接口，这样我们的变量引用和实际对象间，就有一个缓冲层，有利于程序扩展和优化
    - 遵循里氏替换原则

- 里氏替换原则

  - 其实是告诉我们继承需要遵守什么规则

    - 含义是：父类中凡是已经实现好的方法，实际上是在设定规范和契约，虽然不强制要求所有的子类必须遵守这些契约，但如果子类对这些已经实现的方法任意修改，就会对继承体系造成破坏
    - 希望子类尽量不要去重写父类方法
    - 可以通过聚合，依赖，组合来代替继承

  - B继承A，但是重写了A中的方法,调用的时候是想调用A的方法，但实际上由于重写了，则调用了B

- 开闭原则（Open Close principal）

  - 一个软件实体，比如类，模块和函数应该对扩展开放（对于提供方），对修改关闭（针对于使用方）
  - 当软件需要变化时，尽量通过扩展软件实体的行为来实现变化，而不是通过修改已有的代码来实现

- 迪米特法则

  - 核心：降低类之间的耦合
  - 与直接的朋友通信

    - 直接的朋友指的是 类中出现的成员变量，方法参数，方法返回值中的类 

### 类的六大关系

- 依赖

  - 如果是类的成员属性
  - 如果是方法的返回类型
  - 是方法接受的参数类型
  - 方法中使用到的

- 泛化

  - 其实就是继承关系，是依赖关系的特例

- 实现
- 关联

  - 如果A类有B类的成员变量，那么A和B就关联了

- 聚合

  - 整体与部分的关系，但整体与部分能分开（比如用set方法）

- 组合

  - 整体与部分的关系，但整体与部分不能分开，比如在加载类的时候，直接new了对应的属性。
  - 在级联删除的时候，删除一个对象也会删除另一个对象，那么这两个对象的类就是组合关系

### 设计模式

- 装饰器模式

  - 就是对一个类进行二次开发

    - 比如IO的体系就是装饰器模式
    - 如果要创建一个新的Set类，可以去存储删掉的元素，这就可以通过装饰器模式去解决

      - 实现Set
      - 实现Set的所有方法

        - 可以通过构造器中传入不同的类型，以达到不同的结果
          比如当要让自定义的Set存储的元素有序，就可以传入TreeSet类型

- 迭代器模式

## 计算机网络

### TCP/IP四层结构

- 应用层

  - HTTP

    - 状态码

      - 1XX
      - 2XX
      - 3XX

        - 客户端请求的资源发生了变动，需要客户端用心的URL重新发送请求获取资源，也就是 重定向
        - 301

          - 永久重定向

        - 302 Found

          - 临时重定向 表示请求的资源还在 但暂时要用另一个URL访问

        - 304 Not Modified

          - 不具有跳转的含义 表示资源未修改

      - 4XX

        - 400

          - 客户端请求报文有误 但是是笼统的错误

        - 403

          - 表示服务器禁止访问资源

        - 404

          - 无法提供给客户端

        - 客户端发送的报文有误

      - 5XX

        - 属于服务端的错误码
        - 500

          - 服务内部错误

        - 501 Not Implement
        - 502 Bad GateWay
        - 503 service Unavailable 

          - 服务器暂忙 无法响应

    - GET 与 POST

      - GET

        - 从服务器获取指定的资源
        - 安全且幂等，可被缓存，因为是只读操作
        - 可以对GET请求的数据做缓存，这个缓存可以做到浏览器本身上，也可以做到代理上（如Nginx）

      - POST

        - 根据请求负荷 对指定的资源做出处理

    - 缓存技术

      - 出现原因：对于一些具有重复性的HTTP请求，比如每次请求的数据都一样的，就可以把这对 请求- 响应的数据都缓存在本地

    - 特性

      - HTTP1.1

        - 简单，灵活，易于扩展 应用广泛和跨平台
        - 缺点：无状态 明文传输 不安全
        - 长连接

  - HTTPS
  - WebSocket

    - 
    - 

- 传输层

  - TCP
  - UDP

- 网络层

  - IP

- 网络接口层
- 

### Session Cookie Token

- Session 存储在服务端 放在内存中 不利于分布式 相当于会员卡对应的内部信息
  Cookie 相当于是传递Session的媒介 相当于会员卡
  Token 是服务端生成的 但是在客户端进行保存的 客户端传来token 服务端就会解析这个token 与数据库的信息匹配 天然利于分布式 相当于是加密的会员卡

