---
title: MySQL 相关
date: 2026-01-21 19:54:30
tags:
  - MySQL
---

在 MySQL 中有多种方法实现 **"不存在则插入，存在则跳过"** 的功能。以下是详细的实现方式：

## 方法1：使用 `INSERT IGNORE`（最简单）

```sql
-- 创建测试表
CREATE TABLE users (
    id INT PRIMARY KEY,
    name VARCHAR(50),
    email VARCHAR(100)
);

-- 插入数据，如果id已存在则跳过整行
INSERT IGNORE INTO users (id, name, email)
VALUES
    (1, '张三', 'zhangsan@example.com'),
    (2, '李四', 'lisi@example.com'),
    (3, '王五', 'wangwu@example.com');

-- 再次执行相同的插入（不会插入，也不会报错）
INSERT IGNORE INTO users (id, name, email)
VALUES
    (1, '张三', 'zhangsan@example.com'),
    (4, '赵六', 'zhaoliu@example.com');  -- 只有这行会插入
```

**特点**：

- 简单易用
- 如果存在重复，**跳过整行插入**
- 只检查 **主键或唯一索引** 的重复
- 返回的 affected rows = 实际插入的行数

## 方法2：使用 `ON DUPLICATE KEY UPDATE`（更灵活）

```sql
-- 如果id存在则更新某些字段，不存在则插入
INSERT INTO users (id, name, email)
VALUES
    (1, '张三', 'zhangsan@example.com'),
    (5, '钱七', 'qianqi@example.com')
ON DUPLICATE KEY UPDATE
    name = VALUES(name),  -- 如果存在则更新name
    email = VALUES(email);  -- 如果存在则更新email

-- 如果不想更新，可以保持原值
INSERT INTO users (id, name, email)
VALUES (1, '新名字', 'new@example.com')
ON DUPLICATE KEY UPDATE
    id = id;  -- 什么都不更新，实际上就是跳过
```

## 方法3：使用 `REPLACE INTO`（删除后插入）

```sql
-- 如果id存在，先删除再插入（相当于更新整行）
REPLACE INTO users (id, name, email)
VALUES (1, '完全新的数据', 'completely_new@example.com');

-- 批量替换
REPLACE INTO users (id, name, email)
VALUES
    (1, '新张三', 'new_zhangsan@example.com'),
    (6, '孙八', 'sunba@example.com');
```

**注意**：`REPLACE` 会**先删除再插入**，如果表有自增字段，会生成新的自增值。

## 方法4：使用 `NOT EXISTS` 子查询（最通用）

```sql
-- 单条插入
INSERT INTO users (id, name, email)
SELECT 1, '张三', 'zhangsan@example.com'
FROM DUAL
WHERE NOT EXISTS (
    SELECT 1 FROM users WHERE id = 1
);

-- 批量插入，跳过已存在的
INSERT INTO users (id, name, email)
SELECT * FROM (
    SELECT 1 as id, '张三' as name, 'zhangsan@example.com' as email
    UNION ALL
    SELECT 2, '李四', 'lisi@example.com'
    UNION ALL
    SELECT 3, '王五', 'wangwu@example.com'
) AS new_data
WHERE NOT EXISTS (
    SELECT 1 FROM users u WHERE u.id = new_data.id
);
```

## 方法5：使用临时表或CTE

```sql
-- 使用临时表准备数据
CREATE TEMPORARY TABLE temp_users (
    id INT,
    name VARCHAR(50),
    email VARCHAR(100)
);

INSERT INTO temp_users VALUES
(1, '张三', 'zhangsan@example.com'),
(2, '李四', 'lisi@example.com'),
(7, '周九', 'zhoujiu@example.com');

-- 插入不存在的记录
INSERT INTO users (id, name, email)
SELECT t.id, t.name, t.email
FROM temp_users t
LEFT JOIN users u ON t.id = u.id
WHERE u.id IS NULL;

-- 或者使用 NOT EXISTS
INSERT INTO users (id, name, email)
SELECT id, name, email
FROM temp_users t
WHERE NOT EXISTS (
    SELECT 1 FROM users u WHERE u.id = t.id
);
```

## 方法6：存储过程实现

```sql
DELIMITER $$

CREATE PROCEDURE insert_if_not_exists(
    IN p_id INT,
    IN p_name VARCHAR(50),
    IN p_email VARCHAR(100)
)
BEGIN
    DECLARE v_exists INT DEFAULT 0;

    -- 检查是否存在
    SELECT COUNT(*) INTO v_exists
    FROM users
    WHERE id = p_id;

    -- 如果不存在则插入
    IF v_exists = 0 THEN
        INSERT INTO users (id, name, email)
        VALUES (p_id, p_name, p_email);
        SELECT 'Insert successful' as result;
    ELSE
        SELECT 'Record already exists, skipped' as result;
    END IF;
END$$

DELIMITER ;

-- 使用存储过程
CALL insert_if_not_exists(1, '张三', 'zhangsan@example.com');
```

## 方法7：基于唯一约束的多字段判断

```sql
-- 创建有复合唯一约束的表
CREATE TABLE user_contacts (
    id INT AUTO_INCREMENT PRIMARY KEY,
    user_id INT,
    contact_type VARCHAR(20),
    contact_value VARCHAR(100),
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    UNIQUE KEY uk_user_contact (user_id, contact_type)
);

-- 方法1：INSERT IGNORE
INSERT IGNORE INTO user_contacts (user_id, contact_type, contact_value)
VALUES
    (1, 'email', 'user1@example.com'),
    (1, 'phone', '13800138000'),
    (1, 'email', 'new_email@example.com');  -- 这行会被忽略

-- 方法2：ON DUPLICATE KEY UPDATE
INSERT INTO user_contacts (user_id, contact_type, contact_value)
VALUES
    (1, 'email', 'updated_email@example.com'),
    (2, 'phone', '13900139000')
ON DUPLICATE KEY UPDATE
    contact_value = VALUES(contact_value);
```

## 方法8：使用 `INSERT ... SELECT ... WHERE NOT EXISTS` 的高级用法

```sql
-- 从其他表导入数据，跳过已存在的
INSERT INTO users (id, name, email)
SELECT o.customer_id, o.customer_name, o.customer_email
FROM orders o
WHERE NOT EXISTS (
    SELECT 1 FROM users u WHERE u.id = o.customer_id
)
GROUP BY o.customer_id;  -- 去重
```

## 综合比较和选择建议

| 方法                      | 优点                 | 缺点                         | 适用场景           |
| ------------------------- | -------------------- | ---------------------------- | ------------------ |
| `INSERT IGNORE`           | 简单，高效           | 跳过整行，无法部分更新       | 简单的不重复插入   |
| `ON DUPLICATE KEY UPDATE` | 灵活，可更新部分字段 | 语法稍复杂                   | 需要更新已存在记录 |
| `REPLACE INTO`            | 相当于删除后插入     | 删除触发器会触发，自增ID会变 | 需要完全替换记录   |
| `NOT EXISTS` 子查询       | 最灵活，逻辑清晰     | 性能可能较差                 | 复杂条件判断       |
| 存储过程                  | 可封装复杂逻辑       | 需要创建和维护过程           | 频繁调用的业务逻辑 |

## 性能优化建议

### 1. 对于批量插入

```sql
-- 使用 INSERT IGNORE + 批量VALUES
INSERT IGNORE INTO users (id, name, email)
VALUES
    (1, 'A', 'a@example.com'),
    (2, 'B', 'b@example.com'),
    -- ... 几百或几千行
    (1000, 'Z', 'z@example.com');
```

### 2. 大数据量导入

```sql
-- 分批处理
SET autocommit = 0;
SET unique_checks = 0;
SET foreign_key_checks = 0;

-- 每批1000条
INSERT IGNORE INTO target_table
SELECT * FROM source_table
LIMIT 100000, 1000;

SET unique_checks = 1;
SET foreign_key_checks = 1;
COMMIT;
```

### 3. 创建合适索引

```sql
-- 确保有主键或唯一索引
ALTER TABLE users ADD PRIMARY KEY (id);
-- 或
CREATE UNIQUE INDEX uk_users_id ON users(id);

-- 多字段唯一约束
ALTER TABLE user_contacts
ADD CONSTRAINT uk_user_type UNIQUE (user_id, contact_type);
```

## 实际应用示例

### 场景1：用户注册防重复

```sql
-- 用户表有唯一用户名约束
CREATE TABLE users (
    id INT AUTO_INCREMENT PRIMARY KEY,
    username VARCHAR(50) UNIQUE,
    email VARCHAR(100) UNIQUE,
    password_hash VARCHAR(255)
);

-- 注册时检查
INSERT IGNORE INTO users (username, email, password_hash)
VALUES ('john_doe', 'john@example.com', 'hashed_password');

-- 或者用 ON DUPLICATE KEY UPDATE 提供更友好的反馈
INSERT INTO users (username, email, password_hash)
VALUES ('john_doe', 'john@example.com', 'hashed_password')
ON DUPLICATE KEY UPDATE
    username = IF(username = VALUES(username), username, NULL);
```

### 场景2：数据同步

```sql
-- 从API获取数据同步到数据库
INSERT IGNORE INTO products (external_id, name, price, updated_at)
VALUES
    ('EXT001', 'Product A', 99.99, NOW()),
    ('EXT002', 'Product B', 149.50, NOW()),
    ('EXT003', 'Product C', 79.99, NOW())
ON DUPLICATE KEY UPDATE
    name = VALUES(name),
    price = VALUES(price),
    updated_at = NOW();
```

**最简单的推荐**：如果只需要跳过重复记录，使用 `INSERT IGNORE`；如果需要更新部分字段，使用 `ON DUPLICATE KEY UPDATE`。
