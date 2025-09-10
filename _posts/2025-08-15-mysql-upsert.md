---
layout: post
title: 数据库中upsert机制
categories:
  - mysql
description: 学一些数据库的知识
keywords: mysql 
mermaid: false
sequence: false
flow: false
mathjax: false
mindmap: false
mindmap2: false
---

尝试**MySQL EVENT 方案**，包含 **建表 + 触发器 + 自动清理（仅删除 1 小时前的数据）**，保证 staging_table 不会无限膨胀，同时保留最近数据用于调试。

---

## ✅ **完整实现步骤**

### **1. 创建目标表（带唯一约束）**

```sql
CREATE TABLE target_table (
    id INT PRIMARY KEY,
    name VARCHAR(50),
    value INT,
    updated_at DATETIME
);
```

---

### **2. 创建中转表 staging\_table**

多一个 `created_at` 字段，用于清理逻辑：

```sql
CREATE TABLE staging_table (
    id INT,
    name VARCHAR(50),
    value INT,
    created_at DATETIME DEFAULT NOW()
);
```

---

### **3. 创建触发器，实现 UPSERT**

```sql
DELIMITER //

CREATE TRIGGER staging_to_target
AFTER INSERT ON staging_table
FOR EACH ROW
BEGIN
    INSERT INTO target_table (id, name, value, updated_at)
    VALUES (NEW.id, NEW.name, NEW.value, NOW())
    ON DUPLICATE KEY UPDATE
        name = VALUES(name),
        value = VALUES(value),
        updated_at = NOW();
END;
//

DELIMITER ;
```

---

### **4. 开启 MySQL EVENT Scheduler**

```sql
SET GLOBAL event_scheduler = ON;
```

（要永久开启，在 `my.cnf` 加 `event_scheduler=ON`）

---

### **5. 创建自动清理事件**

每天执行一次，清理 1 小时前的数据（避免正在处理的数据被删）：

```sql
CREATE EVENT clear_staging_table
ON SCHEDULE EVERY 1 DAY
STARTS CURRENT_TIMESTAMP + INTERVAL 1 DAY
DO
  DELETE FROM staging_table WHERE created_at < NOW() - INTERVAL 1 HOUR;
```

如果你想 **完全清空** staging\_table（不保留任何数据），可以改成：

```sql
CREATE EVENT clear_staging_table
ON SCHEDULE EVERY 1 DAY
STARTS CURRENT_TIMESTAMP + INTERVAL 1 DAY
DO
  TRUNCATE TABLE staging_table;
```

---

### **6. 验证**

* **插入 API 数据**

```sql
INSERT INTO staging_table (id, name, value) VALUES (1, 'Alice', 100);
INSERT INTO staging_table (id, name, value) VALUES (1, 'Alice', 200);
```

* **查看目标表**

```sql
SELECT * FROM target_table;
```

你会看到：

```
id | name  | value | updated_at
1  | Alice | 200   | ...
```

* **查看事件是否存在**

```sql
SHOW EVENTS;
```

---

✅ **安全策略总结：**

* `DELETE WHERE created_at < NOW() - INTERVAL 1 HOUR` → 保留最近 1 小时的中转数据。
* 如果 staging\_table **只用于 UPSERT**，可以直接 `TRUNCATE`（最快）。
* **MySQL EVENT 比 Cron 更好**，因为它运行在数据库内部，不依赖操作系统。

---

注： 尚未验证