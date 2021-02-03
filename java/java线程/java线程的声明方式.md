###  java声明线程的四种方式

###  1. 继承Thread类

继承Thread类，实现run()方法，之后调用时new这个对象，再调用对象的start()方法即可。

```JAVA
package study.threadStudy;

// step1: 继承Thread类
public class NewThread extends Thread{
  // step2: 重写run方法
  @Override
  public void run() {
    System.out.println("create a thread by extends Thread");
  }

  public static void main(String[] args) {
    // step3: 实例化对象并且执行run方法
    NewThread newThread = new NewThread();
    newThread.start();
  }
}

```

### 2. 实现Runnable接口

由于Java语言的规范，我们如果继承了一个类，就无法再继承Thread类了，所以我们可以实现Runnable接口。使用方法为新建一个Thread类，并且将实现类传入Thread的构造方法中。

```JAVA
package study.threadStudy;

// step1: 实现Runnable接口
public class RunnableThread implements Runnable{
  // step2: 重写run方法
  @Override
  public void run() {
    System.out.println("create a thread implements Runnable");
  }

  public static void main(String[] args) {
    // step3: 实例化该类
    RunnableThread runnableThread = new RunnableThread();
    // step4: 将该类以参数形式传入Thread类的构造器中
    Thread thread = new Thread(runnableThread);
    // step5: 执行Thread的start方法
    thread.start();
  }
}

```

如下是Thread类的run方法，我们就可以知道为何可以这样做

```JAVA
public void run() {
        if (target != null) {
            target.run();
        }
    }
```

###  3. 实现Callable以实现带返回值的线程

Callable可以收集线程执行的结果。具体的实现方法为：创建一个类并实现Callable接口，再call方法中实现具体的运算逻辑并返回计算结果。具体的调用过程为：创建一个线程池，一个用于接收返回值的Future list以及Callable线程实例，使用线程池提交任务并将线程执行后的结果保存在Future中，再线程执行结束后遍历Future list来获取结果。实现代码如下：

```java
package study.threadStudy;

import java.util.LinkedList;
import java.util.List;
import java.util.concurrent.*;

// step1: 实现Callable接口
public class CallableThread implements Callable<String> {
  private String name;

  public CallableThread(String name) {
    this.name = name;
  }

  // step2: 实现call方法
  @Override
  public String call() throws Exception {
    return this.name;
  }

  public static void main(String[] args) {
    // step3: 实现一个大小为5的线程池
    ExecutorService pool = Executors.newFixedThreadPool(5);
    // step4: 创建Future的list
    List<Future> list = new LinkedList<>();
    for (int i = 0; i < 5; i++) {
      // step5: 创建有返回值的Callable实例
      CallableThread c = new CallableThread(i + " ");
      // step6: 使用Future记录结果并且加到集合中
      Future future = pool.submit(c);
      list.add(future);
    }
    // step7: 关闭线程池，等待线程执行结束
    pool.shutdown();
    // step8： 可以通过Future的get方法获取到返回值
    for (Future future : list) {
      try {
        System.out.println("get the result from callable thread:" + future.get());
      } catch (InterruptedException e) {
        e.printStackTrace();
      } catch (ExecutionException e) {
        e.printStackTrace();
      }
    }
  }
}

```

###  4. 基于线程池

在3中我们已经看到了线程池调用submit方法获取到callable的返回值。我们也可以使用线程池的execute方法来执行runnable，代码如下:

```java
package study.threadStudy;

import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;

public class ThreadPoolClass {
  public static void main(String[] args) {
    ExecutorService threadPool = Executors.newFixedThreadPool(5);
    for (int i = 0; i < 5; i++) {
      threadPool.execute(new Runnable() {
        @Override
        public void run() {
          System.out.println("execute thread:::" + Thread.currentThread().getName());
        }
      });
    }
  }
}

```

