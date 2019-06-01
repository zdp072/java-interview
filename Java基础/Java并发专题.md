## 并行和并发有什么区别？
1. 并行：多核处理器同时处理多个任务
2. 并发：多个任务在一个CPU核上，交替的执行(多个线程竞争共享资源)

## 线程和进程有啥区别？
一个程序下至少有一个进程，一个进程下可以有多个线程提高程序的执行速度。

## 什么是守护线程？
守护线程是运行在程序后台的一种特殊线程，它独立于控制终端并且周期性的执行某种任务或等待处理某些发生的时间。
在java中垃圾回收线程就是守护线程。

## 线程的基本方法
#### wait
调用该方法的线程进入等待状态，只有等待另外线程的通知才会返回，需要注意的是调用wait()方法后，会释放对象的锁。
因此wait方法一般用在同步方法或同步代码块中。

#### sleep
sleep导致当前线程休眠，与wait方法不同的是sleep不会释放当前线程占有的锁

#### yield
yield会使当前线程让出CPU执行时间片，与其他线程一起重新竞争CPU时间片

#### interrupt
中断一个线程，给线程一个通知信号，仅仅会改变这个线程内部的一个中断标记为。运行的线程本身并不会因此而被阻塞或终止。

#### join
在main线程中调用一个线程的join方法，则main线程转为阻塞状态，直到这个线程结束。
在很多情况下，主线程生成并启动了子线程，需要用到子线程返回的结果，也就是主线程需要在子线程结束后再结束。

#### notify
唤醒监视器上等待的单个线程，如果所有线程都在此对象上等待，则会选择唤醒其中的一个线程，选择是任意的。

#### notifyAll
唤醒监视器上等待的所有线程。

## 线程生命周期中有哪些状态？
#### 新建（New）
当程序使用new关键字创建了一个线程，该线程就处于新建状态，此时jvm为其分配好了内存，并初始化了成员变量的值。

#### 就绪（Runnable）
当线程对象调用了start()方法之后，该线程就处于就绪状态，此时jvm会为其创建方法调用栈和程序计数器，等待CPU调度。

#### 运行（Running）
当处于就绪状态的线程获得了CPU，开始执行run()方法中的方法体，该线程便处于运行状态。

#### 阻塞（Blocked）
阻塞状态是指线程由于某种原因暂时停止运行。阻塞分为以下三种：
1. 等待阻塞(wait -> 等待队列)：运行的线程调用wait()方法，则jvm会把该线程放入等待队列。
2. 同步阻塞(lock -> 锁池)：运行的线程在获取对象的同步锁，若该同步锁被别的线程占用，则jvm会把该线程放入锁池。
3. 其他阻塞(sleep、join)：运行的线程执行Thread.sleep(ms)或t.join()方法，则jvm会把该线程置为阻塞状态，当sleep超时或join等待的线程结束，线程重新转入可运行状态。

#### 死亡（Dead）
线程死亡有三种方式：
1. run()或call()方法执行完成，线程正常结束。
2. 线程抛出一个未被捕获的Exception或Error。
3. 直接调用该线程的stop()方法来结束线程。

## 多个线程顺序执行的几种方法
1. 信号量Sephmore
2. 使用ReentrantLock
3. wait/notify

## 一个线程会因为哪些原因放弃CPU:
1. Java虚拟机让当前线程暂时放弃CPU，转到就绪态，使其他线程获得运行机会
2. 当前线程因为某些原因而处于阻塞状态
3. 线程运行结束

## 什么是死锁？
死锁的场景一般是线程A和线程B都在互相等待对方释放锁。

## 如何避免死锁
1. 加锁顺序（线程按照一定的顺序加锁，针对转账的场景，需要同时申请两个锁的方法中，总是以相同的顺序来申请锁，比如总是先申请id大的账户上的锁 ，然后再申请id小的账户上的锁）

2. 加锁时限（线程尝试获取锁的时候加上一定的时限，超过时限则放弃对该锁的请求，并释放自己占有的锁，synchronized不具备这个功能，但是我们可以使用Lock类中的tryLock方法去尝试获取锁，这个方法可以指定一个超时时限，在等待超过该时限之后便会返回一个失败信息。）

## synchronized关键字原理

1. synchronized有三种使用方法
同步普通方法，锁的是当前对象；
同步静态方法，锁的是class对象；
同步代码块，锁的是（）中的对象；

2. 同步的本质是从对象监视器Monitor中获取enter指令，而这个获取过程具有排他性从而达到同一时刻只能有一个线程访问的目的。注:同步方法调用前会加入一个enter指令，在推出方法或异常处会插入exit指令。

## synchronized锁升级的原理是什么？
在锁对象的对象头里面有一个threadid，在第一次访问的时候threadid为空，jvm让其持有偏向锁，并将threadid设置为线程id，线程再次进入的时候会先判断threadid是否与其线程id一致，如果不一致则升级偏向锁为轻量级锁，轻量级锁通过自旋循环一定次数来获取锁，执行一定次数之后，如果还没有正常获取到锁，此时锁从轻量级升级为重量级锁。
锁升级是为了降低锁带来的性能消耗。

无锁 -> 偏向锁 -> 轻量级锁 -> 重量级锁

## synchronized和volatile的区别是什么？
1. volatile是变量修饰符；synchronized可以修饰方法、代码段
2. volatile仅能实现变量修改的可见性，不能保证原子性；synchronized可以保证变量修改的可见性和原子性
3. volatile不会造成线程阻塞；synchronized会造成线程阻塞

## 什么是原子性？
某个线程正在做某个具体业务时，中间不可以被加塞或者被分割。需要整体完整，要么同时成功，要么同时失败。

## 什么是CAS，在什么场景下使用？
CAS：Compare and set，是一种乐观锁的实现，在set的时候，加上初始条件做比对。

通常使用CAS可以解决高并发时数据一致性问题，只需要在进行set操作时，compare一下初始值，如果初始值变换，不允许set成功。
业务1执行 => UPDAtE t_yue SET money=28 WHERE uid=$uid AND money=100
业务2执行 => UPDAtE t_yue SET money=38 WHERE uid=$uid AND money=100
这两个操作同时进行时，只能有一个执行成功

## cas的底层原理是什么？谈谈你对unsafe的理解
1. cas中的unsafe类（位于sum.misc包）相当于一个后门，基于该类可以直接操作特定内存的数据（native），其内部方法可以像C的指针一样直接操作内存。
2. getAndAddInt使用了自旋的方式，do.while循环的进行compare，比较当前工作内存中的值和主内存中的值，如果相同则执行相应操作。

## cas的缺点是什么？
1. 自旋如果循环时间长，对cpu开销较大
2. 只能保证一个共享变量的原子操作
3. 引出ABA问题

## ABA问题，如何解决
并发1在修改数据时，虽然是A，但已经不是初始条件的A了，中间发生了A变B，B又变A的变化，此A非彼A，数据却成功修改，可能导致错误，这就是CAS引发的ABA问题。

导致ABA的原因是CAS过程中只简单进行了值的校验，有些场景不能仅仅比对值，还必须确保是原来的数据，才能修改成功
常用的方式是通过版本号来比对，一个数据一个版本，如果版本变化了，即使值相同，也不应该修改成功。


## i++是线程安全的吗?
自增操作不是线程安全的。i++可以分成三个操作：
1.  执行getfield原始值
2.  执行iadd进行加1操作
3.  执行putfield把累加后的值写回主存

假设A，B线程都执行了加1操作，在第三步A线程被阻塞，B先同步至主内存，A再同步回主内存，就会出现数据被覆盖的情况。

## 自增操作如何解决原子性问题？
1. 使用synchronized
2. 使用AtomicInteger

## 浅谈AtomicInteger实现原理
AtomicInteger是对int的封装，提供原子性的访问和更新操作，其原子性操作的实现是基于CAS。

CAS的过程是这样，执行运算时，使用当前数据值作为判断条件，利用CAS指令试图进行更新。更新之前获取内存中的最新值，与传来的当前值作比较。如果数值没有变，则说明没有其他线程进行并发修改，更新操作成功。

AtomaticInteger最典型的应用场景是计数。

## volatile关键字的原理及作用？
volatile是java虚拟机提供的轻量级的同步机制，保证可见性，不保证原子性，禁止指令重排。

每个线程都有自己的工作内存（本地高速缓存），同时还有一个共享的主内存。由于本地工作内存的存在，多个线程并发读取一个共享变量的时候，有可能某个线程修改了变量的值，但是其他线程看不到，也就是对其他线程不可见。

假设共享变量data前面加了volatile修饰的话，那么线程A只要修改了data变量的值，就会在修改完自己本地工作内存的data变量值之后，强制将整个data变量最新的值刷回主内存，主内存里data变量的值立马就编程了最新的值。如果此时别的线程的工作内存中有data变量的本地缓存的话，会强制让其他线程的工作内存中data变量缓存直接过期失效，强制从主内存读取最新的值。

#### 变量可见性
这里的可见性是指当一个线程改变了变量的值，那么新的值对其他所有线程是可以立刻获取的。

#### 禁止重排序
volatile禁止了指令重排

指令重排序：处理器为了提高程序运行效率，可能会对输入代码进行优化，它不保证程序中各个语句的执行先后顺序同代码中的顺序一致。重排序过程不会影响到单线程程序的执行，但是会影响到多线程并发执行的正确性。

## volatile有哪些应用场景
volatile主要解决的是一个线程修改完值之后，其他线程立马可以督导最新的值。

适合在读多写少的场景，它保证了可见性和有序性，但不保证原子性。如果多个线程同时修改一个变量的值，那还是可能出现多线程并发的安全问题，导致数据值修改错乱，volatile是不负责解决这个问题的。

单例模式：双重判空加锁不一定线程安全，原因是有指令重排序的存在，加入volatile可以禁止指令重排。
instance = new SingletonDemo(); 可以分成以下三步完成
1.分配对象内存空间
2.初始化对象
3.设置instance指向刚分配的内存地址，此时instance != null
步骤2和步骤3不存在数据依赖关系，可能会出现重排优化


## 对happens before的理解？
Happens-before指的是前一个操作的结果对后续操作是可见的

Happens-Before的语义本质上是一种可见性，A Happens-Before B 意味着A事件对B事件来说是可见的，无论A事件和B 事件是否发生在同一个线程里。例如A事件发生在线程1上，B事件发生在线程2上，Happens-Before规则保证线程2上也能看到、A事件的发生。

#### 程序顺序规则

如果多个操作之间有先后依赖关系，则不允许对这些操作进行重排序
如以下：A和B可以交换顺序，但AC或BC不能交换

```
int i = 0；//步骤A
int j = 1; //步骤B
int x = i + j;//步骤C
```

#### 锁定规则
使用ReentrantLock或synchronized可以确保同一时刻只能有一个线程执行锁中的操作

根据锁定原则，线程A中对value变量的修改，可以被线程B感知到
```
class HappensBeforeLock {
    private int value = 0;
    
    public synchronized void setValue(int value) {
        this.value = value;
    }
    
    public synchronized int getValue() {
        return value;
    }
}
```

#### volatile变量规则
volatile变量的写操作对于读操作是可见的

#### 线程启动规则
主线程A启动子线程B,子线程B能够看到主线程在启动子线程B前的操作.

#### 线程结束规则
当一个线程结束时，会把自己所有操作的结果都同步到主内存。而任何其它线程当发现这个线程已经执行结束了，就会从主内存中重新刷新最新的变量值。

在线程s中调用t.join()方法。则线程s会被挂起，等待t线程运行结束才能恢复执行。当t.join()成功返回时，s线程就知道t线程已经结束了。所以根据本条原则，在t线程中对共享变量的修改，对s线程都是可见的。

#### 中断规则
假设两个线程A和B，A先做了一些操作operationA，然后调用B线程的interrupt方法。当B线程感知到自己的中断标识被设置时(通过抛出InterruptedException，或调用interrupted和isInterrupted),operationA中的操作结果对B都是可见的。

#### 终结器规则
一个对象的构造函数必须在它的finalize()方法调用时执行完。
根据这条原则，可以确保在对象的finalize方法执行时，该对象的所有field字段值都是可见的。

#### 传递性规则
如果操作A Happens-Before B，B Happens-Before C，那么可以得出操作A Happens-Before C。

## Java中的阻塞队列
阻塞队列一般有以下两种情况：
1. 当队列中没有数据的情况下，消费者端的所有线程会被自动阻塞（挂起），直到有数据放入队列。
2. 当队列中填满数据的情况下，生产者端的所有线程会被自动阻塞（挂起），直到队列中有空的位置，线程会被自动唤醒。

#### ArrayBlockingQueue 由数组结构组成的有界阻塞队列
此队列按照先进先出的原则对元素进行排序，默认情况下不保证访问者公平的访问队列。
所谓的公平访问队列是指先阻塞的生产者线程，可以先往队列里插入元素，先阻塞的消费者线程，可以先从队列里获取元素。
通常情况下保证公平性会降低吞吐量。

#### LinkedBlockingQueue 由链表结构组成的有界阻塞队列
同ArrayBlockingQueue，此队列按照先进先出的原则对元素进行排序，

#### PriorityBlockingQueue 支持优先级排序的无界阻塞队列
是一个支持优先级的无界队列，默认情况下元素采取自然顺序升序排序。
可以自定义实现compareTo()方法来指定元素进行排序规则。

#### DelayQueue 使用优先级队列实现的无界阻塞队列
是一个支持延时获取元素的无界阻塞队列，在创建元素时可以指定多久才能从队列中获取当前元素，只有在延迟期满时才能从队列中提取元素。

应用场景：
1. 缓存系统的设计：可以用DelayQueue保存缓存元素的有效期，使用一个线程循环查询DelayQueue，一旦获取到了元素表示缓存到期
2. 定时任务调度：使用DelayQueue保存当前将会执行的任务和执行时间，使用一个线程循环查询DelayQueue，一旦获取到了任务就开始执行。

#### SynchronousQueue 不存储元素的阻塞队列
是一个不存储元素的阻塞队列，每一个put操作必须等待一个take操作，否则不能继续添加元素。
SynchronousQueue可以看成是一个传球手，非常适合传递性场景，比如一个线程中使用的数据，传递给另外一个线程使用。

#### LinkedTransferQueue 由链表结构组成的无界阻塞队列
相比于其他阻塞队列，LinkedTransferQueue多了tryTransfer和transfer方法

#### LinkedBlockingDeque 由链表结构组成的双向阻塞队列
所谓的双向队列指的是你可以从队列的两端插入和移出元素。
双向队列因为多了一个操作队列的入口，在多线程同时入队时，也就减少了一半的竞争。

## 如何使用阻塞队列实现一个生产者和消费者？
选择一个阻塞队列实现即可。

## 什么是有界队列？什么是无界队列？
有界队列就是有固定大小的队列，比如设定了固定大小的LinkedBlockingQueue，又或者大小为0，只是在生产者和消费者中做中转用的SynchronousQueue。

无界队列指的是没有固定大小的队列，这些队列的特点是可以直接入队，直到溢出。没有设定固定大小的LinkedBlockingQueue它的默认大小为Integer.MAX_VALUE，在使用者的体验上，相当于无界。

## 读多写少的场景应该使用哪个并发容器？为什么？
使用CopyOnWriteArrayList，内部持有一个ReentrantLock lock = new ReentrantLock();
利用了读写分离的思想，当写线程写入数据的时候会复制新建一个新容器，当数据更新完成后，再将旧容器引用指向新容器。读线程会感知到数据更新是延时的，也就是说COW是牺牲了数据的实时性而保证数据的最终一致性。由于写线程写数据是在新容器写入的，因此读线程不会被阻塞。COW适合读多写少的场景。

## 什么是CopyOnWrite容器
CopyOnWrite容器即写时复制的容器。通俗的理解是当我们往一个容器添加元素的时候，不直接往当前容器添加，而是先将当前容器进行Copy，复制出一个新的容器，然后新的容器里添加元素，添加完元素之后，再将原容器的引用指向新的容器。这样做的好处是我们可以对CopyOnWrite容器进行并发的读，而不需要加锁，因为当前容器不会添加任何元素。所以CopyOnWrite容器也是一种读写分离的思想，读和写不同的容器。CopyOnWriteArrayList是线程安全的，CopyOnWriteArraySet是利用CopyOnWriteArrayList来实现的，也是线程安全的

## 什么是跳表SkipList
对于一个单链表，即使链表是有序的，如果我们想要在其中查找某个数据，也只能从头到尾遍历链表，这样效率自然就会很低。假如我们对链表每两个结点提取一个结点到上一级，然后建立一个索引指向原始结点。其实这里利用了二分法的原理

## 读写锁可以用于什么场景？
读写锁维护了一对锁，一个读锁，一个写锁。读写锁在同一时刻允许多个线程进行读操作，但是写线程访问过程中，所有的读线程和其他写线程均被阻塞。如此，并发性有了很大的提升。这样，在某些读远远大于写的场景中，读写锁能够提供比排它锁更好的并发量和吞吐量。

## 什么场景下可以使用volatile替换synchronized？
Volatile变量具有synchronized的可见性特性，但是不具备原子性
#### 状态标志
实现volatile变量的规范使用仅仅是使用一个布尔状态标志，用于指示发生了一个重要的一次性事件，例如完成初始化或请求停机。
这种类型的状态标记的一个公共特性是：通常只有一种状态转换；shutdownRequested标志从false转换为true，然后程序停止。

```
volatile boolean shutdownRequested;
 
public void shutdown() { 
    shutdownRequested = true; 
}
 
public void doWork() { 
    while (!shutdownRequested) { 
        // do stuff
    }
}
```

#### 一次性安全发布
在缺乏同步的情况下，可能会遇到某个对象引用的更新值（由另一个线程写入）和该对象状态的旧值同时存在。
对象引用在没有同步的情况下进行读操作，产生的问题是您可能会看到一个更新的引用，但仍然会通过该引用看到不完全构造的对象。

```
//注意volatile！！！！！！！！！！！！！！！！！  
private volatile static Singleton instace;   
  
public static Singleton getInstance(){   
    //第一次null检查     
    if(instance == null){            
        synchronized(Singleton.class) {    //1     
            //第二次null检查       
            if(instance == null){          //2  
                instance = new Singleton();//3  
            }  
        }           
    }  
    return instance;        
```

#### 独立观察
定期 “发布” 观察结果供程序内部使用。
如下代码展示了身份验证机制如何记忆最近一次登录的用户的名字。将反复使用lastUser 引用来发布值，以供程序的其他部分使用。

```
public class UserManager {
    public volatile String lastUser; //发布的信息
 
    public boolean authenticate(String user, String password) {
        boolean valid = passwordIsValid(user, password);
        if (valid) {
            User u = new User();
            activeUsers.add(u);
            lastUser = user;
        }
        return valid;
    }
} 
```

#### volatile bean
很多框架为易变数据的持有者（例如 HttpSession）提供了容器，但是放入这些容器中的对象必须是线程安全的。
```
@ThreadSafe
public class Person {
    private volatile String firstName;
    private volatile String lastName;
    private volatile int age;
 
    public String getFirstName() { return firstName; }
    public String getLastName() { return lastName; }
    public int getAge() { return age; }
 
    public void setFirstName(String firstName) { 
        this.firstName = firstName;
    }
 
    public void setLastName(String lastName) { 
        this.lastName = lastName;
    }
 
    public void setAge(int age) { 
        this.age = age;
    }
}
```
#### 开销较低的“读－写锁”策略
如果读操作远远超过写操作，您可以结合使用内部锁和 volatile 变量来减少公共代码路径的开销。
如下显示的线程安全的计数器，使用synchronized确保增量操作是原子的，并使用volatile保证当前结果的可见性。如果更新不频繁的话，该方法可实现更好的性能，因为读路径的开销仅仅涉及 volatile 读操作，这通常要优于一个无竞争的锁获取的开销。

使用锁进行所有变化的操作，使用 volatile 进行只读操作。其中，锁一次只允许一个线程访问值，volatile允许多个线程执行读操作

```
@ThreadSafe
public class CheesyCounter {
    // Employs the cheap read-write lock trick
    // All mutative operations MUST be done with the 'this' lock held
    @GuardedBy("this") private volatile int value;
 
    //读操作，没有synchronized，提高性能
    public int getValue() { 
        return value; 
    } 
 
    //写操作，必须synchronized。因为x++不是原子操作
    public synchronized int increment() {
        return value++;
    }
}
```
https://blog.csdn.net/vking_wang/article/details/9982709

## 说下创建线程的不同方式？你倾向于使用哪种方式，并说明原因。
1. 继承Thread类
2. 实现Runnable接口
3. 通过Executor框架创建线程池

使用线程池，线程是一个比较重的对象，频繁的创建和销毁消耗性能。

## 如何在两个线程间共享数据
1. 如果每个线程执行的代码相同，可以使用同一个Runnable对象
2. 如果每个线程执行的代码不同，这时候需要用不同的Runnable对象

## 说说ContDownLatch的原理（倒计时门阀）
让一个或多个线程持续等待，直到其他多线程执行的一组操作全部完成以后，这些等待的线程才会继续执行。

## 说说CyclicBarrier的原理（循环栅栏）
可以把它理解成汽车站人满发车的场景。
通过它可以实现让一组线程等待至某个状态之后再全部同时执行。
可以用于多线程计算数据，最后合并计算结果的场景。

## 说下CountDownLatch和CyclicBarrier的区别
- CyclicBarrier就是一个栅栏，等待所有线程到达后再执行相关的操作。barrier 在释放等待线程后可以重用。
- CountDownLatch是计数器, 线程完成一个就记一个, 就像报数一样, 只不过是递减的，当计数器值到达0时，它表示所有的线程已经完成了任务

## 说说Semaphore的原理（许可证管理器）
Semaphore是一种基于计数的信号量，它可以设置一个阀值。多个线程竞争获取许可信号，做完自己的申请后归还，超过阀值后，线程申请许可将会被阻塞。
通常用来控制同时访问某些资源的线程数量，或实现互斥锁（计数器为1），semaphore基本可以完成ReentrantLock的所有工作。

## 说说Exchanger的原理
线程通过exchange方法将一个对象实体用于交换，将自己的交换数据给对方，并从对方获取交换数据。
Exchanger可以看做一个双向的SynchronousQueue。

## ThreadLocal原理分析
ThreadLocal提供了线程本地变量，它可以保证访问到的变量属于当前线程，每个线程都保存一个变量副本。
ThreadLocal相当于将线程隔离。

## 线程池的几种方式
在Executors类里面提供了一些静态工厂，生成一些常用的线程池。
1. newSingleThreadExecutor
只有一个线程在工作，如果这个唯一的线程因为异常结束，那么会有一个新的线程来替代它。

2. newCachedThreadPool
可缓存的线程池，线程池大小完全依赖于jvm能够创建的最大线程大小

3. newFixedThreadPool
固定大小的线程池，其大小达到最大值就会保持不变。

## 线程池的原理是什么，底层几个参数是什么意思？
底层使用ThreadPoolExecutor实现
线程池的作用: 线程复用、控制最大并发数

#### corePoolSize: 核心线程池数量
在线程数少于核心数量时，有新任务进来就新建一个线程，即使有的线程没事干
等超出核心数量后，就不会新建线程了，空闲的线程就得去任务队列里取任务执行了

#### maximumPoolSize: 指定了线程池中的最大线程数量
包括核心线程池数量 + 核心以外的数量
如果任务队列满了，并且池中线程数小于最大线程数，会再创建新的线程执行任务

#### keepAliveTime: 核心池以外的线程存活时间，即没有任务的外包的存活时间 
如果给线程池设置 allowCoreThreadTimeOut(true)，则核心线程在空闲时头上也会响起死亡的倒计时
如果任务是多而容易执行的，可以调大这个参数，那样线程就可以在存活的时间里有更大可能接受新任务

#### unit: keepAliveTime的单位

#### workQueue: 任务队列
被提交但尚未被执行的任务，不同的任务类型有不同的选择

#### threadFactory: 线程工厂
用于常见线程，一般用默认的即可

#### handler: 拒绝策略
当任务太多来不及处理，如何拒绝任务
1. CallerRunsPolicy：只要线程池没关闭，就直接用调用者所在线程来运行任务，显然这样做不会真的丢弃任务。
2. AbortPolicy：直接抛出RejectedExecutionException异常，阻止系统正常运行
3. DiscardPolicy：丢弃无法处理的任务，不予任何处理。如果允许任务丢失，这是最好的一种方案。
4. DiscardOldestPolicy：把队列里待最久的那个任务扔了，然后再调用execute()尝试再次提交当前任务
5. 我们也可以实现自己的RejectedExecutionHandler接口自定义策略，比如如记录日志什么的

## 线程池工作过程
1. 线程池刚创建时，里面没有一个线程。任务队列是作为参数传进来的。不过，就算队列里面有任务，线程池也不会马上执行它们。
2. 当调用 execute() 方法添加一个任务时，线程池会做如下判断:
3. 如果正在运行的线程数量小于corePoolSize，那么马上创建线程运行这个任务;
4. 如果正在运行的线程数量大于或等于corePoolSize，那么将这个任务放入队列;
5. 如果这时候队列满了，而且正在运行的线程数量小于maximumPoolSize，那么还是要创建非核心线程立刻运行这个任务;
6. 如果队列满了，而且正在运行的线程数量大于或等于maximumPoolSize，那么线程池会抛出异常 RejectExecutionException。
7. 当一个线程完成任务时，它会从队列中取下一个任务来执行。当一个线程无事可做，超过一定的时间(keepAliveTime)时，线程池会判断，如果当前运行的线程数大于 corePoolSize，那么这个线程就被停掉。所以线程池的所有任务完成后，它最终会收缩到 corePoolSize 的大小。

## 如何设置线程池大小
1. CPU密集型：N * CPU + 1 
2. IO密集型：2N * CPU

## 为啥线程池不建议使用Executors去创建
线程池不允许使用Executors去创建，而是通过ThreadPoolExecutor的方式，这样的处理的目的是为了让我们更加明确线程池的运行规则，规避资源耗尽的风险。 Executors各个方法的弊端如下：

newFixedThreadPool和newSingleThreadExecutor: 主要问题是堆积的请求处理队列可能会耗费非常大的内存，甚至OOM。

newCachedThreadPool和newScheduledThreadPool: 主要问题是线程数最大数是Integer.MAX_VALUE，可能会创建数量非常多的线程，甚至OOM。

## 如何保证多线程编程中的线程安全
1. 使用synchronized关键字
2. 使用java.util.concurrent.atomic包中的原子类（AtomicInteger）
3. 使用java.util.concurrent.locks包中的锁
4. 使用线程安全的集合ConcurrentHashMap
5. 使用volatile关键字，保证变量可见性

## 线程之间是如何进行通信的？
1. 使用synchronized同步，这种方式本质上就是“共享内存”式的通信。多个线程需要访问同一个共享变量，谁拿到了锁（获得了访问权限），谁就可以执行
2. while轮询，这种方式会浪费CPU资源
3. wait/notify机制
4. 生产者/消费者模式

https://blog.csdn.net/Hadwin1991/article/details/73527835

## 如何根据cpu核数来设计线程大小
对于计算密集型的任务，在拥有N个处理器的系统上，当线程池的大小为N+1时，通常能实现最优的效率

## synchronized和ReentrantLock的区别
#### 相同点
1. 都是用来协调多线程对共享对象的访问
2. 都是可重入锁，同一线程可以多次获得同一个锁
3. 都保证了可见性和互斥性

#### 不同点
1. ReentrantLock显示的获得、释放锁，synchronized 隐式获得释放锁
2. ReentrantLock是API级别的，synchronized是JVM级别的
3. ReentrantLock可以实现公平锁
4. Lock是一个接口，而synchronized是Java中的关键字，synchronized是内置的语言实现。
5. synchronized在发生异常时，会自动释放线程占有的锁，因此不会导致死锁现象发生;而Lock在发生异常时，如果没有主动通过unLock()去释放锁，则很可能造成死锁现象，因此使用 Lock 时需要在 finally 块中释放锁。
6. Lock可以让等待锁的线程响应中断，而synchronized却不行，使用synchronized时等待的线程会一直等待下去，不能够响应中断。
7. 通过Lock可以知道有没有成功获取锁，而synchronized却无法办到。
8. Lock可以提高多个线程进行读操作的效率，既就是实现读写锁等。

## ForkJoin框架的原理
将大任务分割成若干个小任务，最终汇总每个小任务的结果后得到大任务结果的框架。

## 什么是工作窃取算法
某个线程从其他队列里面窃取任务来执行。
工作窃取算法的优点是充分利用线程进行并行计算，使用双端队列并减少了线程间的竞争。

## ThreadLoacl有哪些使用场景
日常开发时有时候会遇到把一个参数层层传递到最内层，然而中间层根本不需要使用这个参数，这时我们完全没有必要在每一个方法里面都传递这样一个通用的参数。使用threadLocal可以实现在任何类中想用的时候直接拿来使用就好了。

## Callable、Future和FutureTask
通过Callable和Future可以在任务执行完毕之后得到任务执行结果。
FutureTask是Future接口的一个唯一实现类。

## LockSupport的作用
LockSupport工具类主要用来实现线程的挂起和唤醒。
LockSupport比Object的wait/notify有两大优势
1. LockSupport不需要在同步代码块里 。所以线程间也不需要维护一个共享的同步对象了，实现了线程间的解耦。
2. unpark函数可以先于park调用，所以不需要担心线程间的执行的先后顺序。

## Condition的作用
可以使用Condition的await(), signal()来实现线程的休眠/唤醒功能。

