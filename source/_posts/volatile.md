---
typora-root-url: ./
---

volatile**关键字修饰变量具有以下特点**：

1. 保证了变量的内存**可见性**。
2. 禁止指令重排序。(有序性)

**可见性(Visibility)** **是指当多个线程访问同一个变量时，一个线程修改了这个变量的值，其他线程能够立即看得到修改的值**。 正如上面“交互操作流程”中所说明的一样，JMM是通过在线程1变量工作内存修改后将新值同步回主内存，线程2在变量读取前从主内存刷新变量值，这种**依赖主内存作为传递媒介**的方式来实现可见性。

**有序性(Ordering)** 有序性规则表现在以下两种场景: 线程内和线程间

- 线程内 从某个线程的角度看方法的执行，指令会按照一种叫“串行”（as-if-serial）的方式执行，此种方式已经应用于顺序编程语言。
- 线程间 这个线程“观察”到其他线程并发地执行非同步的代码时，由于指令重排序优化，任何代码都有可能交叉执行。唯一起作用的约束是：对于同步方法，同步块(synchronized关键字修饰)以及volatile字段的操作仍维持相对有序



demo:

```java
package com.cbl.keyworld;

/**
 * @author ：CBL
 * @date ：Created in 2020/1/16  17:49
 * @description：
 */
public class VloatileDemo1 {

    private static Boolean FLAG=false;


    /**
     * lock(锁定)：将一个变量标识为被一个线程独占状态。
     * unlock(解锁)：将一个变量从独占状态释放出来，释放后的变量才可以被其他线程锁定。
     * read(读取)：将一个变量的值从主内存传输到工作内存中，以便随后的load操作。
     * load(载入)：把read操作从主内存中得到的变量值放入工作内存的变量的副本中。
     * use(使用)：把工作内存中的一个变量的值传给执行引擎，每当虚拟机遇到一个使用到变量的指令时都会使用该指令。
     * assign(赋值)：把一个从执行引擎接收到的值赋给工作内存中的变量，每当虚拟机遇到一个给变量赋值的指令时，都要使用该操作。
     * store(存储)：把工作内存中的一个变量的值传递给主内存，以便随后的write操作。
     * write(写入)：把store操作从工作内存中得到的变量的值写到主内存中的变量
     * @param args
     * @throws Exception
     */
    public static void main(String[] args) throws Exception{
        //线程2
        new Thread(()->{
            System.out.println("wait................");
            while (!FLAG){

            }
            System.out.println(".....................success");
        }).start();

        Thread.sleep(200);


        //线程1
        new Thread(()->{
            System.out.println("start................");
            FLAG=true;
            System.out.println("end................");
        }).start();

    }
}

```

运行结果：

wait................
start................
end................

流程图如下：



线程1改变了Flag，但是由于线程2已经获取了主内存中的变量，加载进了线程的运行内存，运行内存已经有了变量的副本变量，主内存发生改变了，副本内存的值不会发生改变。

此时，FLAG变量假如有volatile修饰的话，当主内存发生改变，线程内的副本变量也会随之改变。

加了volatile关键字之后的流程图如下：

![20200117_no_volatile](/../image/20200117_no_volatile.png)

volatile 其实底层就是加锁，利用了cpu总线嗅探机制，当线程1副本变量回写进主内存的时候，线程2的总线嗅探机制就会触发，会导致线程2里面的副本变量失效。当线程1在store操作的时候，就会进行lock操作，当完成write操作们就会释放锁。

![20200117_volatile](/../image/20200117_volatile.png)

