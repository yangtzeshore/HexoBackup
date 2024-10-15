---
title: DDD的一些随笔感想IV
date: 2024-10-15 19:41:34
tags:
- 架构设计 DDD
categories:
- 架构
---



紧接着上一篇，这篇主要讲解Saga模式。



Saga模式是一种用于处理分布式系统中长事务（Long-Running Transactions）的模式。它通过将一个大事务拆分成一系列小的局部事务（Local Transactions），每个局部事务都是独立提交的。如果某个局部事务失败，系统会通过补偿操作来回滚之前已成功的事务，确保系统达到最终一致性。

Saga模式主要用于解决分布式事务中的一致性问题，在微服务架构中尤为重要，因为微服务通常具有独立的数据存储，不适合使用传统的分布式事务机制。

### 1. **Saga模式的工作原理**
Saga模式的核心思想是将一个大事务分解成多个局部事务。每个局部事务会对自己的数据进行修改，并在其完成时提交。每个局部事务之后，系统会检查整个流程是否可以继续进行。如果发生错误，系统执行补偿逻辑，撤销之前的局部事务。

Saga模式有两种主要实现方式：
- **编排式（Orchestration-based Saga）**
- **协同式（Choreography-based Saga）**

### 2. **Saga的两种实现方式**
#### 1. **编排式 Saga**
编排式 Saga 中，有一个专门的服务（Saga Orchestrator）来负责管理整个 Saga 事务的执行流程。编排器负责调用各个局部事务，并根据事务的结果决定下一个步骤是继续还是执行补偿操作。

**优点：**

- 事务的执行流程集中管理，清晰易懂。
- 控制逻辑可以集中到编排器中，便于维护和调试。

**缺点：**

- 编排器成为系统的中心，容易形成单点瓶颈。
- 编排器需要了解每个局部事务的执行细节，增加了耦合。

##### **示例：电子商务系统中的订单处理**

在一个电子商务系统中，当用户下单时，可能会涉及到以下几个操作：
1. 创建订单。
2. 扣减库存。
3. 扣除用户的账户余额。

这些操作可以组成一个 Saga，任何一个步骤失败都需要撤销前面的操作。编排器负责按照顺序调用每个服务，并在必要时进行补偿。

**Saga 编排器的伪代码：**

```java
public class OrderSagaOrchestrator {

    @Autowired
    private OrderService orderService;

    @Autowired
    private InventoryService inventoryService;

    @Autowired
    private PaymentService paymentService;

    public void createOrder(OrderRequest request) {
        try {
            // Step 1: 创建订单
            Order order = orderService.createOrder(request);

            // Step 2: 扣减库存
            inventoryService.deductInventory(order.getProductId(), order.getQuantity());

            // Step 3: 扣除用户账户余额
            paymentService.deductBalance(order.getUserId(), order.getTotalAmount());

            // 如果所有步骤成功，订单处理完成
        } catch (Exception e) {
            // 如果发生错误，执行补偿
            compensate(order);
        }
    }

    private void compensate(Order order) {
        // 补偿逻辑，如恢复库存、退还余额等
        inventoryService.revertInventory(order.getProductId(), order.getQuantity());
        paymentService.revertBalance(order.getUserId(), order.getTotalAmount());
        orderService.cancelOrder(order.getId());
    }
}
```

#### 2. **协同式 Saga**
协同式 Saga 没有集中管理的编排器，所有服务通过监听事件来协作完成事务。每个服务执行自己的局部事务，并在成功后发布一个事件，通知下一个服务执行。如果某个服务失败，它也会发布一个补偿事件，撤销之前已完成的操作。

**优点：**

- 服务之间更为松耦合，各自只关心自己应该处理的事务。
- 没有单点控制器，减少了瓶颈和耦合。

**缺点：**

- 事务执行流程分散，逻辑较为复杂，难以追踪。
- 难以调试，补偿逻辑分散在多个服务中，维护成本高。

##### **示例：同样的订单处理 Saga（协同式实现）**

在协同式实现中，每个服务在完成操作后发布事件，其他服务通过事件监听器进行相应处理。补偿操作同样由事件触发。

**服务发布事件：**

```java
@Service
public class OrderService {

    @Autowired
    private KafkaTemplate<String, OrderCreatedEvent> kafkaTemplate;

    public void createOrder(OrderRequest request) {
        Order order = new Order(request.getUserId(), request.getProductId(), request.getQuantity());
        // 保存订单
        orderRepository.save(order);

        // 发布订单创建事件
        kafkaTemplate.send("OrderCreatedTopic", new OrderCreatedEvent(order));
    }

    @KafkaListener(topics = "OrderCreationFailedTopic")
    public void handleOrderCreationFailed(OrderCreationFailedEvent event) {
        // 取消订单
        orderRepository.deleteById(event.getOrderId());
    }
}
```

**库存服务监听事件并发布补偿事件：**

```java
@Service
public class InventoryService {

    @KafkaListener(topics = "OrderCreatedTopic")
    public void handleOrderCreated(OrderCreatedEvent event) {
        try {
            // 扣减库存
            inventoryRepository.deduct(event.getProductId(), event.getQuantity());
        } catch (Exception e) {
            // 库存不足，发布补偿事件
            kafkaTemplate.send("OrderCreationFailedTopic", new OrderCreationFailedEvent(event.getOrderId()));
        }
    }
}
```

### 3. **Saga模式的事务补偿**
Saga模式使用补偿事务来实现回滚。补偿事务的逻辑与正向事务的执行逻辑相反，它用于撤销之前已经提交的局部事务。例如，在订单处理过程中，如果库存扣减成功但支付失败，系统需要通过补偿事务将之前扣减的库存恢复。

**补偿事务的关键点：**
- 补偿事务应该是幂等的，以确保多次执行不会产生副作用。
- 补偿事务的执行顺序应与正向事务的执行顺序相反。

### 4. **Saga模式的优缺点**
**优点：**

- **去中心化**：尤其在协同式 Saga 中，各个服务之间松耦合，便于扩展和维护。
- **最终一致性**：通过异步事件和补偿机制，系统可以在不同服务之间达到最终一致性，适合分布式系统中的长事务。
- **高可用性**：Saga模式避免了长时间锁住资源的问题，服务可以在局部事务中独立处理，提升了系统的可用性。

**缺点：**

- **复杂性高**：补偿逻辑的设计和实现增加了系统的复杂性，尤其是协同式 Saga 中，服务之间的事件交互难以跟踪和调试。
- **不适合强一致性场景**：Saga模式适合最终一致性的场景，但对于需要强一致性的系统，如金融领域的结算系统，Saga可能不适用。
  
### 5. **Saga模式的实际应用场景**
Saga模式特别适合以下场景：
- **电商系统中的订单处理**：订单创建、库存扣减、支付等流程可以使用 Saga 保证最终一致性。
- **航班和酒店预订系统**：预订多个资源时，可以将每个资源的预订作为局部事务，确保在某个资源预订失败时能进行补偿。
- **银行转账系统**：当资金转移涉及多个账户或银行时，Saga 模式可以确保转账操作的最终一致性。

### 6. **项目中的 Saga 模式落地建议**
在项目中落地 Saga 模式时，应考虑以下几点：
- **事务补偿机制的设计**：每个局部事务都需要有对应的补偿事务，并确保补偿逻辑的幂等性。
- **事件驱动与消息中间件的选择**：选择合适的消息中间件（如 Kafka、RabbitMQ）来传递事务事件，确保事件的可靠传递与处理。
- **监控与可观察性**：在实际系统中，Saga 事务往往跨越多个服务，因此需要良好的监控机制来跟踪每个局部事务的状态，并及时发现和处理错误。
- **错误处理与超时管理**：在 Saga 模式下，某些局部事务可能会因为长时间未能完成而超时。因此，系统需要设计机制来检测并处理超时事务。

总结来说，Saga模式提供了一种灵活的方式来处理分布式系统中的事务管理问题，通过补偿逻辑实现最终一致性。根据业务需求选择合适的实现方式（编排式或协同式），并设计健壮的补偿机制，能够有效提高系统的稳定性和可扩展性。

接下来，我会继续讨论如何结合事件驱动架构和 Saga 模式，进一步提高微服务系统的弹性与扩展性。