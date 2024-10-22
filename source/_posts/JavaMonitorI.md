---
title: Java并发——管程模型
date: 2024-10-22 09:09:46
tags:
- 并发 管程
categories:
- 并发编程
---

最近在翻操作系统的资料的时候，看到信号量和管程，联想到JDK的实践，记录一下管程的一些思想。操作系统的话，南大蒋炎炎老师的课不错，有时间实践完，后续全面记录一下心得。



管程（Monitor）是一种用于同步并发程序中线程访问共享资源的机制，它提供了对临界区的自动管理，避免了线程之间的竞争条件。不同的管程模型在处理同步和线程唤醒方面有差异，主要有三种经典模型：**Hasen 模型**、**Hoare 模型** 和 **Mesa 模型**。

### 1. Hasen 模型
Hasen 模型是一个最简单的管程模型，采用条件变量和锁机制来同步线程。这个模型的特点是，当线程 `wait` 时，它必须显式地释放锁，并进入等待队列。当被唤醒时，线程会被放回就绪队列，但不保证立即获得锁。

#### 特点：
- 线程在 `wait()` 时进入等待队列，释放管程锁。
- 线程被唤醒时，只进入就绪队列，不保证立即执行。

### 2. Hoare 模型
Hoare 模型由 Tony Hoare 提出，在管程的经典实现中，当某个线程执行 `wait()`，它会立即释放管程锁，并进入等待队列。而当条件满足时，线程被 `signal()` 唤醒并**立即执行**，而唤醒它的线程会让出管程锁。

#### 特点：
- 被 `signal()` 唤醒的线程立即获得锁并开始执行。
- 唤醒它的线程会暂停，直到该被唤醒的线程执行完。

#### 代码示例：
假设有一个简单的生产者-消费者问题，Hoare 模型保证生产者和消费者直接交替执行。

```java
class HoareMonitor {
    private int count = 0;
    private final int LIMIT = 10;

    public synchronized void produce() throws InterruptedException {
        while (count == LIMIT) {
            wait();
        }
        count++;
        System.out.println("Produced: " + count);
        notify();
    }

    public synchronized void consume() throws InterruptedException {
        while (count == 0) {
            wait();
        }
        System.out.println("Consumed: " + count);
        count--;
        notify();
    }
}
```

在 Hoare 模型中，当生产者生产或消费者消费之后，`notify()` 会唤醒对方线程，唤醒的线程会立刻开始执行。

### 3. Mesa 模型
Mesa 模型是基于 Hoare 模型的扩展，是现代操作系统中更常见的管程实现模型。与 Hoare 模型不同，Mesa 模型的 `signal()` 只是将等待的线程放入就绪队列，被唤醒的线程并不会立刻执行，而是需要重新竞争锁，可能会导致其他线程先执行。

#### 特点：
- 被 `signal()` 唤醒的线程进入就绪队列，必须再次竞争锁才能执行。
- 可能存在**信号丢失**问题：即线程被唤醒后，条件可能再次不满足。

#### 代码示例：
```java
class MesaMonitor {
    private int count = 0;
    private final int LIMIT = 10;

    public synchronized void produce() throws InterruptedException {
        while (count == LIMIT) {
            wait();
        }
        count++;
        System.out.println("Produced: " + count);
        notify();
    }

    public synchronized void consume() throws InterruptedException {
        while (count == 0) {
            wait();
        }
        System.out.println("Consumed: " + count);
        count--;
        notify();
    }
}
```

在 Mesa 模型中，当生产者或消费者通过 `notify()` 唤醒对方时，唤醒的线程并不会立刻执行，而是进入就绪队列，必须重新争夺锁。

### Java 对管程的实现

Java 提供了内置的 `synchronized` 关键字和 `Object` 类中的 `wait()`、`notify()`、`notifyAll()` 方法来实现管程。Java 的管程更接近 **Mesa 模型**，因为 `notify()` 唤醒的线程不会立即执行，而是进入就绪队列，等待重新竞争锁。

#### Java `synchronized` 代码示例：

```java
class SharedResource {
    private int count = 0;
    private final int LIMIT = 10;

    public synchronized void produce() throws InterruptedException {
        while (count == LIMIT) {
            wait(); // 释放锁并等待
        }
        count++;
        System.out.println("Produced: " + count);
        notify(); // 唤醒其他等待线程
    }

    public synchronized void consume() throws InterruptedException {
        while (count == 0) {
            wait();
        }
        System.out.println("Consumed: " + count);
        count--;
        notify();
    }
}

public class Main {
    public static void main(String[] args) {
        SharedResource resource = new SharedResource();

        Thread producer = new Thread(() -> {
            try {
                while (true) {
                    resource.produce();
                }
            } catch (InterruptedException e) {
                Thread.currentThread().interrupt();
            }
        });

        Thread consumer = new Thread(() -> {
            try {
                while (true) {
                    resource.consume();
                }
            } catch (InterruptedException e) {
                Thread.currentThread().interrupt();
            }
        });

        producer.start();
        consumer.start();
    }
}
```

### 总结
- **Hasen 模型**：线程被唤醒后进入就绪队列，但不保证立即执行。
- **Hoare 模型**：被唤醒的线程立刻执行，并暂时让出当前线程。
- **Mesa 模型**（Java的实现）：线程被 `notify()` 唤醒后进入就绪队列，需要重新竞争锁。

Java 的 `synchronized` 关键字和 `wait()`、`notify()` 实现了接近 Mesa 模型的管程机制，是通过对共享资源进行加锁和同步来避免线程竞争的典型方式。