
# Group by 子句的使用

---

在SQL中，`GROUP BY` 子句用于将查询结果按照一个或多个列进行分组。它通常与聚合函数（如 `COUNT()`、`SUM()`、`AVG()`、`MAX()`、`MIN()` 等）一起使用，以便对每个组进行汇总计算。

### 基本语法：
```sql
SELECT column1, column2, AGGREGATE_FUNCTION(column3)
FROM table_name
WHERE condition
GROUP BY column1, column2;
```

- `column1, column2`: 要分组的列。
- `AGGREGATE_FUNCTION(column3)`: 对每个分组进行聚合计算的列，例如计数、求和等。

### `GROUP BY` 的主要用途：
- 将数据分成多个组，每个组包含相同值的记录。
- 对每个组应用聚合函数，进行汇总计算。

### 示例：

假设有一个名为 `sales` 的表，结构如下：

| sale_id | product | quantity | price | branch |
|---------|---------|----------|-------|--------|
| 1       | A       | 10       | 100   | North  |
| 2       | B       | 5        | 50    | East   |
| 3       | A       | 7        | 70    | South  |
| 4       | A       | 3        | 30    | North  |
| 5       | B       | 8        | 80    | East   |

#### 1. 统计每个产品的销售总量：
```sql
SELECT product, SUM(quantity) AS total_quantity
FROM sales
GROUP BY product;
```
**输出结果：**

| product | total_quantity |
|---------|----------------|
| A       | 20             |
| B       | 13             |

**解释：**
- 这里的 `GROUP BY product` 将 `sales` 表中的记录按 `product` 列分组。
- 每个产品的销售数量总和通过 `SUM(quantity)` 进行计算。

#### 2. 统计每个分店的销售总额：
```sql
SELECT branch, SUM(price * quantity) AS total_sales
FROM sales
GROUP BY branch;
```
**输出结果：**

| branch | total_sales |
|--------|-------------|
| North  | 1300        |
| East   | 690         |
| South  | 490         |

**解释：**
- `GROUP BY branch` 将数据按 `branch` 列分组。
- `SUM(price * quantity)` 计算每个分店的销售总额。

#### 3. 结合 `GROUP BY` 和 `HAVING` 子句：
`HAVING` 子句通常用于过滤聚合结果，类似于 `WHERE`，但 `WHERE` 用于过滤行，而 `HAVING` 用于过滤分组。

例如，我们可以找出销售总额大于1000的分店：
```sql
SELECT branch, SUM(price * quantity) AS total_sales
FROM sales
GROUP BY branch
HAVING total_sales > 1000;
```

**输出结果：**

| branch | total_sales |
|--------|-------------|
| North  | 1300        |

**解释：**
- `GROUP BY branch` 分组后，`HAVING` 子句过滤出销售总额大于1000的分店。

### 注意事项：
1. 在 `GROUP BY` 查询中，**所有未参与聚合函数的列**必须出现在 `GROUP BY` 子句中。
2. `GROUP BY` 子句中的列可以是多列组合，用于实现更复杂的分组。

#### 例如：按产品和分店分组统计销售总额：
```sql
SELECT product, branch, SUM(price * quantity) AS total_sales
FROM sales
GROUP BY product, branch;
```

**输出结果：**

| product | branch | total_sales |
|:---------:|:--------:|:-------------:|
| A       | North  | 1300        |
| A       | South  | 490         |
| B       | East   | 690         |

通过 `GROUP BY`，可以对数据进行汇总、统计和分析，从而为业务决策提供支持。