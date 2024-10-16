---
title: TBB和Java并行库的一些对比
date: 2024-10-16 15:12:19
tags:
- C++ Java
categories:
- C++
---



最近看到TBB的一些资料，然后自然而然地想到了java的一些并行库，就记录了两者的一些简单的比对。



在 C++ 中，Intel TBB（Threading Building Blocks）提供了一套高效的并行编程模型，而在 Java 中，也有丰富的并发库供开发者使用，比如 `ForkJoinPool`、`CompletableFuture`、`Stream.parallel()` 等。通过对这两者的功能进行比对，你可以更好地理解在不同的场景下该如何选择合适的库。

### 场景 1：并行循环处理
C++ TBB 和 Java 都可以通过并行循环提高计算密集型任务的效率。

#### C++: 使用 TBB 并行执行循环
```cpp
#include <tbb/parallel_for.h>
#include <vector>
#include <iostream>

int main() {
    std::vector<int> data(1000, 1);

    tbb::parallel_for(0, (int)data.size(), [&](int i) {
        data[i] += 1;  // 并行处理
    });

    std::cout << "Completed parallel loop with TBB" << std::endl;
}
```

#### Java: 使用 `Stream.parallel()` 执行并行循环
```java
import java.util.ArrayList;
import java.util.List;

public class ParallelStreamExample {
    public static void main(String[] args) {
        List<Integer> data = new ArrayList<>();
        for (int i = 0; i < 1000; i++) {
            data.add(1);
        }

        data.parallelStream().forEach(i -> {
            // 并行处理
            int newValue = i + 1;
        });

        System.out.println("Completed parallel loop with Java Streams");
    }
}
```

**功能比对：**
- **C++ TBB** 提供更细粒度的并行控制，可以直接控制迭代范围和并行执行的任务调度。
- **Java `Stream.parallel()`** 提供的是高级抽象，适合数据流的处理，易于使用，但在更复杂的任务调度上不如 TBB 灵活。

### 场景 2：任务分解与合并
当任务可以递归分解时，TBB 和 Java `ForkJoinPool` 都可以用于任务的拆分和合并。

#### C++: 使用 TBB 的任务分解
```cpp
#include <tbb/task_group.h>
#include <iostream>

int recursiveTask(int n) {
    if (n < 2) return n;

    int x, y;
    tbb::task_group tg;
    tg.run([&] { x = recursiveTask(n - 1); });  // 并行子任务
    tg.run([&] { y = recursiveTask(n - 2); });  // 并行子任务
    tg.wait();

    return x + y;
}

int main() {
    int result = recursiveTask(10);
    std::cout << "Result from TBB: " << result << std::endl;
}
```

#### Java: 使用 `ForkJoinPool` 实现任务分解
```java
import java.util.concurrent.RecursiveTask;
import java.util.concurrent.ForkJoinPool;

public class ForkJoinTaskExample {
    static class FibonacciTask extends RecursiveTask<Integer> {
        private final int n;

        FibonacciTask(int n) {
            this.n = n;
        }

        @Override
        protected Integer compute() {
            if (n < 2) return n;
            FibonacciTask f1 = new FibonacciTask(n - 1);
            f1.fork(); // 异步执行
            FibonacciTask f2 = new FibonacciTask(n - 2);
            return f2.compute() + f1.join(); // 合并结果
        }
    }

    public static void main(String[] args) {
        ForkJoinPool pool = new ForkJoinPool();
        int result = pool.invoke(new FibonacciTask(10));
        System.out.println("Result from ForkJoinPool: " + result);
    }
}
```

**功能比对：**
- **C++ TBB** 中，`task_group` 可以更灵活地定义异步任务，并且能够控制每个任务的启动和合并。
- **Java `ForkJoinPool`** 实现了类似的递归分解，`fork()` 和 `join()` 用于任务的分发和结果的合并。

### 场景 3：复杂任务依赖关系
当任务之间有依赖关系时，TBB 的任务调度和 Java 的 `CompletableFuture` 都能处理任务链的调度。

#### C++: 使用 TBB 任务调度
```cpp
#include <tbb/task_group.h>
#include <iostream>

void complexTask() {
    tbb::task_group tg;
    tg.run([]{ std::cout << "Task 1" << std::endl; });  // 并行任务1
    tg.run([]{ std::cout << "Task 2" << std::endl; });  // 并行任务2
    tg.wait();  // 等待所有任务完成
    std::cout << "All tasks completed" << std::endl;
}

int main() {
    complexTask();
}
```

#### Java: 使用 `CompletableFuture` 实现异步任务链
```java
import java.util.concurrent.CompletableFuture;

public class CompletableFutureExample {
    public static void main(String[] args) {
        CompletableFuture<Void> task1 = CompletableFuture.runAsync(() -> {
            System.out.println("Task 1");
        });

        CompletableFuture<Void> task2 = CompletableFuture.runAsync(() -> {
            System.out.println("Task 2");
        });

        CompletableFuture<Void> allTasks = CompletableFuture.allOf(task1, task2);
        allTasks.thenRun(() -> {
            System.out.println("All tasks completed");
        }).join();  // 等待所有任务完成
    }
}
```

**功能比对：**
- **C++ TBB** 的任务调度更加底层，任务依赖的控制是通过显式调用实现的。
- **Java `CompletableFuture`** 提供了更高抽象的异步任务链控制，可以轻松实现任务依赖、异常处理、任务合并等功能。

### 场景 4：并行数据聚合
在需要对大量数据进行并行处理和聚合时，TBB 的 `parallel_reduce` 和 Java 的 `Collectors.toList()` 都能够高效处理。

#### C++: 使用 TBB `parallel_reduce`
```cpp
#include <tbb/parallel_reduce.h>
#include <vector>
#include <iostream>

int main() {
    std::vector<int> data(1000, 1);

    int sum = tbb::parallel_reduce(
        tbb::blocked_range<size_t>(0, data.size()),
        0,
        [&](const tbb::blocked_range<size_t>& r, int init) {
            for (size_t i = r.begin(); i != r.end(); ++i) {
                init += data[i];  // 局部和
            }
            return init;
        },
        std::plus<int>()  // 合并操作
    );

    std::cout << "Sum using TBB: " << sum << std::endl;
}
```

#### Java: 使用 `Stream.parallel()` 和 `Collectors`
```java
import java.util.ArrayList;
import java.util.List;

public class ParallelStreamSum {
    public static void main(String[] args) {
        List<Integer> data = new ArrayList<>();
        for (int i = 0; i < 1000; i++) {
            data.add(1);
        }

        int sum = data.parallelStream()
                      .reduce(0, Integer::sum);  // 并行聚合

        System.out.println("Sum using Java Streams: " + sum);
    }
}
```

**功能比对：**
- **C++ TBB** 提供了 `parallel_reduce`，允许在大数据集合上并行执行自定义的聚合逻辑。
- **Java `Stream.parallel()`** 提供了流式处理，简化了并行计算和聚合，但在复杂聚合操作上不如 TBB 灵活。

### 总结
- **C++ TBB** 提供了对并发任务的高度灵活控制，更适合复杂的并行任务和高性能场景。
- **Java 并发库** 提供了较高层的抽象，更易于使用，特别是对于常见的并行计算任务（如数据流、异步任务链等）。

在选择并发工具时，可以根据任务复杂性、代码维护性以及性能要求来进行取舍。