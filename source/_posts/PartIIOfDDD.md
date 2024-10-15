---
title: DDD的一些随笔感想II
date: 2024-10-15 19:30:50
tags:
- 架构设计 DDD
categories:
- 架构
---



紧接着上一篇，这篇主要讲解聚合。



聚合（Aggregate）是领域驱动设计（DDD）中的核心概念之一，它用于组织和管理业务模型中的复杂性。聚合在DDD中起着重要作用，帮助我们以分层的方式处理业务逻辑，同时保证数据的一致性和完整性。聚合的设计和管理对于保证微服务架构中的稳定性至关重要。

### 1. **聚合的定义**
聚合是领域中的一组相关对象，它们一起处理某个业务场景中的操作。聚合具有以下特征：

- **聚合根（Aggregate Root）**：聚合中的一个主对象，它是外界访问聚合的唯一入口，其他对象通过聚合根进行管理。
- **边界**：聚合将多个实体和值对象组合成一个逻辑单元，它的边界定义了哪些对象属于这个聚合。
- **事务一致性**：在聚合内，所有的操作应当保证数据的一致性。聚合内的修改通常在同一个事务中进行，确保数据的原子性。

#### **聚合的设计原则**
- **封装一致性**：聚合应该封装业务逻辑，并负责维护内部状态的一致性。外部不能直接修改聚合中的对象，而必须通过聚合根进行操作。
- **小聚合原则**：尽可能保持聚合小而简单。一个聚合包含过多的对象会导致系统复杂度和事务管理的难度增加。
- **跨聚合的协作通过事件**：如果多个聚合需要协作完成某个任务，通常通过领域事件进行沟通，而不是直接调用。

### 2. **聚合的示例**
假设我们在一个电子商务系统中有一个订单（Order）聚合。一个订单可能包含多个订单项（OrderItem），同时订单会受到库存的约束和客户支付信息的影响。

#### **1. 聚合内的对象：**
- **订单（Order）**：聚合根，代表整个订单的主要业务逻辑。
- **订单项（OrderItem）**：订单的组成部分，是订单内的子实体。
- **值对象（Value Object）**：如价格、商品数量等，不单独存在，没有独立的生命周期。

#### **2. 订单聚合示例代码：**

```java
@Entity
public class Order {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    private Long customerId;
    private OrderStatus status;

    @OneToMany(cascade = CascadeType.ALL)
    private List<OrderItem> items = new ArrayList<>();

    public Order(Long customerId) {
        this.customerId = customerId;
        this.status = OrderStatus.CREATED;
    }

    // 聚合根负责所有操作
    public void addItem(Long productId, int quantity, Money price) {
        OrderItem item = new OrderItem(productId, quantity, price);
        this.items.add(item);
    }

    public void placeOrder() {
        if (items.isEmpty()) {
            throw new IllegalStateException("订单项不能为空");
        }
        this.status = OrderStatus.PLACED;
        // 发布领域事件，通知其他聚合
        DomainEventPublisher.publish(new OrderPlacedEvent(this.id));
    }

    // getter 和 setter 省略
}
```

#### **订单项（OrderItem）：**

```java
@Entity
public class OrderItem {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    private Long productId;
    private int quantity;
    private Money price;

    // 值对象封装业务逻辑，如价格计算
    public Money calculateTotalPrice() {
        return price.multiply(quantity);
    }

    public OrderItem(Long productId, int quantity, Money price) {
        this.productId = productId;
        this.quantity = quantity;
        this.price = price;
    }

    // getter 和 setter 省略
}
```

#### **值对象（Money）：**

```java
@Embeddable
public class Money {
    private BigDecimal amount;
    private String currency;

    protected Money() {}

    public Money(BigDecimal amount, String currency) {
        this.amount = amount;
        this.currency = currency;
    }

    public Money multiply(int multiplier) {
        return new Money(this.amount.multiply(BigDecimal.valueOf(multiplier)), this.currency);
    }

    // getter 和 setter 省略
}
```

### 3. **聚合的操作**
聚合内的所有操作应该通过聚合根来进行。订单中的子实体（OrderItem）不能独立存在，必须通过订单聚合根来管理。这样可以保证订单的整体一致性。

#### **添加订单项的操作流程：**

1. 外部系统通过订单聚合根调用`addItem()`方法来添加新的订单项。
2. 聚合根负责验证订单项的有效性，并将其添加到订单中。
3. 其他任何修改（如取消订单、修改订单项等）也必须通过订单聚合根来进行。

### 4. **跨聚合的通信**
在实际系统中，往往会有多个聚合相互协作。通常我们采用事件驱动的方式进行聚合间的通信。比如，当订单被创建后，库存聚合可能需要更新库存数量，这个操作通常通过领域事件来实现。

#### **事件通信的代码示例：**

```java
public class OrderPlacedEvent {
    private Long orderId;

    public OrderPlacedEvent(Long orderId) {
        this.orderId = orderId;
    }

    public Long getOrderId() {
        return orderId;
    }
}
```

库存服务可以监听`OrderPlacedEvent`事件并作出相应的处理：

```java
@Service
public class InventoryService {

    @KafkaListener(topics = "OrderPlacedEvent", groupId = "inventory-group")
    public void handleOrderPlaced(OrderPlacedEvent event) {
        // 处理库存逻辑
        System.out.println("Received OrderPlacedEvent for Order ID: " + event.getOrderId());
    }
}
```

### 5. **聚合设计的落地建议**
在实际项目中，聚合设计需要特别注意以下几点：

- **聚合大小控制**：尽量保持聚合的简洁，避免一个聚合根管理过多子实体。如果聚合太大，可能会导致事务长时间持锁，影响性能。
  
- **读写分离**：对于需要高性能读操作的场景，可以考虑使用CQRS（命令查询责任分离）模式，读写操作分开进行，聚合负责写操作，查询则通过专门的查询模型完成。

- **事务管理**：在聚合内部，尽量使用单一事务来处理操作，保证数据的一致性。对于跨聚合的事务，可以考虑使用Saga模式或者事件驱动架构来确保最终一致性。

- **事件驱动架构**：当聚合之间需要协作时，使用事件来进行通信，这样可以解耦聚合间的依赖，同时增强系统的扩展性。

通过以上方式，聚合能够很好地帮助我们管理复杂业务逻辑，并在微服务架构中有效落地DDD的设计思想。在接下来的篇章中，我可以进一步介绍事件驱动架构、Saga模式等内容，以及如何将这些概念结合起来落地到实际项目中。