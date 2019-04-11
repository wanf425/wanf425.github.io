---
layout: article
title: 001-Java基础-并发包(同步结构)
key: A20190409007
tags: 编程 JAVA基础
category: blog
date: 2019-04-09 22:19:07 +08:00
modify_date: 2019-04-09 22:19:07 +08:00
---

Java的并发包中提供了丰富的同步结构，例如：

* CountDownLatch，允许一个或多个线程等待某些操作完成。
* CyclicBarrier，允许多个线程等待达到某个屏障。
* Semaphore，Java版本的信号量实现。

<!--more-->

## Semaphore代码示例

场景：火车站有若干名乘客在出租车等待区等车，此时有很多出租车已经就位，调度员一放行1名乘客上车。

代码：

```
public class SemaphoreDemo {
    public static void main(String[] args) throws InterruptedException {
        System.out.println("Action...GO!");
        Semaphore semaphore = new Semaphore(1);
        for (int i = 0; i < 10; i++) {
            Thread t = new Thread(new SemaphoreWorker(semaphore));
            t.start();
        }
    }
}

class SemaphoreWorker implements Runnable {
    private static SimpleDateFormat df = new SimpleDateFormat("HH:mm:ss:SSS");
    private String name;
    private Semaphore semaphore;

    public SemaphoreWorker(Semaphore semaphore) {
        this.semaphore = semaphore;
    }

    @Override
    public void run() {
        try {
            log("is waiting for a permit!");
            semaphore.acquire();
            log("acquired a permit!");
            log("executed!");
        } catch (InterruptedException e) {
            e.printStackTrace();
        } finally {
            log("released a permit!");
            semaphore.release();
        }
    }

    private void log(String msg) {
        if (name == null) {
            name = Thread.currentThread().getName();
        }

        System.out.println(df.format(new Date()) + ":" + name + " " + msg);
    }
}
```

## CountDownLatch代码示例

场景：火车站有10名乘客在出租车等待区等车，此时有很多出租车已经就位，调度员一放行5名乘客上车。

代码：

```
public class CountDownLatchDemo {

    public static void main(String[] args) throws InterruptedException {
        CountDownLatch latch = new CountDownLatch(5);
        for (int i = 0; i < 5; i++) {
            Thread t = new Thread(new FirstBatchWorker(latch));
            t.start();
        }

        for (int i = 0; i < 5; i++) {
            Thread t = new Thread(new SecondBatchWorker(latch));
            t.start();
        }
    }
}

class FirstBatchWorker implements Runnable {
    private CountDownLatch latch;

    public FirstBatchWorker(CountDownLatch latch) {
        this.latch = latch;
    }

    @Override
    public void run() {
        System.out.println("First batch executed!");
        latch.countDown();
    }
}

class SecondBatchWorker implements Runnable {
    private CountDownLatch latch;

    public SecondBatchWorker(CountDownLatch latch) {
        this.latch = latch;
    }

    @Override
    public void run() {
        try {
            latch.await();
            System.out.println("Second batch executed!");
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }
}
```

## CyclicBarrier代码示例

场景：与CountDownLatch相同。

代码：

```
public class CyclicBarrierDemo {

    public static void main(String[] args) throws InterruptedException {
        CyclicBarrier barrier = new CyclicBarrier(5, new Runnable() {
            @Override
            public void run() {
                System.out.println("Action...GO again!");
            }
        });
        for (int i = 0; i < 15; i++) {
            Thread t = new Thread(new CyclicWorker(barrier));
            t.start();
        }
    }

    static class CyclicWorker implements Runnable {
        private CyclicBarrier barrier;

        public CyclicWorker(CyclicBarrier barrier) {
            this.barrier = barrier;
        }

        @Override
        public void run() {
            try {
                System.out.println(Thread.currentThread().getName() + "Executed!");
                barrier.await();
            } catch (BrokenBarrierException e) {
                e.printStackTrace();
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
    }
}
```

## CyclicBarrier与CountDownLatch的区别

CountDownLatch无法自动重置，CyclicBarrier可以。

