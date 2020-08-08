# SQL 日常

## 父级对象和子对象

```SQL
CREATE TABLE parent (
    id VARCHAR(32) NOT NULL,
    name VARCHAR(16) NOT NULL,
    created_at DATE DEFAULT CURRENT() NOT NULL,
    updated_at DATE DEFAULT CURRENT() ON UPDATE CURRENT() NOT NULL,
    deleted_at DATE,
    spec JSON NOT NULL,
    status VARCHAR(16) NOT NULL,
);

CREATE TABLE child (
    id VARCHAR(32) NOT NULL,
    name VARCHAR(16) NOT NULL,
    created_at DATE DEFAULT CURRENT() NOT NULL,
    updated_at DATE DEFAULT CURRENT() ON UPDATE CURRENT() NOT NULL,
    deleted_at DATE,
    spec JSON NOT NULL,
    status VARCHAR(16) NOT NULL,
    parent_id VARCHAR(32) NOT NULL,
);
```

### 获取父子对象

1. 以父对象为基准分页，并获取父对象下所有未删除的子对象；
2. 获取父对象当前所有未删除条数和状态统计；
3. 统计父对象下所有未删除子对象的数量和状态统计；

```SQL
# 获取父对象
SELECT * FROM parent
WHERE
  deleted_at IS NULL
LIMIT 10 OFFSET 0
ORDER BY created_at DESC;

# 统计父对象状态
SELECT COUNT(0) FROM parent
WHERE deleted_at IS NULL;

SELECT status, COUNT(0) AS count
FROM parent
WHERE deleted_at IS NULL
GROUP BY status;

# 获取子对象
SELECT * FROM child
WHERE
    parent_id IN (:parentIDs)
    AND deleted_at IS NULL;

SELECT parent_id, status, COUNT(0)
FROM child
WHERE
    parent_id IN (:parentIDs)
    AN deleted_at IS NULL
GROUP BY parent_id, status;
```
