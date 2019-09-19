什么?对你没有听错,也没有看错 ..多线程并发执行任务，取结果归集~~ 不再忧愁….

## 引言

先来看一些APP的获取数据,诸如此类,一个页面获取N多个,多达10个左右的一个用户行为数据,比如:点赞数,发布文章数,点赞数,消息数,关注数,收藏数,粉丝数,卡券数,红包数……….. 真的是多~ 我们看些图:

![img](https://mmbiz.qpic.cn/mmbiz/JfTPiahTHJhowY3YjJn4ibVVk2tUa6FXpaMjLYKfps80unenNjAxsiaYibiakQibYQ9vObWPMEDjXVQibRZriaAxoAxiaQw/640?wx_fmt=other&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

![img](https://mmbiz.qpic.cn/mmbiz/JfTPiahTHJhowY3YjJn4ibVVk2tUa6FXpaAHYGdGoPs7O58h1Z6sdEFEY50I9556Ua7oa2e1tDmUDlfe6ndrk5LQ/640?wx_fmt=other&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

平时要10+接口的去获取数据(因为当你10+个查询写一起,那估计到半分钟才能响应了),一个页面上N多接口,真是累死前端的宝宝了,前端开启多线程也累啊,我们做后端的要体量一下前端的宝宝们,毕竟有句话叫"程序员何苦为难程序员~"

今天我们也可以一个接口将这些数据返回~ 还贼TM快,解决串行编程,阻塞编程带来的苦恼~

## 多线程并发执行任务，取结果归集

今天猪脚就是：Future、FutureTask、ExecutorService…

- 用上FutureTask任务获取结果老少皆宜，就是CPU有消耗。FutureTask也可以做闭锁（实现了Future的语义，表示一种抽象的可计算的结果）。通过把Callable(相当于一个可生成结果的Runnable)作为一个属性，进而把它自己作为一个执行器去继承Runnable,FutureTask 实际上就是一个支持取消行为的异步任务执行器。
- Callable就是一个回调接口,可以泛型声明返回类型,而Runnable是线程去执行的方法.这个很简单~大家想深入了解就进去看源码好了～ 因为真的很简单～
- FutureTask实现了Future，提供了start, cancel, query等功能，并且实现了Runnable接口，可以提交给线程执行。
- Java并发工具类的三板斧 状态，队列，CAS

## 状态

```
 /**     * The run state of this task, initially NEW.  The run state     * transitions to a terminal state only in methods set,     * setException, and cancel.  During completion, state may take on     * transient values of COMPLETING (while outcome is being set) or     * INTERRUPTING (only while interrupting the runner to satisfy a     * cancel(true)). Transitions from these intermediate to final     * states use cheaper ordered/lazy writes because values are unique     * and cannot be further modified.     *     * Possible state transitions:        //可能发生的状态过度过程     * NEW -> COMPLETING -> NORMAL        // 创建-->完成-->正常     * NEW -> COMPLETING -> EXCEPTIONAL   // 创建-->完成-->异常     * NEW -> CANCELLED                   // 创建-->取消     * NEW -> INTERRUPTING -> INTERRUPTED // 创建-->中断中-->中断结束     */    private volatile int state;                  // 执行器状态    private static final int NEW = 0;            // 初始值        由构造函数保证     private static final int COMPLETING = 1;     // 完成进行时    正在设置任务结果    private static final int NORMAL = 2;         // 正常结束      任务正常执行完毕    private static final int EXCEPTIONAL = 3;    // 发生异常      任务执行过程中发生异常    private static final int CANCELLED = 4;      // 已经取消      任务已经取消    private static final int INTERRUPTING = 5;   // 中断进行时    正在中断运行任务的线程    private static final int INTERRUPTED = 6;    // 中断结束      任务被中断    /** The underlying callable; nulled out after running */    private Callable<V> callable;    /** The result to return or exception to throw from get() */    private Object outcome; // non-volatile, protected by state reads/writes    /** The thread running the callable; CASed during run() */    private volatile Thread runner;    /** Treiber stack of waiting threads */    private volatile WaitNode waiters;
```

还不明白就看图:

![img](https://mmbiz.qpic.cn/mmbiz/JfTPiahTHJhowY3YjJn4ibVVk2tUa6FXpabNWPKx5UocIibNUcyoQ24rAC82U7vEnhgwUKxMuY0JDh4vLrvibd5Ukg/640?wx_fmt=other&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

![img](https://mmbiz.qpic.cn/mmbiz/JfTPiahTHJhowY3YjJn4ibVVk2tUa6FXpargMSnCrrJ96UtT7oNkVRIyktstAW2W5jXIiagGjQMVg8CcXgtrvndJw/640?wx_fmt=other&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

```
public interface Future<T> {    /**    *取消任务    *@param mayInterruptIfRunning    *是否允许取消正在执行却没有执行完毕的任务，如果设置true，则表示可以取消正在执行过程中的任务    *如果任务正在执行，则返回true    *如果任务还没有执行，则无论mayInterruptIfRunning为true还是false，返回true    *如果任务已经完成，则无论mayInterruptIfRunning为true还是false，返回false    */    boolean cancel(boolean mayInterruptIfRunning);    /**    *任务是否被取消成功，如果在任务正常完成前被取消成功，则返回 true    */    boolean isCancelled();    /**    *任务是否完成    */    boolean isDone();    /**    *通过阻塞获取执行结果    */    T get() throws InterruptedException, ExecutionException;    /**    *通过阻塞获取执行结果。如果在指定的时间内没有返回，则返回null    */    T get(long timeout, TimeUnit unit)        throws InterruptedException, ExecutionException, TimeoutException;}
```

Future

- cancle 可以停止任务的执行 但不一定成功 看返回值true or false
- get 阻塞获取callable的任务结果,即get阻塞住调用线程，直至计算完成返回结果
- isCancelled 是否取消成功
- isDone 是否完成

### 重点说明:

**Furture.get()获取执行结果的值，取决于执行的状态，如果任务完成，会立即返回结果，否则一直阻塞直到任务进入完成状态，然后返回结果或者抛出异常。**

**“运行完成”表示计算的所有可能结束的状态，包含正常结束，由于取消而结束和由于异常而结束。当进入完成状态，他会停止在这个状态上，只要state不处于 NEW 状态，就说明任务已经执行完毕。**

FutureTask负责将计算结果从执行任务的线程传递到调用这个线程的线程，而且确保了,传递过程中结果的安全发布

UNSAFE 无锁编程技术,确保了线程的安全性~ 为了保持无锁编程CPU的消耗,所以用状态标记,减少空转的时候CPU的压力

- 任务本尊：callable
- 任务的执行者：runner
- 任务的结果：outcome
- 获取任务的结果：state + outcome + waiters
- 中断或者取消任务：state + runner + waiters

### run方法

1、检查state，非NEW，说明已经启动，直接返回；否则，设置runner为当前线程，成功则继续，否则，返回。

2、调用Callable.call()方法执行任务，成功则调用set(result)方法，失败则调用setException(ex)方法，最终都会设置state，并调用finishCompletion()方法，唤醒阻塞在get()方法上的线程们。

3、如注释所示，如果省略ran变量，并把"set(result);" 语句移动到try代码块"ran = true;" 语句处，会怎样呢？首先，从代码逻辑上看，是没有问题的，但是，考虑到"set(result);"方法万一抛出异常甚至是错误了呢？set()方法最终会调用到用户自定义的done()方法，所以，不可省略。

4、如果state为INTERRUPTING, 则主动让出CPU，自旋等待别的线程执行完中断流程。见handlePossibleCancellationInterrupt(int s) 方法。

```
public void run() {        // UNSAFE.compareAndSwapObject， CAS保证Callable任务只被执行一次 无锁编程        if (state != NEW || !UNSAFE.compareAndSwapObject(this, runnerOffset, null, Thread.currentThread()))            return;        try {            Callable<V> c = callable; // 拿到执行任务            if (c != null && state == NEW) { // 任务不为空，并且执行器状态是初始值，才会执行；如果取消就不执行了                V result;                boolean ran; // 记录是否执行成功                try {                    result = c.call(); // 执行任务                    ran = true; // 成功                } catch (Throwable ex) {                    result = null; // 异常，清空结果                    ran = false; // 失败                    setException(ex); // 记录异常                }                if (ran) // 问题：ran变量可以省略吗，把set(result);移到try块里面？                    set(result); // 设置结果            }        } finally {            runner = null; // 直到set状态前，runner一直都是非空的，为了防止并发调用run()方法。            int s = state;            if (s >= INTERRUPTING) // 有别的线程要中断当前线程，把CPU让出去，自旋等一下                handlePossibleCancellationInterrupt(s);        }    }
```





```
      private void handlePossibleCancellationInterrupt(int s) {         if (s == INTERRUPTING) // 当state为INTERRUPTING时             while (state == INTERRUPTING) // 表示有线程正在中断当前线程                 Thread.yield(); // 让出CPU，自旋等待中断     }
```

再啰嗦下: run方法重点做了以下几件事：

- 将runner属性设置成当前正在执行run方法的线程
- 调用callable成员变量的call方法来执行任务
- 设置执行结果outcome, 如果执行成功, 则outcome保存的就是执行结果；如果执行过程中发生了异常, 则outcome中保存的就是异常，设置结果之前，先将state状态设为中间态
- 对outcome的赋值完成后，设置state状态为终止态(NORMAL或者EXCEPTIONAL)
- 唤醒Treiber栈中所有等待的线程
- 善后清理(waiters, callable，runner设为null)
- 检查是否有遗漏的中断，如果有，等待中断状态完成。

怎么能少了get方法呢,一直阻塞获取参见:awaitDone

```
    public V get() throws InterruptedException, ExecutionException {        int s = state; // 执行器状态         if (s <= COMPLETING) // 如果状态小于等于COMPLETING，说明任务正在执行，需要等待             s = awaitDone(false, 0L); // 等待         return report(s); // 报告结果     }
```

顺便偷偷看下get(long, TimeUnit),就是get的方法扩展,增加了超时时间,超时后我还没拿到就生气抛异常….

```
public V get(long timeout, TimeUnit unit) throws InterruptedException, ExecutionException, TimeoutException {        if (unit == null) // 参数校验            throw new NullPointerException();        int s = state; // 执行器状态        if (s <= COMPLETING && (s = awaitDone(true, unit.toNanos(timeout))) <= COMPLETING) // 如果状态小于等于COMPLETING，说明任务正在执行，需要等待；等待指定时间，state依然小于等于COMPLETING            throw new TimeoutException(); // 抛出超时异常        return report(s); // 报告结果    }
```

那么再看awaitDone,要知道会写死循环while(true)|for (;;)的都是高手~

```
private int awaitDone(boolean timed, long nanos) throws InterruptedException {        final long deadline = timed ? System.nanoTime() + nanos : 0L; // 计算deadline        WaitNode q = null; // 等待结点        boolean queued = false; // 是否已经入队        for (;;) {            if (Thread.interrupted()) { // 如果当前线程已经标记中断，则直接移除此结点，并抛出中断异常                removeWaiter(q);                throw new InterruptedException();            }            int s = state; // 执行器状态            if (s > COMPLETING) { // 如果状态大于COMPLETING，说明任务已经完成，或者已经取消，直接返回                if (q != null)                    q.thread = null; // 复位线程属性                return s; // 返回            } else if (s == COMPLETING) // 如果状态等于COMPLETING，说明正在整理结果，自旋等待一会儿                Thread.yield();            else if (q == null) // 初始，构建结点                q = new WaitNode();            else if (!queued) // 还没入队，则CAS入队                queued = UNSAFE.compareAndSwapObject(this, waitersOffset, q.next = waiters, q);            else if (timed) { // 是否允许超时                nanos = deadline - System.nanoTime(); // 计算等待时间                if (nanos <= 0L) { // 超时                    removeWaiter(q); // 移除结点                    return state; // 返回结果                }                LockSupport.parkNanos(this, nanos); // 线程阻塞指定时间            } else                LockSupport.park(this); // 阻塞线程        }    }
```

至此,线程安排任务和获取我就不啰嗦了~~~~还要很多探索的,毕竟带薪聊天比较紧张,我就不多赘述了~

### 队列

接着我们来看队列，在FutureTask中，队列的实现是一个单向链表，它表示所有等待任务执行完毕的线程的集合。我们知道，FutureTask实现了Future接口，可以获取“Task”的执行结果，那么如果获取结果时，任务还没有执行完毕怎么办呢？那么获取结果的线程就会在一个等待队列中挂起，直到任务执行完毕被唤醒。这一点有点类似于AQS中的sync queue，在下文的分析中，大家可以自己对照它们的异同点。

我们前面说过，在并发编程中使用队列通常是将当前线程包装成某种类型的数据结构扔到等待队列中，我们先来看看队列中的每一个节点是怎么个结构：

```
static final class WaitNode {    volatile Thread thread;    volatile WaitNode next;    WaitNode() { thread = Thread.currentThread(); }}
```

可见，相比于AQS的sync queue所使用的双向链表中的Node，这个WaitNode要简单多了，它只包含了一个记录线程的thread属性和指向下一个节点的next属性。

值得一提的是，FutureTask中的这个单向链表是当做栈来使用的，确切来说是当做Treiber栈来使用的，不了解Treiber栈是个啥的可以简单的把它当做是一个线程安全的栈，它使用CAS来完成入栈出栈操作(想进一步了解的话可以看这篇文章)。

为啥要使用一个线程安全的栈呢，因为同一时刻可能有多个线程都在获取任务的执行结果，如果任务还在执行过程中，则这些线程就要被包装成WaitNode扔到Treiber栈的栈顶，即完成入栈操作，这样就有可能出现多个线程同时入栈的情况，因此需要使用CAS操作保证入栈的线程安全，对于出栈的情况也是同理。

由于FutureTask中的队列本质上是一个Treiber(驱动)栈，那么使用这个队列就只需要一个指向栈顶节点的指针就行了，在FutureTask中，就是waiters属性：

```
/** Treiber stack of waiting threads */private volatile WaitNode waiters;
```

事实上，它就是整个单向链表的头节点。

综上，FutureTask中所使用的队列的结构如下：

![img](https://mmbiz.qpic.cn/mmbiz/JfTPiahTHJhowY3YjJn4ibVVk2tUa6FXpaTkWnc4ribHCoPQpQDVjlHja0IXsQl4NxBIn21Aiar45b6h2RLQCaVxIg/640?wx_fmt=other&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

### CAS操作

CAS操作大多数是用来改变状态的，在FutureTask中也不例外。我们一般在静态代码块中初始化需要CAS操作的属性的偏移量：

```
    // Unsafe mechanics    private static final sun.misc.Unsafe UNSAFE;    private static final long stateOffset;    private static final long runnerOffset;    private static final long waitersOffset;    static {        try {            UNSAFE = sun.misc.Unsafe.getUnsafe();            Class<?> k = FutureTask.class;            stateOffset = UNSAFE.objectFieldOffset(k.getDeclaredField("state"));            runnerOffset = UNSAFE.objectFieldOffset(k.getDeclaredField("runner"));            waitersOffset = UNSAFE.objectFieldOffset(k.getDeclaredField("waiters"));        } catch (Exception e) {            throw new Error(e);        }    }
```

从这个静态代码块中我们也可以看出，CAS操作主要针对3个属性，包括state、runner和waiters，说明这3个属性基本是会被多个线程同时访问的。其中state属性代表了任务的状态，waiters属性代表了指向栈顶节点的指针，这两个我们上面已经分析过了。

runner属性代表了执行FutureTask中的“Task”的线程。为什么需要一个属性来记录执行任务的线程呢？这是为了中断或者取消任务做准备的，只有知道了执行任务的线程是谁，我们才能去中断它。

定义完属性的偏移量之后，接下来就是CAS操作本身了。在FutureTask，CAS操作最终调用的还是Unsafe类的compareAndSwapXXX方法，关于Unsafe，由于带薪码文这里不再赘述。

## 实战演练

一切没有例子的讲解都是耍流氓 >>> 葱姜切沫~~加入生命的源泉….

实战项目以springboot为项目脚手架,github地址:

> https://github.com/leaJone/mybot

### 1.MyFutureTask实现类

内部定义一个线程池进行任务的调度和线程的管理以及线程的复用,大家可以根据自己的实际项目情况进行配置

其中线程调度示例:核心线程 8 最大线程 20 保活时间30s 存储队列 10 有守护线程 拒绝策略:将超负荷任务回退到调用者

说明 :

> 默认使用核心线程(8)数执行任务,任务数量超过核心线程数就丢到队列,队列(10)满了就再开启新的线程,新的线程数最大为20,当任务执行完,新开启的线程将存活30s,若没有任务就消亡,线程池回到核心线程数量.

```
import com.boot.lea.mybot.dto.UserBehaviorDataDTO;import com.boot.lea.mybot.service.UserService;import com.google.common.util.concurrent.ThreadFactoryBuilder;import lombok.extern.slf4j.Slf4j;import org.springframework.stereotype.Component;import javax.annotation.Resource;import java.util.concurrent.*;/** * @author Lijing * @date 2019年7月29日 */@Slf4j@Componentpublic class MyFutureTask {    @Resource    UserService userService;    /**     * 核心线程 8 最大线程 20 保活时间30s 存储队列 10 有守护线程 拒绝策略:将超负荷任务回退到调用者     */    private static ExecutorService executor = new ThreadPoolExecutor(8, 20,            30L, TimeUnit.SECONDS, new LinkedBlockingQueue<Runnable>(10),            new ThreadFactoryBuilder().setNameFormat("User_Async_FutureTask-%d").setDaemon(true).build(),            new ThreadPoolExecutor.CallerRunsPolicy());    @SuppressWarnings("all")    public UserBehaviorDataDTO getUserAggregatedResult(final Long userId) {        System.out.println("MyFutureTask的线程:" + Thread.currentThread());        long fansCount = 0, msgCount = 0, collectCount = 0,                followCount = 0, redBagCount = 0, couponCount = 0;//        fansCount = userService.countFansCountByUserId(userId);//        msgCount = userService.countMsgCountByUserId(userId);//        collectCount = userService.countCollectCountByUserId(userId);//        followCount = userService.countFollowCountByUserId(userId);//        redBagCount = userService.countRedBagCountByUserId(userId);//        couponCount = userService.countCouponCountByUserId(userId);        try {            Future<Long> fansCountFT = executor.submit(() -> userService.countFansCountByUserId(userId));            Future<Long> msgCountFT = executor.submit(() -> userService.countMsgCountByUserId(userId));            Future<Long> collectCountFT = executor.submit(() -> userService.countCollectCountByUserId(userId));            Future<Long> followCountFT = executor.submit(() -> userService.countFollowCountByUserId(userId));            Future<Long> redBagCountFT = executor.submit(() -> userService.countRedBagCountByUserId(userId));            Future<Long> couponCountFT = executor.submit(() -> userService.countCouponCountByUserId(userId));            //get阻塞            fansCount = fansCountFT.get();            msgCount = msgCountFT.get();            collectCount = collectCountFT.get();            followCount = followCountFT.get();            redBagCount = redBagCountFT.get();            couponCount = couponCountFT.get();        } catch (InterruptedException | ExecutionException e) {            e.printStackTrace();            log.error(">>>>>>聚合查询用户聚合信息异常:" + e + "<<<<<<<<<");        }        UserBehaviorDataDTO userBehaviorData =                UserBehaviorDataDTO.builder().fansCount(fansCount).msgCount(msgCount)                        .collectCount(collectCount).followCount(followCount)                        .redBagCount(redBagCount).couponCount(couponCount).build();        return userBehaviorData;    }}
```

### 2.service业务方法

常规业务查询方法,为了特效,以及看出实际的效果,我们每个方法做了延时

```
import com.boot.lea.mybot.mapper.UserMapper;import org.springframework.beans.factory.annotation.Autowired;import org.springframework.stereotype.Service;import java.util.concurrent.TimeUnit;@Servicepublic class UserServiceImpl implements UserService {    @Autowired    UserMapper userMapper;    @Override    public long countFansCountByUserId(Long userId) {        try {            Thread.sleep(10000);            System.out.println("获取FansCount===睡眠:" + 10 + "s");        } catch (InterruptedException e) {            e.printStackTrace();        }        System.out.println("UserService获取FansCount的线程  " + Thread.currentThread().getName());        return 520;    }    @Override    public long countMsgCountByUserId(Long userId) {        System.out.println("UserService获取MsgCount的线程  " + Thread.currentThread().getName());        try {            Thread.sleep(10000);            System.out.println("获取MsgCount===睡眠:" + 10 + "s");        } catch (InterruptedException e) {            e.printStackTrace();        }        return 618;    }    @Override    public long countCollectCountByUserId(Long userId) {        System.out.println("UserService获取CollectCount的线程  " + Thread.currentThread().getName());        try {            Thread.sleep(10000);            System.out.println("获取CollectCount==睡眠:" + 10 + "s");        } catch (InterruptedException e) {            e.printStackTrace();        }        return 6664;    }    @Override    public long countFollowCountByUserId(Long userId) {        System.out.println("UserService获取FollowCount的线程  " + Thread.currentThread().getName());        try {            Thread.sleep(10000);            System.out.println("获取FollowCount===睡眠:" + 10+ "s");        } catch (InterruptedException e) {            e.printStackTrace();        }        return userMapper.countFollowCountByUserId(userId);    }    @Override    public long countRedBagCountByUserId(Long userId) {        System.out.println("UserService获取RedBagCount的线程  " + Thread.currentThread().getName());        try {            TimeUnit.SECONDS.sleep(4);            System.out.println("获取RedBagCount===睡眠:" + 4 + "s");        } catch (InterruptedException e) {            e.printStackTrace();        }        return 99;    }    @Override    public long countCouponCountByUserId(Long userId) {        System.out.println("UserService获取CouponCount的线程  " + Thread.currentThread().getName());        try {            TimeUnit.SECONDS.sleep(8);            System.out.println("获取CouponCount===睡眠:" + 8+ "s");        } catch (InterruptedException e) {            e.printStackTrace();        }        return 66;    }}
```

### 3.controller调用

```
/** * @author LiJing * @ClassName: UserController * @Description: 用户控制器 * @date 2019/7/29 15:16 */@RestController@RequestMapping("user/")public class UserController {    @Autowired    private UserService userService;    @Autowired    private MyFutureTask myFutureTask;    @GetMapping("/index")    @ResponseBody    public String index() {        return "启动用户模块成功~~~~~~~~";    }    //http://localhost:8080/api/user/get/data?userId=4    @GetMapping("/get/data")    @ResponseBody    public UserBehaviorDataDTO getUserData(Long userId) {        System.out.println("UserController的线程:" + Thread.currentThread());        long begin = System.currentTimeMillis();        UserBehaviorDataDTO userAggregatedResult = myFutureTask.getUserAggregatedResult(userId);        long end = System.currentTimeMillis();        System.out.println("===============总耗时:" + (end - begin) /1000.0000+ "秒");        return userAggregatedResult;    }}
```

我们启动项目:开启调用 http://localhost:8080/api/user/get/data?userId=4

当我们线程池配置为:核心线程 8 最大线程 20 保活时间30s 存储队列 10 的时候,我们测试的结果如下:

![img](https://mmbiz.qpic.cn/mmbiz/JfTPiahTHJhowY3YjJn4ibVVk2tUa6FXpaYzOlEcPP50CtFpWfyO6ic9lE2SczIBlEVJW102ZBcH6PTmicCc2HsB8A/640?wx_fmt=other&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

![img](https://mmbiz.qpic.cn/mmbiz/JfTPiahTHJhowY3YjJn4ibVVk2tUa6FXpaALb9Q4KyZ6HonKHvxUwamNMkS62GdXoiaM89iamdKSGaBKibbdEKtl9eQ/640?wx_fmt=other&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

结果:我们看到每个server method的执行线程都是从线程池中发起的线程名:User_Async_FutureTask-%d, 总耗时从累计的52秒缩短到10秒,即取决于最耗时的方法查询时间.

那我们再将注释代码放开,进行串行查询进行测试:

![img](https://mmbiz.qpic.cn/mmbiz/JfTPiahTHJhowY3YjJn4ibVVk2tUa6FXpaYemvqmctic4skdE2iadVKibOd4Q4oYw97Eicgf0OPMJ0rD7C8XLBJHIRxw/640?wx_fmt=other&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

![img](https://mmbiz.qpic.cn/mmbiz/JfTPiahTHJhowY3YjJn4ibVVk2tUa6FXpa1jNRh43Y0E9ib8v51a5ywo8VLzAKG65SZfxesn62PE0h06GzhJQrb1A/640?wx_fmt=other&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

![img](https://mmbiz.qpic.cn/mmbiz/JfTPiahTHJhowY3YjJn4ibVVk2tUa6FXpaJLzF58ia7kz2ohyrhtv3Fibic6Lb0hSibckf1BYmdlfCKfyHH2KrceVsJQ/640?wx_fmt=other&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

结果:我们使用串行的方式进行查询,结果汇总将达到52秒,那太可怕了~~

## 总结

使用FutureTask的时候,就是将任务runner以caller的方式进行回调,阻塞获取,最后我们将结果汇总,即完成了开启多线程异步调用我们的业务方法.

```
            Future<Long> fansCountFT = executor.submit(new Callable<Long>() {                @Override                public Long call() throws Exception {                    return userService.countFansCountByUserId(userId);                }            });
```

这里使用的只是一个简单的例子,具体项目可以定义具体的业务方法进行归并处理,其实在JDK1.8以后,又有了ExecutorCompletionService,ForkJoinTask,CompletableFuture这些都可以实现上述的方法,我们后续会做一些这些方法使用的案例,期望大家的关注,文章中有不足之处,欢迎指正~

## 小甜点

![img](https://mmbiz.qpic.cn/mmbiz/JfTPiahTHJhowY3YjJn4ibVVk2tUa6FXpaZviaJxWibHa84hTmd2LcmBvfYVYUuxctQuox8zoiapYhxDQWFDRYlrbBA/640?wx_fmt=other&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

所以:我们要用到亲爱的Spring的异步编程,异步编程有很多种方式:比如常见的Future的sync,CompletableFuture.supplyAsync,@Async,哈哈 其实都离不开Thread.start()…,等等我说个笑话:

> 老爸有俩孩子：小红和小明。老爸想喝酒了，他让小红去买酒，小红出去了。然后老爸突然想吸烟了，于是老爸让小明去买烟。在面对对象的思想中，一般会把买东西，然后买回来这件事作为一个方法，如果按照顺序结构或者使用多线程同步的话，小明想去买烟就必须等小红这个买东西的操作进行完。这样无疑增加了时间的开销(万一老爸尿憋呢?)。异步就是为了解决这样的问题。你可以分别给小红小明下达指令，让他们去买东西，然后你就可以自己做自己的事，等他们买回来的时候接收结果就可以了。

```
package com.boot.lea.mybot.futrue;/** * @ClassName: TestFuture * @Description: 演示异步编程 * @author LiJing * @date 2019/8/5 15:16 */@SuppressWarnings("all")public class TestFuture {    static ExecutorService executor = Executors.newFixedThreadPool(2);    public static void main(String[] args) throws InterruptedException {        //两个线程的线程池        //小红买酒任务，这里的future2代表的是小红未来发生的操作，返回小红买东西这个操作的结果        CompletableFuture<String> future2 = CompletableFuture.supplyAsync(() -> {            System.out.println("爸：小红你去买瓶酒！");            try {                System.out.println("小红出去买酒了，女孩子跑的比较慢，估计5s后才会回来...");                Thread.sleep(5000);                return "我买回来了！";            } catch (InterruptedException e) {                System.err.println("小红路上遭遇了不测");                return "来世再见！";            }        }, executor);        //小明买烟任务，这里的future1代表的是小明未来买东西会发生的事，返回值是小明买东西的结果        CompletableFuture<String> future1 = CompletableFuture.supplyAsync(() -> {            System.out.println("爸：小明你去买包烟！");            try {                System.out.println("小明出去买烟了，可能要3s后回来...");                Thread.sleep(3000);                throw new InterruptedException();//                return "我买回来了!";            } catch (InterruptedException e) {                System.out.println("小明路上遭遇了不测！");                return "这是我托人带来的口信，我已经不在了。";            }        }, executor);        //获取小红买酒结果，从小红的操作中获取结果，把结果打印        future2.thenAccept((e) -> {            System.out.println("小红说：" + e);        });        //获取小明买烟的结果        future1.thenAccept((e) -> {            System.out.println("小明说：" + e);        });        System.out.println("爸：等啊等 西湖美景三月天嘞......");        System.out.println("爸: 我觉得无聊甚至去了趟厕所。");        Thread.currentThread().join(9 * 1000);        System.out.println("爸：终于给老子买来了......huo 酒");        //关闭线程池        executor.shutdown();    }}
```

运行结果:

![img](https://mmbiz.qpic.cn/mmbiz/JfTPiahTHJhowY3YjJn4ibVVk2tUa6FXpaFh6kIK6K1aqekpZHEgMTVkQMqAywibgLHu6DSEjrXefWuZ9dsk2vicTg/640?wx_fmt=other&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)