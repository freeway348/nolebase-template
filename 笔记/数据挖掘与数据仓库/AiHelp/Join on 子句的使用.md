
# Join on 子句的使用

---
`JOIN ON` 子句用于在SQL查询中结合多张表的数据，基于表中的公共字段（通常是主键和外键）进行关联。`JOIN` 是指连接操作，而 `ON` 用于指定连接条件，即两张表中要匹配的列。通过使用 `JOIN ON`，可以从多个表中同时检索数据。

### 主要的 `JOIN` 类型：
1. **INNER JOIN**: 返回两个表中符合连接条件的交集数据。
2. **LEFT JOIN (LEFT OUTER JOIN)**: 返回左表中的所有记录，即使在右表中没有匹配，也会显示左表的行，右表没有匹配的地方为NULL。
3. **RIGHT JOIN (RIGHT OUTER JOIN)**: 返回右表中的所有记录，即使在左表中没有匹配，也会显示右表的行，左表没有匹配的地方为NULL。
4. **FULL JOIN (FULL OUTER JOIN)**: 返回左表和右表中的所有记录。没有匹配的部分会用NULL填充。
5. **CROSS JOIN**: 笛卡尔积，返回两个表中所有可能的组合。

### 基本语法：
```sql
SELECT columns
FROM table1
JOIN table2
ON table1.column = table2.column;
```
- `table1` 和 `table2` 是要连接的表。
- `table1.column` 和 `table2.column` 是进行连接的字段，通常是一张表的主键和另一张表的外键。

### 示例：
假设有两个表：

#### 表1：`students`
| student_id | student_name |
|------------|--------------|
| 1          | Alice        |
| 2          | Bob          |
| 3          | Charlie      |

#### 表2：`grades`
| student_id | course  | grade |
|------------|---------|-------|
| 1          | Math    | A     |
| 1          | English | B     |
| 2          | Math    | B     |
| 3          | English | C     |

### 1. **INNER JOIN 示例**：
获取每个学生及其对应的课程和成绩。
```sql
SELECT students.student_name, grades.course, grades.grade
FROM students
INNER JOIN grades
ON students.student_id = grades.student_id;
```
**输出结果：**

| student_name | course  | grade |
|--------------|---------|-------|
| Alice        | Math    | A     |
| Alice        | English | B     |
| Bob          | Math    | B     |
| Charlie      | English | C     |

**解释：**
- `INNER JOIN` 返回两个表中 `student_id` 匹配的记录。只有学生表中有记录且成绩表中也有记录时，才会显示该学生的相关信息。

### 2. **LEFT JOIN 示例**：
显示所有学生，即使他们没有任何成绩。
```sql
SELECT students.student_name, grades.course, grades.grade
FROM students
LEFT JOIN grades
ON students.student_id = grades.student_id;
```
**输出结果：**

| student_name | course  | grade |
|--------------|---------|-------|
| Alice        | Math    | A     |
| Alice        | English | B     |
| Bob          | Math    | B     |
| Charlie      | English | C     |
| Dave         | NULL    | NULL  |

**解释：**
- `LEFT JOIN` 返回左表 `students` 中的所有记录，不论是否在右表 `grades` 中有匹配的记录。没有成绩的学生 `Dave` 也会显示，课程和成绩列为 `NULL`。

### 3. **RIGHT JOIN 示例**：
显示所有课程及其对应的学生，即使有些课程没有学生选修。
```sql
SELECT students.student_name, grades.course, grades.grade
FROM students
RIGHT JOIN grades
ON students.student_id = grades.student_id;
```
**输出结果：**

| student_name | course  | grade |
|--------------|---------|-------|
| Alice        | Math    | A     |
| Alice        | English | B     |
| Bob          | Math    | B     |
| Charlie      | English | C     |

**解释：**
- `RIGHT JOIN` 返回右表 `grades` 中的所有记录。不论左表 `students` 中是否有匹配的记录，都将显示右表中的课程和成绩。

### 4. **FULL OUTER JOIN 示例**：
显示所有学生和课程，包含没有匹配的记录。
```sql
SELECT students.student_name, grades.course, grades.grade
FROM students
FULL OUTER JOIN grades
ON students.student_id = grades.student_id;
```
**输出结果：**

| student_name | course  | grade |
|--------------|---------|-------|
| Alice        | Math    | A     |
| Alice        | English | B     |
| Bob          | Math    | B     |
| Charlie      | English | C     |
| Dave         | NULL    | NULL  |
| NULL         | History | NULL  |

**解释：**
- `FULL OUTER JOIN` 返回左表和右表中的所有记录。对于没有匹配的记录，将用 `NULL` 填充缺失部分。

### 总结：
- `JOIN ON` 的作用是通过指定连接条件将多张表的数据关联起来，使得能够在查询中一次性获取多个表中的相关数据。
- 常见的 `JOIN` 类型包括 `INNER JOIN`、`LEFT JOIN`、`RIGHT JOIN` 和 `FULL JOIN`，它们分别适用于不同的关联场景。