---
title: Neo4j 学习笔记
date: 2025-12-28 19:54:30
tags:
  - Neo4j
---

## Neo4j 的简介

> Neo4j 存储的数据元素包括节点 、连接节点的边以及节点和边的属性。其开发者将其描述为符合 ACID 标准的事务型数据库，并具备原生图存储和处理功能。 Neo4j 提供非开源的 “社区版”，该版本采用 GNU 通用公共许可证的修改版授权；此外，它还提供在线备份和高可用性扩展，这些扩展采用闭源商业许可证授权。 Neo 也以闭源商业条款授权 Neo4j 及其这些扩展。
>
> 官网：https://neo4j.com/
>
> DB Rank: https://db-engines.com/en/ranking
>
> deepseek: https://chat.deepseek.com/share/hhhtx7rvss019bdq5q

## 底层原理

- 图论

## 使用场景

选择 **Neo4j 图数据库**，本质上是在选择一种**以“关系”为一等公民的数据建模方式**。当你的数据和问题 **“关系复杂、关系重要、关系需要高频遍历”** 时，就非常适合用 Neo4j。

下面我用 **判断标准 + 典型场景 + 不适合的情况** 给你一套清晰的决策指南。

---

### 一、什么时候「一定要」考虑 Neo4j？

你可以用下面这 6 个问题自检，只要 **满足 2–3 条以上**，Neo4j 基本就是正确选择。

#### 1️⃣ 数据的「关系」比「数据本身」更重要

> 你关心的是：**谁和谁有什么关系？关系链怎么走？**

例如：

- A → 认识 → B → 投资 → C → 控股 → D
- 用户 → 点击 → 商品 → 属于 → 品类 → 关联 → 其他商品

✅ **关系是核心价值** → 用 Neo4j
❌ 只是字段附属 → 用关系型数据库

---

#### 2️⃣ 需要频繁做「多跳关系查询」（2 跳、3 跳、N 跳）

典型问题：

- “这个人朋友的朋友都有哪些？”
- “与该商品 3 步之内有关联的所有商品”
- “这个账号是否和黑名单存在 4 层以内的关联？”

在 MySQL 中：

```sql
JOIN JOIN JOIN JOIN ...（性能指数级下降）
```

在 Neo4j 中：

```cypher
MATCH (a)-[*1..4]->(b)
```

🚀 **查询复杂关系路径 = 图数据库主场**

---

#### 3️⃣ 关系数量巨大，且远大于实体数量

例如：

- 1 亿用户
- 但有 30 亿条「关注 / 交易 / 推荐 / 引用」关系

📌 **关系 ≫ 节点**
→ Neo4j 使用邻接表 + 指针直连，遍历复杂度几乎与数据量无关

---

#### 4️⃣ 关系类型多、经常变化

比如：

- 朋友
- 同事
- 投资
- 家人
- 推荐
- 转账
- 点赞
- 屏蔽

关系是**带类型、方向、属性的实体**，而不是中间表。

Neo4j：

```text
(User)-[:INVEST {amount: 100w}]->(Company)
```

MySQL：

- 改表结构
- 建新中间表
- 改 ORM
- 改 SQL

📌 **关系经常变 → Neo4j 成本最低**

---

#### 5️⃣ 需要做「关系推理 / 图算法」

例如：

- 推荐系统（相似用户、相似商品）
- 社交影响力（PageRank）
- 路径最短 / 最相关
- 社区发现（聚类）

Neo4j 自带：

- Graph Data Science（GDS）
- PageRank
- Louvain
- BFS / DFS
- 最短路径

👉 如果你在做 **“结构洞 / 影响力 / 推荐”**，Neo4j 非常合适

---

#### 6️⃣ 数据结构天然是「图」

如果你在画白板时，很自然画出的是：

```
A —— B —— C
 \         |
   —— D ——
```

而不是：

```
表1、表2、表3
```

那你已经在用“图思维”了。

---

### 二、最典型的使用场景（高命中）

#### 🔹 1. 社交网络

- 好友 / 关注 / 拉黑
- 二度、三度人脉
- 社交推荐

📌 Facebook、LinkedIn 典型图模型

---

#### 🔹 2. 风控 / 反欺诈（非常适合）

- 账户关联
- 设备共享
- 资金路径
- “是否存在关联黑产网络？”

👉 **银行 / 支付 / 电商风控核心**

---

#### 🔹 3. 推荐系统（关系型推荐）

- 用户 ↔ 商品
- 商品 ↔ 商品
- 用户 ↔ 兴趣 ↔ 商品

比纯协同过滤更可解释

---

#### 🔹 4. 知识图谱

- 实体（人、公司、概念）
- 关系（属于、影响、包含、因果）

📌 Neo4j = 知识图谱事实标准之一

---

#### 🔹 5. IT 运维 / 拓扑 / 依赖分析

- 服务依赖
- 网络拓扑
- 微服务调用链

---

#### 🔹 6. 代码分析 / 依赖图

- 函数调用
- 模块依赖
- 类继承

---

### 三、什么时候「不该」用 Neo4j？

下面这些场景，用 Neo4j 反而是 **反模式**：

#### ❌ 1. 纯 CRUD + 表结构稳定

- 订单表
- 用户表
- 财务流水

👉 MySQL / PostgreSQL 更合适

---

#### ❌ 2. 大量聚合 / 报表 / OLAP

- SUM / COUNT / GROUP BY
- 财务统计
- BI 报表

👉 ClickHouse / PostgreSQL / Hive

---

#### ❌ 3. 只是“有外键”，但不需要多跳

- 一对多
- 多对多（但最多 1 跳）

👉 没必要上图数据库

---

#### ❌ 4. 强事务 + 高并发写入

Neo4j 写入吞吐量 **不如** 传统关系型数据库

---

### 四、一个简单的决策口诀

> **数据是点，关系是网，查询在网里跑 —— 用 Neo4j** > **数据是表，查询在字段上算 —— 用关系型数据库**

---

### 五、给你一个工程实践建议（很重要）

📌 **Neo4j 不应该“替代” MySQL，而是“补充”**

常见架构：

```
MySQL / PostgreSQL：事实数据、交易数据
Neo4j：关系、推理、推荐、风控
```

通过：

- Binlog / CDC
- MQ
- 定期同步

把「关系视图」同步到 Neo4j。

## 对比关系型数据库

| 关系型数据库（RDBMS） | 图数据库   |
| --------------------- | ---------- |
| 表                    | 图         |
| 行                    | 节点       |
| 列和数据              | 属性和数据 |
| 约束                  | 关系       |

## 安装

- 基于 `Docker` 安装

```bash
docker run \
    -d \
    --name neo4j \
    -p 7474:7474 \
    -p 7687:7687 \
    -e NEO4J_AUTH=neo4j/ABC123456 \
    -v ./neo4j_data:/data \
    neo4j:latest
```

- 部署完成后，访问 `http://localhost:7474/browser/`

## Cypher 查询语言

### 创建节点 CREATE

```cypher
create(emp:Employee)
create(dept:Department)
```

### 检索语句 MATCH

```cypher
match(emp:Employee) return emp
```

> `match` 要与 `return` 连用

### RETURN 返回

- 检索节点的某些属性
- 检索节点的所有属性
- 检索节点和关联关系的某些属性
- 检索节点和关联关系的所有属性

```cypher
create (e:customer {id:"1001",name:"shenjy",location:"hangzhou"})
match (e:customer) return e.id,e.name,e.location

create (cc:CreditCard {id:"1", number:"348612836412863484",cvv:"888", expireDate:"26/17"})
```

### 创建关系 CREATE

- 单向关系
- 双向关系

- 多标签

```cypher
create (m:User)
create (m:电影:图片:Movie)
MATCH (n:`电影`) RETURN n LIMIT 25

create (p1:Profile1)-[r1:喜欢]->(p2:Profile2)
MATCH p=()-[r:`喜欢`]->() RETURN p LIMIT 25
```

### WHERE 条件

```cypher
MATCH (n:Employee) RETURN n LIMIT 25

create (e:Employee {id:1,name:"张三",age:18});
create (e:Employee {id:2,name:"李四",age:19});
create (e:Employee {id:3,name:"王五",age:20});
create (e:Employee {id:4,name:"赵六",age:21});
create (e:Employee {id:5,name:"田七",age:22})

match (e:Employee) WHERE e.age > 18 return e limit 10
match (e:Employee) WHERE e.age > 18 or e.id = 1 return e limit 10
match (c:customer),(cc:CreditCard) where c.id="1001" or cc.id="1" return c, cc
```

### 对已有的节点添加关系

```cypher
match (c:customer),(cc:CreditCard) where c.id="1001" or cc.id="1" return c, cc

match (e:Employee), (cc:CreditCard) where e.name = "张三" and cc.id = "1" create (e)-[r:uses{shopdate:"2025/07/07", price: 6000}]->(cc) return r
```

### 删除节点|关系

- 删除节点
- 删除节点以及相关节点和关系

```cypher
match (e:Employee) where e.name = "王五" delete e

match (u:User) delete u

match(cc:CreditCard)-[rel]-(e:Employee) return cc,rel,e
match(cc:CreditCard)-[rel]-(e:Employee) delete cc,rel,e

# 先删除关系
MATCH (n)-[r]-()
WHERE id(n) = 11
DELETE r

# 在删除节点
MATCH (n)
WHERE id(n) = 11 or id(n) = 12
DELETE n
```

### REMOVE 命令

- 向现有节点或关系添加或删除属性

```cypher
create(:Book{id:1,title:"Neo4j深入浅出",page:300,price:66})

match(book:Book) remove book.page return book

match(m:Movie) remove m:`电影`
```

### SET 命令

- 向现有的节点或关系添加属性值

```cypher
-- 更新属性
match (b:Book) set b.title = "Neo4j实战笔记" return b

-- 添加属性
match(b:Book) set b.pages = 300 return b
```

### ORDER BY

```cypher
match(e:Employee) order by e.age asc return e

match(e:Employee) order by e.age desc return e
```

### UNION | UNION ALL

```cypher
-- UNION ALL
match (e:Employee) where e.age > 20 return e
UNION ALL
match (e:Employee) where e.age >=18 and e.age <=20 return e

-- UNION
match (e:Employee) where e.age > 18 return e.age as age
UNION
match (e:Employee) where e.age >=18 and e.age <=20 return e.age as age
```

### SKIP | LIMIT 字句

```cypher
match(e:Employee) order by e.age skip 0 limit 2 return e

match(e:Employee) return e limit 10
```

### MERGE 命令

- 没有创建，有的话，不做任何操作

```cypher
merge (u:User{id:2,name:"李四"})
-- Added 1 label, created 1 node, set 2 properties, completed after 11 ms.
merge (u:User{id:2,name:"李四"})
-- (no changes, no records)
```

### NULL 值

```cypher
match(e:Employee) return e.id, e.name, e.age
╒════╤══════╤═════╕
│e.id│e.name│e.age│
╞════╪══════╪═════╡
│null│null  │null │
├────┼──────┼─────┤
│1   │"张三"  │18   │
├────┼──────┼─────┤
│4   │"赵六"  │21   │
├────┼──────┼─────┤
│5   │"田七"  │22   │
├────┼──────┼─────┤
│2   │"李四"  │19   │
├────┼──────┼─────┤
│3   │"王五"  │20   │
└────┴──────┴─────┘

match(e:Employee) where e.id is null return e.id, e.name, e.age
╒════╤══════╤═════╕
│e.id│e.name│e.age│
╞════╪══════╪═════╡
│null│null  │null │
└────┴──────┴─────┘

match(e:Employee) where e.id is not null return e.id, e.name, e.age
╒════╤══════╤═════╕
│e.id│e.name│e.age│
╞════╪══════╪═════╡
│1   │"张三"  │18   │
├────┼──────┼─────┤
│4   │"赵六"  │21   │
├────┼──────┼─────┤
│5   │"田七"  │22   │
├────┼──────┼─────┤
│2   │"李四"  │19   │
├────┼──────┼─────┤
│3   │"王五"  │20   │
└────┴──────┴─────┘
```

### IN 命令

```cypher
match(e:Employee) where e.age in [18,22] return e
╒══════════════════════════════════════╕
│e                                     │
╞══════════════════════════════════════╡
│(:Employee {name: "张三",id: 1,age: 18})│
├──────────────────────────────────────┤
│(:Employee {name: "田七",id: 5,age: 22})│
└──────────────────────────────────────┘
```

### Neo4j 中的函数

> https://neo4j.com/docs/cypher-manual/current/functions/

### 关系函数

```cypher
match(a:Profile1)-[r:`喜欢`]->(b:Profile2) return a,r,b

match(a:Profile1)-[r:`喜欢`]->(b:Profile2) return startnode(r), endnode(r), id(r), type(r)

╒════════════╤═══════════╤═════╤═══════╕
│startnode(r)│endnode(r) │id(r)│type(r)│
╞════════════╪═══════════╪═════╪═══════╡
│(:Profile1) │(:Profile2)│0    │"喜欢"  │
└────────────┴───────────┴─────┴───────┘
```

### 与 SpringBoot 集成

> 参考文档：https://spring.io/projects/spring-data-neo4j
> springboot demo: https://github.com/neo4j-examples/movies-java-bolt

## 📚 所有关键字目录

1. [查询关键字](#1-查询关键字)
2. [数据操作关键字](#2-数据操作关键字)
3. [模式匹配关键字](#3-模式匹配关键字)
4. [聚合和函数关键字](#4-聚合和函数关键字)
5. [子查询和过程关键字](#5-子查询和过程关键字)
6. [约束和索引关键字](#6-约束和索引关键字)
7. [事务控制关键字](#7-事务控制关键字)
8. [管理关键字](#8-管理关键字)

---

## 1. **查询关键字**

### **MATCH** - 匹配模式

```cypher
-- 匹配所有 Person 节点
MATCH (p:Person)
RETURN p

-- 匹配特定属性的节点
MATCH (p:Person {name: '张三'})
RETURN p

-- 匹配关系和节点
MATCH (p:Person)-[:FRIEND]->(f:Person)
RETURN p.name, f.name

-- 可变长度路径
MATCH (a:Person)-[:FRIEND*1..3]->(b:Person)
RETURN a.name, b.name
```

### **OPTIONAL MATCH** - 可选匹配

```cypher
-- 即使没有匹配也返回结果
MATCH (p:Person {name: '张三'})
OPTIONAL MATCH (p)-[:FRIEND]->(f)
RETURN p.name, f.name

-- 复杂的可选匹配
MATCH (p:Person)
OPTIONAL MATCH (p)-[:WORKS_AT]->(c:Company)
OPTIONAL MATCH (p)-[:LIVES_IN]->(city:City)
RETURN p.name, c.name, city.name
```

### **WHERE** - 条件过滤

```cypher
-- 基本条件
MATCH (p:Person)
WHERE p.age > 30
RETURN p.name

-- 多条件
MATCH (p:Person)
WHERE p.age > 30 AND p.city = '北京'
RETURN p

-- 正则表达式
MATCH (p:Person)
WHERE p.name =~ '张.*'
RETURN p

-- 属性存在性检查
MATCH (p:Person)
WHERE EXISTS(p.email)
RETURN p

-- IN 操作符
MATCH (p:Person)
WHERE p.city IN ['北京', '上海', '广州']
RETURN p
```

### **RETURN** - 返回结果

```cypher
-- 返回节点
MATCH (p:Person)
RETURN p

-- 返回属性
MATCH (p:Person)
RETURN p.name, p.age

-- 返回表达式
MATCH (p:Person)
RETURN p.name AS 姓名,
       p.age AS 年龄,
       CASE
         WHEN p.age < 30 THEN '青年'
         WHEN p.age < 50 THEN '中年'
         ELSE '老年'
       END AS 年龄段

-- DISTINCT 去重
MATCH (p:Person)
RETURN DISTINCT p.city

-- 返回所有变量
MATCH (p:Person)-[r]->(n)
RETURN *
```

### **WITH** - 传递结果

```cypher
-- 传递结果并过滤
MATCH (p:Person)
WITH p
WHERE p.age > 30
RETURN p.name

-- 聚合后传递
MATCH (p:Person)
WITH p.city AS city, count(p) AS count
WHERE count > 100
RETURN city, count

-- 排序后分页
MATCH (p:Person)
WITH p
ORDER BY p.age DESC
SKIP 10
LIMIT 5
RETURN p.name, p.age
```

### **UNWIND** - 展开列表

```cypher
-- 展开列表
WITH ['张三', '李四', '王五'] AS names
UNWIND names AS name
RETURN name

-- 创建多个节点
WITH ['北京', '上海', '广州', '深圳'] AS cities
UNWIND cities AS city
CREATE (:City {name: city})
RETURN count(*)

-- 展开嵌套结构
WITH [
  {name: '张三', age: 30},
  {name: '李四', age: 25}
] AS people
UNWIND people AS person
CREATE (p:Person)
SET p = person
RETURN p
```

### **ORDER BY** - 排序

```cypher
-- 升序排序
MATCH (p:Person)
RETURN p.name, p.age
ORDER BY p.age ASC

-- 降序排序
MATCH (p:Person)
RETURN p.name, p.salary
ORDER BY p.salary DESC

-- 多字段排序
MATCH (p:Person)
RETURN p.city, p.name, p.age
ORDER BY p.city, p.age DESC
```

### **SKIP** - 跳过记录

```cypher
-- 分页查询
MATCH (p:Person)
RETURN p.name
ORDER BY p.name
SKIP 20
LIMIT 10
```

### **LIMIT** - 限制记录数

```cypher
-- 限制返回数量
MATCH (p:Person)
RETURN p.name
LIMIT 10

-- 分页
MATCH (p:Person)
RETURN p.name
SKIP 30
LIMIT 10
```

### **DISTINCT** - 去重

```cypher
-- 去除重复值
MATCH (p:Person)
RETURN DISTINCT p.city

-- 在聚合中使用
MATCH (p:Person)-[:VISITED]->(c:City)
RETURN p.name, count(DISTINCT c) AS cities_visited
```

---

## 2. **数据操作关键字**

### **CREATE** - 创建

```cypher
-- 创建节点
CREATE (:Person {name: '张三', age: 30})

-- 创建多个节点
CREATE
  (:Person {name: '李四'}),
  (:Person {name: '王五'})

-- 创建节点和关系
CREATE
  (a:Person {name: '张三'})-[:FRIEND]->(b:Person {name: '李四'})

-- 创建带属性的关系
CREATE
  (a:Person {name: '张三'})-[r:FRIEND {since: date()}]->(b:Person {name: '李四'})
```

### **MERGE** - 合并（存在则更新，不存在则创建）

```cypher
-- 合并节点
MERGE (p:Person {name: '张三'})
RETURN p

-- 合并并设置属性
MERGE (p:Person {name: '张三'})
ON CREATE SET
  p.created = datetime(),
  p.status = 'new'
ON MATCH SET
  p.updated = datetime(),
  p.update_count = coalesce(p.update_count, 0) + 1

-- 合并关系
MERGE (a:Person {name: '张三'})
MERGE (b:Person {name: '李四'})
MERGE (a)-[:FRIEND]->(b)
```

### **SET** - 设置属性

```cypher
-- 设置属性
MATCH (p:Person {name: '张三'})
SET p.age = 31

-- 设置多个属性
MATCH (p:Person {name: '张三'})
SET p.age = 31, p.city = '北京'

-- 设置 Map 属性
MATCH (p:Person {name: '张三'})
SET p += {age: 31, city: '北京'}

-- 设置标签
MATCH (p {name: '张三'})
SET p:Employee:Manager

-- 设置数组属性
MATCH (p:Person {name: '张三'})
SET p.skills = ['Java', 'Python']
```

### **REMOVE** - 移除属性或标签

```cypher
-- 移除属性
MATCH (p:Person {name: '张三'})
REMOVE p.temp_field

-- 移除标签
MATCH (p:Person:Employee {name: '张三'})
REMOVE p:Employee

-- 移除多个标签
MATCH (p:Person:Employee:Manager)
REMOVE p:Employee:Manager
```

### **DELETE** - 删除

```cypher
-- 删除节点（必须先删除关系）
MATCH (p:Person {name: '张三'})
DELETE p

-- 删除关系
MATCH (p:Person {name: '张三'})-[r:FRIEND]->()
DELETE r

-- 删除多个
MATCH (p:Person)
WHERE p.age > 100
DELETE p
```

### **DETACH DELETE** - 分离删除（删除节点及其关系）

```cypher
-- 删除节点及其所有关系
MATCH (p:Person {name: '张三'})
DETACH DELETE p

-- 批量删除
MATCH (p:Person)
WHERE p.status = 'deleted'
DETACH DELETE p
```

---

## 3. **模式匹配关键字**

### **NOT** - 否定

```cypher
-- 不匹配特定模式
MATCH (p:Person)
WHERE NOT (p)-[:FRIEND]->()
RETURN p.name

-- 复杂的否定条件
MATCH (p:Person)
WHERE NOT (p.age < 30 AND p.city = '北京')
RETURN p

-- 否定 EXISTS
MATCH (p:Person)
WHERE NOT EXISTS(p.email)
RETURN p.name
```

### **OR** - 或条件

```cypher
-- 或条件
MATCH (p:Person)
WHERE p.age < 30 OR p.city = '北京'
RETURN p

-- 多个 OR 条件
MATCH (p:Person)
WHERE p.city = '北京' OR p.city = '上海' OR p.city = '广州'
RETURN p
```

### **AND** - 与条件

```cypher
-- 与条件
MATCH (p:Person)
WHERE p.age > 30 AND p.city = '北京'
RETURN p
```

### **XOR** - 异或条件

```cypher
-- 异或条件
MATCH (p:Person)
WHERE p.age < 30 XOR p.city = '北京'
RETURN p
```

### **IN** - 包含

```cypher
-- 属性在列表中
MATCH (p:Person)
WHERE p.city IN ['北京', '上海', '广州']
RETURN p

-- 子查询结果
MATCH (p:Person)
WHERE p.name IN ['张三', '李四']
RETURN p
```

### **STARTS WITH** - 开头匹配

```cypher
-- 字符串开头匹配
MATCH (p:Person)
WHERE p.name STARTS WITH '张'
RETURN p.name
```

### **ENDS WITH** - 结尾匹配

```cypher
-- 字符串结尾匹配
MATCH (p:Person)
WHERE p.email ENDS WITH '@example.com'
RETURN p.name
```

### **CONTAINS** - 包含字符串

```cypher
-- 包含子字符串
MATCH (p:Person)
WHERE p.name CONTAINS '三'
RETURN p.name
```

### **REGEXP** - 正则表达式

```cypher
-- 正则表达式匹配
MATCH (p:Person)
WHERE p.name =~ '张.*'
RETURN p.name

-- 复杂的正则
MATCH (p:Person)
WHERE p.email =~ '[A-Za-z0-9._%+-]+@[A-Za-z0-9.-]+\.[A-Za-z]{2,}'
RETURN p.name
```

---

## 4. **聚合和函数关键字**

### **COUNT** - 计数

```cypher
-- 计数所有
MATCH (p:Person)
RETURN count(p)

-- 计数特定值
MATCH (p:Person)-[:FRIEND]->(f)
RETURN p.name, count(f) AS friend_count

-- DISTINCT 计数
MATCH (p:Person)
RETURN count(DISTINCT p.city)
```

### **SUM** - 求和

```cypher
-- 求和
MATCH (p:Person)
RETURN sum(p.salary)

-- 分组求和
MATCH (p:Person)
RETURN p.city, sum(p.salary) AS total_salary
```

### **AVG** - 平均值

```cypher
-- 平均值
MATCH (p:Person)
RETURN avg(p.age)

-- 分组平均值
MATCH (p:Person)
RETURN p.city, avg(p.age) AS avg_age
```

### **MIN** - 最小值

```cypher
-- 最小值
MATCH (p:Person)
RETURN min(p.age)

-- 分组最小值
MATCH (p:Person)
RETURN p.city, min(p.age) AS min_age
```

### **MAX** - 最大值

```cypher
-- 最大值
MATCH (p:Person)
RETURN max(p.age)

-- 分组最大值
MATCH (p:Person)
RETURN p.city, max(p.age) AS max_age
```

### **COLLECT** - 收集为列表

```cypher
-- 收集为列表
MATCH (p:Person)
RETURN p.city, collect(p.name) AS people

-- 收集 DISTINCT
MATCH (p:Person)-[:FRIEND]->(f)
RETURN p.name, collect(DISTINCT f.name) AS friends
```

### **size()** - 大小

```cypher
-- 列表大小
MATCH (p:Person)
RETURN p.name, size(p.skills) AS skill_count

-- 路径大小
MATCH path = (a:Person)-[:FRIEND*]->(b:Person)
RETURN size(nodes(path)) AS path_length

-- 字符串大小
MATCH (p:Person)
RETURN p.name, size(p.name) AS name_length
```

### **labels()** - 标签列表

```cypher
-- 获取节点标签
MATCH (p {name: '张三'})
RETURN labels(p)

-- 在 WHERE 中使用
MATCH (n)
WHERE 'Employee' IN labels(n)
RETURN n.name
```

### **type()** - 关系类型

```cypher
-- 获取关系类型
MATCH (p:Person)-[r]->()
RETURN type(r), count(*) AS count

-- 筛选关系类型
MATCH (p:Person)-[r]->()
WHERE type(r) = 'FRIEND'
RETURN p.name
```

### **id()** - 节点/关系 ID

```cypher
-- 获取节点 ID
MATCH (p:Person)
RETURN id(p), p.name

-- 获取关系 ID
MATCH (p:Person)-[r:FRIEND]->()
RETURN id(r), type(r)
```

### **properties()** - 属性 Map

```cypher
-- 获取属性 Map
MATCH (p:Person {name: '张三'})
RETURN properties(p)
```

### **nodes()** - 路径中的节点

```cypher
-- 获取路径中的节点
MATCH path = (a:Person)-[:FRIEND*2]->(c:Person)
RETURN [node IN nodes(path) | node.name] AS path_nodes
```

### **relationships()** - 路径中的关系

```cypher
-- 获取路径中的关系
MATCH path = (a:Person)-[:FRIEND*2]->(c:Person)
RETURN [r IN relationships(path) | type(r)] AS relationship_types
```

### **range()** - 范围

```cypher
-- 生成数字范围
RETURN range(1, 10) AS numbers

-- 生成带步长的范围
RETURN range(0, 100, 10) AS decades
```

---

## 5. **子查询和过程关键字**

### **CALL** - 调用过程

```cypher
-- 调用内置过程
CALL db.labels() YIELD label
RETURN label

-- 调用 APOC 过程
CALL apoc.help('apoc')
YIELD name, description
RETURN name, description

-- 调用自定义过程
CALL custom.procedure('参数')
YIELD result
RETURN result
```

### **YIELD** - 产生结果

```cypher
-- 从过程产生结果
CALL db.labels()
YIELD label
RETURN count(label) AS label_count

-- 多个字段
CALL apoc.meta.stats()
YIELD labelCount, relTypeCount, propertyKeyCount
RETURN labelCount, relTypeCount, propertyKeyCount
```

### **UNION** - 并集

```cypher
-- 合并查询结果
MATCH (p:Person)
RETURN p.name AS name
UNION
MATCH (c:Company)
RETURN c.name AS name

-- UNION ALL（包含重复）
MATCH (p:Person {city: '北京'})
RETURN p.name
UNION ALL
MATCH (p:Person {city: '上海'})
RETURN p.name
```

### **UNION ALL** - 并集（包含重复）

```cypher
-- 包含重复的并集
MATCH (p:Employee)
RETURN p.name
UNION ALL
MATCH (m:Manager)
RETURN m.name
```

### **CASE** - 条件表达式

```cypher
-- 简单 CASE
MATCH (p:Person)
RETURN p.name,
  CASE p.age
    WHEN 20 THEN '青年'
    WHEN 30 THEN '壮年'
    ELSE '其他'
  END AS age_group

-- 搜索 CASE
MATCH (p:Person)
RETURN p.name,
  CASE
    WHEN p.age < 30 THEN '青年'
    WHEN p.age < 50 THEN '中年'
    ELSE '老年'
  END AS age_group
```

### **WHEN** - CASE 子句

```cypher
-- 在 CASE 中使用
MATCH (p:Person)
RETURN
  CASE
    WHEN p.salary > 50000 THEN '高收入'
    WHEN p.salary > 30000 THEN '中等收入'
    ELSE '低收入'
  END AS income_level
```

### **THEN** - CASE 结果

```cypher
-- CASE 的结果分支
MATCH (p:Person)
RETURN
  CASE
    WHEN p.city = '北京' THEN '北方'
    WHEN p.city = '上海' THEN '东方'
    WHEN p.city = '广州' THEN '南方'
    ELSE '其他'
  END AS region
```

### **ELSE** - CASE 默认值

```cypher
-- CASE 的默认分支
MATCH (p:Person)
RETURN
  CASE p.department
    WHEN 'IT' THEN '技术部'
    WHEN 'HR' THEN '人力资源部'
    ELSE '其他部门'
  END AS dept_name
```

### **END** - CASE 结束

```cypher
-- CASE 表达式结束
MATCH (p:Person)
RETURN
  p.name,
  CASE
    WHEN p.age < 18 THEN '未成年'
    WHEN p.age < 60 THEN '成年人'
    ELSE '老年人'
  END AS age_category
```

---

## 6. **约束和索引关键字**

### **CREATE CONSTRAINT** - 创建约束

```cypher
-- 唯一约束
CREATE CONSTRAINT unique_person_name FOR (p:Person) REQUIRE p.name IS UNIQUE

-- 节点键约束（企业版）
CREATE CONSTRAINT node_key_person_id FOR (p:Person) REQUIRE (p.id) IS NODE KEY

-- 存在性约束（企业版）
CREATE CONSTRAINT person_name_exists FOR (p:Person) REQUIRE p.name IS NOT NULL
```

### **DROP CONSTRAINT** - 删除约束

```cypher
-- 删除约束
DROP CONSTRAINT unique_person_name
```

### **CREATE INDEX** - 创建索引

```cypher
-- 创建单属性索引
CREATE INDEX person_name_index FOR (p:Person) ON (p.name)

-- 创建复合索引
CREATE INDEX person_city_age_index FOR (p:Person) ON (p.city, p.age)

-- 全文索引
CREATE FULLTEXT INDEX person_fulltext FOR (p:Person) ON EACH [p.name, p.description]
```

### **DROP INDEX** - 删除索引

```cypher
-- 删除索引
DROP INDEX person_name_index
```

---

## 7. **事务控制关键字**

### **BEGIN** - 开始事务

```cypher
-- 开始事务
:begin

-- 在事务中执行操作
CREATE (:Person {name: '张三'})
CREATE (:Person {name: '李四'})

-- 提交或回滚
:commit
-- 或
:rollback
```

### **COMMIT** - 提交事务

```cypher
-- 提交事务
:commit
```

### **ROLLBACK** - 回滚事务

```cypher
-- 回滚事务
:rollback
```

---

## 8. **管理关键字**

### **SHOW** - 显示信息

```cypher
-- 显示所有约束
SHOW CONSTRAINTS

-- 显示所有索引
SHOW INDEXES

-- 显示所有过程
SHOW PROCEDURES

-- 显示所有函数
SHOW FUNCTIONS
```

### **EXPLAIN** - 解释执行计划

```cypher
-- 查看查询计划
EXPLAIN MATCH (p:Person)-[:FRIEND]->(f)
RETURN p.name, count(f)
```

### **PROFILE** - 分析查询性能

```cypher
-- 分析查询性能
PROFILE MATCH (p:Person)-[:FRIEND]->(f)
RETURN p.name, count(f)
```

### **LOAD CSV** - 加载 CSV

```cypher
-- 加载 CSV 文件
LOAD CSV WITH HEADERS FROM 'file:///persons.csv' AS row
CREATE (:Person {
  name: row.name,
  age: toInteger(row.age),
  city: row.city
})

-- 带转换的加载
LOAD CSV WITH HEADERS FROM 'file:///data.csv' AS row
WITH row WHERE row.age IS NOT NULL
CREATE (p:Person)
SET p = row,
    p.age = toInteger(row.age),
    p.salary = toFloat(row.salary)
```

---

## 📊 **所有关键字速查表**

| 类别   | 关键字            | 描述          |
| ------ | ----------------- | ------------- |
| 查询   | MATCH             | 匹配图模式    |
| 查询   | OPTIONAL MATCH    | 可选匹配      |
| 查询   | WHERE             | 条件过滤      |
| 查询   | RETURN            | 返回结果      |
| 查询   | WITH              | 传递结果      |
| 查询   | UNWIND            | 展开列表      |
| 查询   | ORDER BY          | 排序          |
| 查询   | SKIP              | 跳过记录      |
| 查询   | LIMIT             | 限制数量      |
| 查询   | DISTINCT          | 去重          |
| 数据   | CREATE            | 创建          |
| 数据   | MERGE             | 合并          |
| 数据   | SET               | 设置属性      |
| 数据   | REMOVE            | 移除属性/标签 |
| 数据   | DELETE            | 删除          |
| 数据   | DETACH DELETE     | 分离删除      |
| 模式   | NOT               | 否定          |
| 模式   | AND               | 与条件        |
| 模式   | OR                | 或条件        |
| 模式   | XOR               | 异或          |
| 模式   | IN                | 包含          |
| 模式   | STARTS WITH       | 开头匹配      |
| 模式   | ENDS WITH         | 结尾匹配      |
| 模式   | CONTAINS          | 包含          |
| 模式   | REGEXP            | 正则          |
| 聚合   | COUNT             | 计数          |
| 聚合   | SUM               | 求和          |
| 聚合   | AVG               | 平均          |
| 聚合   | MIN               | 最小          |
| 聚合   | MAX               | 最大          |
| 聚合   | COLLECT           | 收集列表      |
| 函数   | size()            | 大小          |
| 函数   | labels()          | 标签列表      |
| 函数   | type()            | 关系类型      |
| 函数   | id()              | ID            |
| 函数   | properties()      | 属性          |
| 函数   | nodes()           | 路径节点      |
| 函数   | relationships()   | 路径关系      |
| 子查询 | CALL              | 调用过程      |
| 子查询 | YIELD             | 产生结果      |
| 子查询 | UNION             | 并集          |
| 子查询 | UNION ALL         | 并集(含重复)  |
| 条件   | CASE              | 条件表达式    |
| 条件   | WHEN              | 条件分支      |
| 条件   | THEN              | 条件结果      |
| 条件   | ELSE              | 默认值        |
| 条件   | END               | 条件结束      |
| 约束   | CREATE CONSTRAINT | 创建约束      |
| 约束   | DROP CONSTRAINT   | 删除约束      |
| 索引   | CREATE INDEX      | 创建索引      |
| 索引   | DROP INDEX        | 删除索引      |
| 事务   | BEGIN             | 开始事务      |
| 事务   | COMMIT            | 提交事务      |
| 事务   | ROLLBACK          | 回滚事务      |
| 管理   | SHOW              | 显示信息      |
| 管理   | EXPLAIN           | 解释计划      |
| 管理   | PROFILE           | 性能分析      |
| 导入   | LOAD CSV          | 加载 CSV      |

---

## 🎯 **最佳实践提示**

1. **性能优化**

   ```cypher
   -- 使用索引字段
   CREATE INDEX FOR (p:Person) ON (p.name)
   MATCH (p:Person) WHERE p.name = '张三'  -- 快速

   -- 避免全表扫描
   MATCH (p:Person) WHERE p.age > 30  -- 慢，如果没有索引
   ```

2. **批量操作**

   ```cypher
   -- 使用 UNWIND 批量处理
   WITH range(1, 1000) AS ids
   UNWIND ids AS id
   CREATE (:Person {id: id, name: 'User' + id})
   ```

3. **避免笛卡尔积**

   ```cypher
   -- 错误：可能产生笛卡尔积
   MATCH (a:Person), (b:Person)  -- 避免这样写

   -- 正确：使用模式匹配
   MATCH (a:Person)-[:FRIEND]->(b:Person)
   ```

4. **内存管理**

   ```cypher
   -- 使用 LIMIT 分批处理
   MATCH (p:Person)
   WITH p LIMIT 1000
   SET p.processed = true
   ```

5. **使用参数**
   ```cypher
   -- 参数化查询
   :param name => '张三'
   MATCH (p:Person {name: $name})
   RETURN p
   ```

这个列表包含了 Neo4j Cypher 查询语言的所有关键字及其用法示例。在实际使用中，根据具体需求选择合适的关键字和模式。
