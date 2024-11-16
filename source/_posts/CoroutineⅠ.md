---
title: 协程调用系统函数会发生什么？
date: 2024-11-16 10:24:15
tags:
- Coroutine 系统调用
categories:
- Coroutine
---

前几天被人问及协程调用系统函数会发生什么，这个问题还是很有趣的，不同的语言和模型实现的机制会有所不同，今天特地用几种流行的语言实现研究了一下，结果也是非常有趣。



在协程中调用系统调用函数是否成功，取决于以下几个因素： 

1. **系统调用的类型**（阻塞/非阻塞）。  
2. **协程的实现机制**（用户态、内核态，或者语言特性）。  
3. **语言运行时及其调度模型**。  

不同语言和运行时对协程的实现差异会导致行为不同。以下是详细分析：

---

## **1. 系统调用和协程的交互：基本原理**
- **系统调用（System Call）**  
  系统调用通常会涉及操作系统内核。如果是 **阻塞型系统调用**，线程会被挂起，直到调用完成。  
- **协程**  
  协程是轻量级的用户态调度任务，本质上是线程内的逻辑划分。协程本身无法直接控制线程的阻塞行为。

如果协程中的阻塞型系统调用阻塞了线程，那么该线程上的所有协程都会被阻塞。

---

## **2. 不同语言对协程和系统调用的处理**
### **(1) C++（依赖库实现，如 Boost 或 C++20 协程）**
- **协程实现**  
  - C++协程通常是用户态协程（如 `Boost.Context` 或 C++20 `co_await`）。
  - 调用阻塞系统调用时，整个线程会被阻塞，因为没有协程调度机制去避免线程阻塞。
- **结果**  
  - 系统调用会成功，但调用期间会导致整个线程和其协程挂起。
  - 需要依赖非阻塞系统调用（如 `epoll`、`io_uring`）或使用第三方 I/O 库（如 libuv）实现高效协作。

### 示例代码

```cpp
#include <iostream>
#include <chrono>
#include <thread>
#include <coroutine>

struct Task {
    struct promise_type {
        Task get_return_object() { return {}; }
        std::suspend_never initial_suspend() { return {}; }
        std::suspend_never final_suspend() noexcept { return {}; }
        void return_void() {}
        void unhandled_exception() {}
    };
};

Task myCoroutine(int id) {
    std::cout << "Coroutine " << id << " starts.\n";
    std::this_thread::sleep_for(std::chrono::seconds(2)); // 阻塞系统调用
    std::cout << "Coroutine " << id << " ends.\n";
    co_return;
}

int main() {
    myCoroutine(1);
    myCoroutine(2);
    std::cout << "All done.\n";
    return 0;
}
```

输出

```
Coroutine 1 starts.
(2秒后)
Coroutine 1 ends.
Coroutine 2 starts.
(2秒后)
Coroutine 2 ends.
All done.
```

### 

### **(2) Java（如虚拟线程或常规线程池协程）**
- **协程实现**  
  - Java 协程（如 Loom 项目的虚拟线程）利用语言级的调度模型，允许阻塞系统调用时挂起虚拟线程，而不是物理线程。
- **结果**  
  - 系统调用会成功。对于虚拟线程，阻塞型系统调用只会挂起当前协程，不会阻塞底层物理线程。
  - 传统线程池模型中，阻塞型调用会阻塞线程，进而影响其他协程。

### 示例代码

```java
public class JavaCoroutineExample {
    public static void main(String[] args) throws Exception {
        System.out.println("Using traditional thread pool:");
        runWithThreadPool();

        System.out.println("\nUsing virtual threads:");
        runWithVirtualThreads();
    }

    static void runWithThreadPool() throws InterruptedException {
        var executor = java.util.concurrent.Executors.newFixedThreadPool(2);

        executor.submit(() -> {
            System.out.println("Task 1 starts.");
            try {
                Thread.sleep(2000); // 阻塞系统调用
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            System.out.println("Task 1 ends.");
        });

        executor.submit(() -> {
            System.out.println("Task 2 starts.");
            try {
                Thread.sleep(2000); // 阻塞系统调用
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            System.out.println("Task 2 ends.");
        });

        executor.shutdown();
        executor.awaitTermination(5, java.util.concurrent.TimeUnit.SECONDS);
    }

    static void runWithVirtualThreads() throws InterruptedException {
        var executor = java.util.concurrent.Executors.newThreadPerTaskExecutor(
                java.util.concurrent.Thread.ofVirtual().factory()
        );

        executor.submit(() -> {
            System.out.println("Task 1 starts.");
            try {
                Thread.sleep(2000); // 阻塞系统调用
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            System.out.println("Task 1 ends.");
        });

        executor.submit(() -> {
            System.out.println("Task 2 starts.");
            try {
                Thread.sleep(2000); // 阻塞系统调用
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            System.out.println("Task 2 ends.");
        });

        executor.shutdown();
        executor.awaitTermination(5, java.util.concurrent.TimeUnit.SECONDS);
    }
}
```

输出

```
Using traditional thread pool:
Task 1 starts.
(2秒后)
Task 1 ends.
Task 2 starts.
(2秒后)
Task 2 ends.

Using virtual threads:
Task 1 starts.
Task 2 starts.
(2秒后)
Task 1 ends.
Task 2 ends.
```

### 

### **(3) Go（基于 Goroutine 的实现）**
- **协程实现**  
  - Go 的 Goroutine 是 M:N 的用户态线程模型。调度器能够检测到阻塞系统调用，通过线程劫持将其他 Goroutine 调度到不同的线程上执行。
- **结果**  
  - 系统调用会成功。即使是阻塞型系统调用，也只会阻塞当前 Goroutine，其他 Goroutine 不受影响。
  - Go 调度器动态分配物理线程来避免阻塞问题。

### 示例代码

```go
package main

import (
	"fmt"
	"time"
)

func task(id int) {
	fmt.Printf("Task %d starts.\n", id)
	time.Sleep(2 * time.Second) // 阻塞系统调用
	fmt.Printf("Task %d ends.\n", id)
}

func main() {
	go task(1)
	go task(2)
	time.Sleep(3 * time.Second) // 等待所有 Goroutine 完成
	fmt.Println("All done.")
}
```

输出

```
Task 1 starts.
Task 2 starts.
(2秒后)
Task 1 ends.
Task 2 ends.
All done.
```

### 

### **(4) Python（基于 `asyncio` 或协程库实现）**
- **协程实现**  
  - Python 的 `asyncio` 使用事件循环管理协程，依赖非阻塞 I/O 实现并行任务。
- **结果**  
  - 如果在 `asyncio` 中调用阻塞型系统调用，会阻塞事件循环，导致整个程序挂起。
  - 为避免这种问题，可以将阻塞任务交给线程池或进程池处理（如 `loop.run_in_executor`）。

### 示例代码

```python
import asyncio
import time

async def task(id):
    print(f"Task {id} starts.")
    time.sleep(2)  # 阻塞系统调用
    print(f"Task {id} ends.")

async def main():
    await asyncio.gather(task(1), task(2))

asyncio.run(main())
```

### 输出

```
Task 1 starts.
(2秒后)
Task 1 ends.
Task 2 starts.
(2秒后)
Task 2 ends.
```

### 改进（避免阻塞）

使用 `await asyncio.sleep()` 替代 `time.sleep()`：

```python
async def task(id):
    print(f"Task {id} starts.")
    await asyncio.sleep(2)  # 非阻塞
    print(f"Task {id} ends.")
```

### 输出

```
Task 1 starts.
Task 2 starts.
(2秒后)
Task 1 ends.
Task 2 ends.
```

---



---

## **3. 总结对比**

| 语言/运行时 | 调用阻塞系统调用的行为                           | 协程调度模型                | 解决方案                      |
| ----------- | ------------------------------------------------ | --------------------------- | ----------------------------- |
| **C++**     | 阻塞整个线程和所有协程                           | 用户态协程                  | 使用非阻塞 I/O 或库支持       |
| **Java**    | 阻塞虚拟线程（对物理线程无影响）或阻塞线程池线程 | 虚拟线程/线程池协程         | 使用 Loom 虚拟线程优化        |
| **Go**      | 只阻塞当前 Goroutine，其他 Goroutine 不受影响    | M:N 用户态协程              | 调度器自动分配线程            |
| **Python**  | 阻塞事件循环，导致所有协程暂停                   | 单线程事件循环（`asyncio`） | 使用线程池/进程池避免阻塞问题 |

---

## **4. 注意事项**
1. **阻塞型调用和协程性能瓶颈**
   - 阻塞型调用会带来性能问题，尤其在高并发环境下。
   - 推荐使用非阻塞 I/O 或异步库（如 `aiofiles`、`aiohttp`）。

2. **运行时支持的重要性**
   - 高效协程调度模型（如 Go 和 Loom）能够避免阻塞型调用对系统整体性能的影响。

3. **上下文切换代价**
   - 协程通过减少线程的上下文切换提高性能，但这并不意味着能完全替代非阻塞 I/O。

---

如果目标是实现高效的 I/O 密集型任务，推荐采用支持非阻塞 I/O 和高效协程模型的语言/运行时，比如 Go 或 Java 的 Loom。