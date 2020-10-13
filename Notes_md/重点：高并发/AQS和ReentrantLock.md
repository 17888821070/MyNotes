AQS

## CLH同步队列

### 简介

> CLH同步队列是一个先进先出的同步队列，AQS依赖它完成同步状态的管理
>
> * 线程获取同步状态失败是，AQS将当前线程和等待状态等构建成一个Node节点，将其加入到同步队列
> * 公平锁：同步状态，将首结点唤醒，使其再次尝试获取同步状态

### [Node](# Node代码) 

> 属性 :
>
> ​	线程引用：表示想要竞争锁资源的线程
>
> ​	等待状态 waitStatus：0默认状态，-1唤醒下一节点，
>
> ​	前驱节点
>
> ​	后继节点
>
> ![img](img\aa686deba521.png)

### 插入节点

> 使用cas操作，在队列末端插入新的节点 addWaiter(Node mode)方法

```java
 1: private Node addWaiter(Node mode) {
 2:     // 新建节点
 3:     Node node = new Node(Thread.currentThread(), mode);
 4:     // 记录原尾节点
 5:     Node pred = tail;
 6:     // 快速尝试，添加新节点为尾节点
 7:     if (pred != null) {
 8:         // 设置新 Node 节点的尾节点为原尾节点
 9:         node.prev = pred;
10:         // CAS 设置新的尾节点
11:         if (compareAndSetTail(pred, node)) {
12:             // 成功，原尾节点的下一个节点为新节点
13:             pred.next = node;
14:             return node;
15:         }
16:     }
17:     // 失败，多次尝试，直到成功
18:     enq(node);
19:     return node;
20: }
```

### 出列

> 出列一次只有一个线程可以执行，所以不需要cas操作

```java
private void setHead(Node node) {
    head = node;
    node.thread = null;
    node.prev = null;
}
```



## ReentrantLock从获取锁到释放锁

> 属性：NonFairSync  继承自AQS
>
> ​			state：线程状态，0没有线程占用，1有线程占用
>
> ​			head：同步双向队列首，默认为null
>
> ​			tail：同步双向队列尾，默认为null
>
> ​			execlusiveOwnerThread：当前持有锁的线程

## 新线程获取锁的流程

#### 1、state==0 && tail==null

> 1. 使用cas操作（cas(0,1)）成功，将state置为1，execlusiveOwnerThread置为当前线程
>
> 结果：![img](img\29922b031de1.png)

#### 2、state==1 && tail==null

> Thread-1 执行了
>
> 1. CAS 尝试将 state 由 0 改为 1，结果失败
> 2. 进入 tryAcquire 逻辑，这时 state 已经是1，结果仍然失败
> 3. 接下来进入 addWaiter 逻辑，构造 Node 队列 
>
> - - 创建Node【thread = null，waitstate=0】，插入到队尾
>   - 创建Node【thread = Thread-1，waitStatus=0】,插入到队尾
>
> ![img](img\a0d9888aa38f.png)
>
>  4. 线程Thread-1进入自旋获取锁逻辑
>
>      1. 自旋尝试获得锁 casstate(0,1)
>
>         <font color="red">备注：如果获取成功了就直接设置为无效节点，此线程就非公平的枪了锁【非公平锁发生场景1】</font>
>
>      2. 失败后将前一节点的waitstatus修改为-1
>
>         
>
>         ![img](img\b7ed749d9a0d.png)
>
>      3. 再次自旋尝试获得锁 casstate(0,1)
>
>         <font color="red">备注：如果获取成功了就直接设置为无效节点，此线程就非公平的枪了锁【非公平锁发生场景2】</font>
>
>      4. 失败，发现waitstatus已经为-1，park()阻塞线程
>
>         ![img](img\ee560216924f.png)

### 3、state==1 && tail!=null

> 与2的情况类似，只是不需要创建Node【thread=null】
>
> ![img](img\dbcb23b0d739.png)

## 释放锁的流程

#### 1、tail==null

> state=1
>
> exclusiceOwnerThread = null

#### 2、tail!=null，node.waitStatus==-1

> 1. 找到离head最近的一个有效的Node
>
> 2. unpark唤醒阻塞的线程
> 3. 此线程cas尝试获取锁
>    1. 成功：
>       1. exclusiceOwnerThread = 当前线程
>       2. state=1
>       3. 将head.next指向此Node，Node.thread=null
>    2. 失败（<font color="red">有新来的线程把所资源抢走了【非公平锁发生场景3】</font>）
>       1. 再次进入自旋获取锁的流程

































#### Node代码

```java
static final class Node {

    // 共享
    static final Node SHARED = new Node();
    // 独占
    static final Node EXCLUSIVE = null;

    /**
     * 因为超时或者中断，节点会被设置为取消状态，被取消的节点时不会参与到竞争中的，他会一直保持取消状态不会转变为其他状态
     */
    static final int CANCELLED =  1;
    /**
     * 后继节点的线程处于等待状态，而当前节点的线程如果释放了同步状态或者被取消，将会通知后继节点，使后继节点的线程得以运行
     */
    static final int SIGNAL    = -1;
    /**
     * 节点在等待队列中，节点线程等待在Condition上，当其他线程对Condition调用了signal()后，该节点将会从等待队列中转移到同步队列中，加入到同步状态的获取中
     */
    static final int CONDITION = -2;
    /**
     * 表示下一次共享式同步状态获取，将会无条件地传播下去
     */
    static final int PROPAGATE = -3;

    /** 等待状态 */
    volatile int waitStatus;

    /** 前驱节点，当节点添加到同步队列时被设置（尾部添加） */
    volatile Node prev;

    /** 后继节点 */
    volatile Node next;

    /** 等待队列中的后续节点。如果当前节点是共享的，那么字段将是一个 SHARED 常量，也就是说节点类型（独占和共享）和等待队列中的后续节点共用同一个字段 */
    Node nextWaiter;
    
    /** 获取同步状态的线程 */
    volatile Thread thread;

    final boolean isShared() {
        return nextWaiter == SHARED;
    }

    final Node predecessor() throws NullPointerException {
        Node p = prev;
        if (p == null)
            throw new NullPointerException();
        else
            return p;
    }

    Node() { // Used to establish initial head or SHARED marker
    }

    Node(Thread thread, Node mode) { // Used by addWaiter
        this.nextWaiter = mode;
        this.thread = thread;
    }

    Node(Thread thread, int waitStatus) { // Used by Condition
        this.waitStatus = waitStatus;
        this.thread = thread;
    }
    
}
```

