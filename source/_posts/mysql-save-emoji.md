---
title: mysql 存储emoji 表情
top: false
cover: false
toc: true
mathjax: true
date: 2021-10-13 18:17:44
password:
summary:
tags: Mysql
categories: Mysql
---


### mysql 存储emoji 表情

> 近期排查bug发现，某个客户的订单一直更新不到系统中; 于是发现客户的订单号竟然有表情(🐝).....真的奇葩;
> 后续发现mysql存储emoji表情需要unicode字符集, 所以尝试修改相关字符集, 得以解决。

![mysql_emoji](./mysql_emoji.png)

1. 需要将mysql 连接指定字符集utf8mb4
    `mysql+mysqldb://user:pass@localhost/datab?charset=utf8mb4`
2. 数据库字符集 首先查看字符集设置如下, character_set_system为utf8, 需要将其修改为utf8mb4
    查看命令： `SHOW VARIABLES WHERE Variable_name LIKE 'character\_set\_%' OR Variable_name LIKE 'collation%';`
    ```
    +--------------------------+--------------------+
    | Variable_name            | Value              |
    +--------------------------+--------------------+
    | character_set_client     | utf8mb4            |
    | character_set_connection | utf8mb4            |
    | character_set_database   | utf8mb4            |
    | character_set_filesystem | binary             |
    | character_set_results    | utf8mb4            |
    | character_set_server     | utf8mb4            |
    | character_set_system     | utf8               |
    | collation_connection     | utf8mb4_unicode_ci |
    | collation_database       | utf8mb4_unicode_ci |
    | collation_server         | utf8mb4_unicode_ci |
    +--------------------------+--------------------+
    ```

3. 改库、表、字段的字符集
    ```sql
    ALTER DATABASE database_name CHARACTER SET = utf8mb4 COLLATE utf8mb4_unicode_ci;
    ALTER TABLE table_name CONVERT TO CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci;
    ALTER TABLE table_name CHANGE column_name column_name VARCHAR(191) CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci NOT NULL;
    ```