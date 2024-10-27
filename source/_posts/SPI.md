---
title: SPI在MySQL JDBC Connector 和 Flink Table Connector的实践
date: 2024-10-27 09:55:10
tags:
- SPI Flink MySQL
categories:
- Java
---

最近在看Flink的时候，发现 Flink Table Connector也是使用了SPI技术的，结合很早之前MySQL JDBC Connector的使用，将两者的SPI实践记录下这篇文章。



### 1. Java SPI 技术概述

Java SPI（Service Provider Interface）是一种机制，允许模块化开发和服务提供者的动态加载。通过 SPI，可以在运行时发现和加载实现特定接口的服务提供者。SPI 的基本步骤包括：

- **定义服务接口**：创建一个接口，描述服务的行为。
- **实现服务提供者**：为接口提供多个实现类。
- **注册服务提供者**：在 `META-INF/services` 目录下创建一个文件，文件名为接口的完全限定名，内容为服务提供者的完全限定名。
- **加载服务**：使用 `ServiceLoader` 加载服务实现。

### 2. 实践案例

#### 2.1 MySQL JDBC Connector

##### 1. 定义服务接口

创建一个数据库连接接口：

```java
public interface DatabaseConnector {
    Connection connect(String url, String user, String password) throws SQLException;
}
```

##### 2. 实现服务提供者

实现 MySQL 和 PostgreSQL 的连接器：

```java
public class MySQLConnector implements DatabaseConnector {
    @Override
    public Connection connect(String url, String user, String password) throws SQLException {
        return DriverManager.getConnection(url, user, password);
    }
}

public class PostgreSQLConnector implements DatabaseConnector {
    @Override
    public Connection connect(String url, String user, String password) throws SQLException {
        return DriverManager.getConnection(url, user, password);
    }
}
```

##### 3. 注册服务提供者

在 `META-INF/services/com.example.DatabaseConnector` 文件中列出实现类：

```
com.example.MySQLConnector
com.example.PostgreSQLConnector
```

##### 4. 加载服务

使用 `ServiceLoader` 动态加载数据库连接器：

```java
import java.sql.Connection;
import java.sql.SQLException;
import java.util.ServiceLoader;

public class DatabaseClient {
    public static void main(String[] args) {
        ServiceLoader<DatabaseConnector> loader = ServiceLoader.load(DatabaseConnector.class);
        for (DatabaseConnector connector : loader) {
            try {
                Connection conn = connector.connect("jdbc:mysql://localhost:3306/mydb", "user", "password");
                System.out.println("Connected using: " + connector.getClass().getSimpleName());
            } catch (SQLException e) {
                e.printStackTrace();
            }
        }
    }
}
```

#### 2.2 Flink Table Connector

##### 1. 定义表源接口

定义一个表源接口，用于读取数据流：

```java
public interface TableSource {
    DataStream<Row> getDataStream(StreamExecutionEnvironment env);
}
```

##### 2. 实现表源提供者

实现 MySQL 和 PostgreSQL 的表源：

```java
public class MySQLTableSource implements TableSource {
    @Override
    public DataStream<Row> getDataStream(StreamExecutionEnvironment env) {
        // 实现从 MySQL 数据库读取数据的逻辑
        return env.fromElements(/* 数据元素 */);
    }
}

public class PostgreSQLTableSource implements TableSource {
    @Override
    public DataStream<Row> getDataStream(StreamExecutionEnvironment env) {
        // 实现从 PostgreSQL 数据库读取数据的逻辑
        return env.fromElements(/* 数据元素 */);
    }
}
```

##### 3. 注册表源

在 `META-INF/services/com.example.TableSource` 文件中列出实现类：

```
com.example.MySQLTableSource
com.example.PostgreSQLTableSource
```

##### 4. 加载表源

使用 `ServiceLoader` 加载表源：

```java
import org.apache.flink.streaming.api.environment.StreamExecutionEnvironment;
import org.apache.flink.streaming.api.datastream.DataStream;

public class FlinkClient {
    public static void main(String[] args) {
        StreamExecutionEnvironment env = StreamExecutionEnvironment.getExecutionEnvironment();
        ServiceLoader<TableSource> loader = ServiceLoader.load(TableSource.class);
        for (TableSource source : loader) {
            DataStream<Row> stream = source.getDataStream(env);
            System.out.println("Loaded TableSource: " + source.getClass().getSimpleName());
            // 进行数据流操作...
        }
    }
}
```

### 3. 桥接模式分析

桥接模式的核心思想是将抽象与实现分离，使它们能够独立变化。在 SPI 的上下文中，接口（如 `DatabaseConnector` 和 `TableSource`）代表了抽象部分，而具体的实现类（如 `MySQLConnector` 和 `PostgreSQLConnector`）则是实现部分。通过使用 SPI，我们能够在运行时选择不同的实现，而不需要修改客户端代码。

#### 桥接模式图示

```plaintext
+--------------------+          +--------------------+
|  DatabaseConnector |<>--------|  MySQLConnector    |
+--------------------+          +--------------------+
|                    |          |                    |
|  connect(url, user, password) |                    |
|                    |          +--------------------+
+--------------------+          
|                    |          +--------------------+
|  TableSource       |<>--------|  MySQLTableSource  |
+--------------------+          +--------------------+
|                    |          |                    |
|  getDataStream(env)|          |  getDataStream(env)|
|                    |          |                    |
+--------------------+          +--------------------+
```

### 4. 总结

结合 Java SPI 和桥接模式，我们实现了灵活的服务扩展，允许系统在运行时选择不同的数据库连接和数据源。通过抽象与实现的分离，用户可以方便地扩展系统功能，而无需修改现有代码。这种结构不仅提高了代码的可维护性，也增强了系统的灵活性和可扩展性。桥接模式的引入进一步提升了设计的清晰度，使得不同的实现可以独立演化，适应不断变化的需求。