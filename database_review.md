> 这是**2025年春季学期数据库系统**课程的期末复习笔记，结合陈世敏老师梳理的考试范围，对slides上的内容进行了针对性整理，同时突出了练习部分。笔记内容仅代表该学期考试范围，请以实际课程内容为准。如有勘误，希望不吝批评指正；如有存在疑惑的内容，也欢迎跟我讨论，我的联系方式如下:
>
> [yaoyongzhou22@mails.ucas.ac.cn](mailto: yaoyongzhou22@mails.ucas.ac.cn)

## 一些需要注意的地方

1. 由于typora的markdown语法原因，代码块中许多地方会自动合并，比如

   - `'!' + '='`会变为`!=`

   - `'>' + '='`会变为`>=`

   - `'<' + '='`会变为`<=`

​	在SQL中，请使用分开书写的形式

2. 待补充



## 第二章 概述(需要了解概念)

###  数据库的概念

####  数据库(Database)

- 内涵：
  1. 存储和管理一组数据，通常按照某种数据模型来组织
  2. 描述了实体(Entity)和他们之间的关系(Relationship)

- 外延：
  1. 文件(File)
     - 文件由字节组成，不关心其内容，数据库关心。
     - 数据库可能在文件中存储数据。
  2. 数据集(Data Set)
     - 数据集的数据可能是混乱的，未经整理和清洗的。
     - 数据库中的数据都是以某种方式组织起来的。
     - 数据库也可以被认为是一种数据集
  3. 数据结构(Data Structure)
     - 数据结构不关心数据内容

#### 数据库系统(DBMS, Database Management System)

- 内涵：
  1. 管理数据库的系统
  2. 管理包括：存储、修改、查询、运算

- 外延：
  1. 文件系统
     - 管理文件，存储/修改/读取的对象是文件。
  2. 运算系统
     - 为了某种特定的运算而设计的系统，方便编程和运行。
  3. 网站
     - 提供网页服务，可以访问数据库系统。

#### 关系模型

- Table/Relation(表)

  1. 列：一个属性，有明确的数据类型

     - 必须是原子类型，不能再进一步分割，没有内部结构
     - 例如：数值类型，字符串类型，枚举类型

  2. 行：一个记录(tuple, record)

     - 表就是记录的集合
     - 记录之间是无序的

  3. 数学定义

     - $$
       \{<t_1, \dots, t_k>|t_1\in D_1,\dots,t_k\in D_k\}
       $$

     - D_j是第j列的类型对应的所有可能取值的集合。

- 原子类型

  1. 无内部嵌套结构：int, double, char, string, Date, Enum
  2. 可以进一步分割的不是原子类型: struct, class, array, list/set/map

- Scheme & Instance

  1. Schema: 模式/类型，e.g. Student(ID integer, GPA float)
  2. Instance: 实例/具体取值
  3. 意义在于只需要定义一次Schema，可以对应多个instance，增删改的过程中也只会改变表的内容而不会改变表的类型。

- 数据独立性(Data Independence)

  1. DBMS内部的数据组织和存储的改变，不影响上层的使用
  2. 数据组织和存储的改变对于应用是隔离的。
  3. 使用抽象层次实现
     - 底层是物理模式(Physical Schema)：确定逻辑模式是如何存储和组织的
     - 往上是逻辑模式(Logical Schema)：指数据表的类型定义
     - 顶层是不同的外部模式(External Schema)：每个用户只能看到逻辑模式的一个部分（每个用户只能看到自己的信息）
  4. 物理模式和逻辑模式之间存在**物理数据独立性**：逻辑模式可以屏蔽物理模式的变化，只要逻辑模式不变，即使物理存储发生变化也不会影响应用
  5. 逻辑模式与外部模式之间存在**逻辑数据独立性**：当逻辑模式发生变化时，仍可以定义和过去相同的外部模式，使得上层的应用不受影响。

- 其他模型（了解）

  1. ER模型
  2. 早于关系模型：层次模型、网状模型
  3. 晚于关系模型：面向对象模型，关系对象模型，XML，图数据模型，树状数据模型

  



### 计算机硬件的发展

#### 80年代计算机系统组成

- CPU处理器
- DRAM主存
- HDD硬盘

#### 摩尔定律

集成电路晶体管数量大约每两年会翻一番

#### CPU体系结构的发展

- 提高串行程序效率

  - 提高主频

  - 流水线

  - 超标量

  - 乱序执行

  - 向量指令

  - 多级高速缓存

#### 存储层次结构

- 外存(4KB page)—DRAM内存—L3缓存—L2缓存—L1缓存(64B)—寄存器(8B)
- 内存：容量增速符合摩尔定律，访问速度比执行指令慢100倍（访存墙）
- 硬盘
  - 组成：磁道+磁头+机械臂
  - 访问：给定一个磁道(track)和扇区(sector)
    1. Seek：移动磁头到对应的柱面上(cylinder)
    2. Rotational delay：等待sector旋转到磁头之下
    3. Transfer：读写sector的内容
    4. 总访问时间: T_{seek} + T_{rotate} + T_{transfer}
  - 随机访问：读取数据很少，主要是T_{seek} 和T_{rotate}
  - 顺序访问：读取大量数据，T_{transfer}起主要作用
  - 优化目标：尽量使用顺序访问
- 闪存(Flash)与固态硬盘(SSD)
  - 不支持直接修改
  - 擦除：次数有限，对大块数据操作
  - 写：操作一页，只能单向修改
  - SSF中有FTL(Flash Translation Layer)：将逻辑地址映射为内部的物理地址
  - 随机写操作性能差
  - 随机读性能很好



### 数据库系统的发展

#### 事务处理(Transaction Processing)

大量并发用户，少量随机读写，银行业务常用

#### 数据仓库(Data Warehouse)

分为前后端



## 第三章：ER模型（明确不会考）



## 第四章：关系模型与SQL

### SQL初步(明确会考，注意练习例题)

SQL语句分类

1. 数据定义(DDL)：表的增删改，视图的操作
   - Create Database：创建一个数据库。
     - 一个RDBMS可以管理多个数据库，每个数据库可以包含多个表
     - 语法:` create database db_name`
   - Create Table：创建一个表。
     - 语法: ` create table table_name(col_name2 type, col_name2 type,...);`
   - 名字
     - 最长128字符
     - 不区分大小写，如需区分，使用**双引号**将名字括起来
   - 类型(只列举标准类型，其他见ppt)
     - 数值类型：
       - 整数：smallint 16位； integer 32位
       - 浮点数：real 32位浮点；double/float 64位浮点
       - 精确小数：decimal(size,d) / numeric(size,d)，size位的十进制数，小数点右侧有d位小数 ，左侧有(size - d)位数字
     - 字符串类型：
       - char：定长
       - varchar(n)：变长，最长为n
     - 日期和时间：
       - date：日期
       - time：时间
       - timestamp：时间戳，包含日期和时间
     - 其他
       - BLOB: Binary Large Object
       - CLOB: Character Large Object
   - 默认值
     - NULL（也可以定义非NULL默认值，在create table时添加`default value`）
     - 可能带来缺失值的情况：
       - 往表中插入新纪录时，没有给出某一列的具体值
       - 修改表的定义，增加一个新的列时

> 练习：创建**TakeCourse**表
>
> | Course ID | Student ID | Year |    Semester     | Grade  |
> | :-------: | :--------: | :--: | :-------------: | :----: |
> |   整数    |    整数    |  年  | 枚举/变长字符串 | 浮点数 |
>
> ```sql
> create table TakeCourse(
>  CourseID integer,
>  StudentID integer,
>  Year year,
>  Semester enum, 
>  -- Semester varchar(n),
>  Grade float,
>  primary key(CourseID, StudentID),
>  foreign key(CourseID) references Course(ID),
>  foreign key(StudentID) references Student(ID)
> );
> ```

- 删除表：Drop Table， 语法:` drop table db_name`
- 修改表：Alter Table（代价可能会很高）
  - 语法:` alter table table_name drop col_name`
  - 语法:` alter table table_name add col_name type default value`

2. 数据操纵(DML)：记录的增删改查

   - Insert
     - 语法:` insert into table_name values;`
     - 不同的value用逗号隔开，按照create table的schema排序
     - 只插入特定的列`insert into table_name(col1, col2) values(value1, value2);` 

   - Delete
     - 语法:` delete from table_name where conditions;`
   - Update
     - 语法:` update table_name set col=value where conditions;`

3. 数据控制(DCL)：访问控制

### 选择、投影、连接(明确会考，注意练习例题)

#### 关系代数（Relational Algebra）

- 代数：变量符号代替具体的数，运算符连接表达运算
- 关系代数：在关系上定义的代数，变量=关系变量； 数=关系实例、即数据表；运算/函数=关系运算
- 传统关系代数：将关系看做**集合**，无重复记录
- 扩展关系代数：将关系看做**多集**，允许重复元素

#### 传统关系代数

1. 关系运算
   - 集合操作：并、交、差
   - 选择行或列：选择、投影
   - 两关系元组之间的操作：笛卡尔积、连接
   - 其他：重命名、除

2. 选择(Selection) $$\sigma$$

   - $$
     \sigma_{\text{条件}}(\text{表名})
     $$

   - 从一个表中提取一些行（过滤记录）

   - 对应sql语句

3. 投影(Projection)$$\pi$$

   - $$
     \pi_{\text{列名}，\dots, \text{列名}}(\text{表名})
     $$

   - 从一个表中提取一些列，对应select语句（提取部分列）

   - 如果将每一列看作一维，关系代数的投影可以类比为降维

   - 在数据库系统中，select默认不会去重，需要手动添加` distinct` 

   - 默认不去重是因为去重操作本身有开销，而且重复数据可能也有意义

4. 连接(Join) $$\bowtie$$

- 等值连接

  - $$R.a = S.b $$为条件的连接，寻找两个表中相互匹配的记录

  - $$
    R \bowtie_{R.a = S.b} S
    $$

  - 其中$$R.a, S.b$$被称作` join key` 

>根据下面的关系模式，解决若干练习 (注意分号和逗号的使用规范)
>
>```sql
>create table Sailors(
>	sid integer primary key,
>	sname varchar(20) unique, 
>    rating integer, 
>    age real
>);
>
>create table Boats(
>    bid integer primary key,
>	bname varchar(20), 
>    color varchar(10)
>);
>create table Reserves(
>    sid integer, 
>    bid integer, 
>    day date
>	primary key (sid, bid, day),
>	foreign key (sid) references Sailors(sid),
>	foreign key (bid) references Boats(bid)
>);
>```
>
>---
>
>- 例1：找出评价高于8的所有水手
>
>  - SQL
>
>  - ``` sql
>    select *
>    from Sailors
>    where rating > 8;
>    ```
>
>  - 关系代数
>
>  - $$
>    \sigma_{rating > 8}(Sailors)
>    $$
>
> ---
>
>- 例2：找出所有水手的名字和评价
>
>  - SQL
>
>  - ```sql
>    select sname, rating
>    from Sailors;
>    ```
>
>  - 关系代数
>
>  - $$
>    \pi_{sname, rating}(Sailors)
>    $$
>
> ---
>
>- 例3：找出评价高于8的所有水手的名字
>
>  - SQL
>
>  - ```sql
>    select sname 
>    from Sailors
>    where rating > 8;
>    ```
>
>  - 关系代数
>
>  - $$
>    \pi_{sname}(\sigma_{rating > 8}(Sailors))
>    $$
>
> ---
>
>- 例4：预定了103号船水手的名字
>
>  - SQL
>
>  - ```sql
>    select sname 
>    from Sailors, Boats, Reserves
>    where Reserves.sid = Sailors.sid and Reserves.bid = 103;
>    ```
>
>  - 关系代数
>
>  - $$
>    \pi_{sname}(Sailors\bowtie_{Sailors.sid = Reserves.sid} \sigma_{bid = 103}(Reserves) )
>    $$
>
>---
>
>- 例5：找出预定了红色船的水手的名字
>
>  - SQL
>
>  - ```sql
>    select sname 
>    from Sailors, Boats, Reserves
>    where Reserves.sid = Sailors.sid
>    and   Reserves.bid = Boats.bid 
>    and   Boats.color = 'Red';
>    ```
>
>  - 关系代数 (实际上对于Natural Join，可以省略$$\bowtie$$下标的条件)
>
>  - $$
>    \pi_{sname}(Sailors \bowtie_{Reserves.sid = Sailors.sid} Reserves \bowtie_{Reserves.bid = Boats.bid} \sigma_{color = 'Red'}(Boats))
>    $$
>
> ---
>
>- 例6：找出Bob在今天预定的船的颜色
>
>  - SQL
>
>  - ```sql
>    select color
>    from Sailors, Boats, Reserves
>    where Reserves.sid = Sailors.sid
>    and   Reserves.bid = Boats.bid 
>    and   Sailors.sname = 'Bob'
>    and   Reserves.date = CURRENT_DATE;
>    ```
>
>  - 关系代数 (这里的写法简洁但并不好，先连接再选择开销会大一些)
>
>  - $$
>    \pi_{color}(\sigma_{sname = 'Bob'\;and\; date = CURRENT\_DATE}(Sailors \bowtie Reserves \bowtie (Boats))
>    $$
>
> ---
>
>- 例7：找出至少预定了两条船的水手的名字
>
>  - SQL
>
>  - ```sql
>    select sname 
>    from Sailors, Reserves as R1, Reserves as R2
>    where R1.sid = Sailors.sid
>      and R2.sid = Sailors.sid
>      and R1.bid <> R2.bid;
>    ```
>
>  - 关系代数
>
>  - $$
>    \pi_{sname}(\sigma_{R1.bid\;!=\;R2.bid}(Sailors \bowtie_{Sailors.sid=R1.sid} \rho_{R1(sid,bid,day)}(Reserves)\bowtie_{Sailors.sid=R2.sid} \rho_{R2(sid,bid,day)}(Reserves)))
>    $$

5.   组合使用 
   - $$\pi_{Name, GPA}(\sigma_{Major = 'Math'}(Student))$$ 就是先在Student表上筛选专业是数学的记录，再提取这些记录的姓名和GPA两列。

6. 条件
   - where条件
     - AND, OR, NOT, ()：注意优先级是 () --> NOT --> AND --> OR 
     - 二值比较： <, >, <=, >=, **相等是=**, **不等是!=或<>**
   - between 90 and 100， 即[90, 100]
   - in (value1, value2, ... )
   - 字符串的模式匹配
     - 匹配： string **like** pattern
     - 不匹配：string **not like** pattern
     - pattern
       - 普通字符：直接匹配该字符
       - %：匹配0或任意多个字符
       - _：匹配任意一个字符
       - [字符列表] ：出现其中一个字符
   - 判断取值是否为NULL
     - where col_name **is/is not null**
     - NULL参加时认为代表取值位置，如果是算术运算，输出为NULL；如果是比较运算，输出为UNKNOWN



#### 传统关系代数(续)

##### 集合操作

- 交(Union)：`Union`
- 并(Intersection): `Intersect`
- 补(Difference)：`Except`
- 关系代数进行集合运算时，参与运算的关系的Schema必须相同(列信息保持一致)
- SQL中的集合操作会默认去重

##### 重命名(SQL **AS**)

- $$\rho_{S(A_1,A_2,\dots,A_n)(R)}$$ : 把关系R重命名为关系S，各列的名字分别是$$A_1,\dots,A_n$$
- 主要是在select和from字句中

##### 笛卡尔积(Cartesian Product)

- 叉积
- SQL为

```sql
select *
from R,S;
```

- 笛卡尔积vs. Join
  - 等值连接的两种表示方法
  - $$R \bowtie_{R.a=S.b} S \; = \; \sigma_{R.a = S.b} (R \times S)$$

##### SQL-92 Join

- 之前说过的是SQL-89中的Join，对应内部连接(Inner Join)
- 下面介绍外部连接(Outer Join)
  - 核心思路在于，外部连接想把没有匹配的记录也一起输出
  - Left outer join: 输出**左表**中未匹配的记录
  - Right outer join: 输出**右表**中未匹配的记录
  - Full outer join: 输出**左表和右表**中未匹配的记录
- 语法

```sql
-- inner join
select R.R_name, S.S_name
from R inner join S on R.a = S.b;

-- left outer join
select R.R_name, S.S_name
from R left outer join S on R.a = S.b;

-- right outer join
select R.R_name, S.S_name
from R right outer join S on R.a = S.b;

-- full outer join
select R.R_name, S.S_name
from R full outer join S on R.a = S.b;


```

- 关系代数
  - **⟕** left outer join
  - **⟖** right outer join
  - **⟗** full outer join
- 自然连接(Natural Join)
  - Join Key是两个关系中相同名字的列，无需显式地给出Join Key
- $$\theta$$连接
  - 非等值连接
  - $$\theta$$代表非等值的比较操作

##### 除法(Division)

- $$A/B$$
- $$if \; A(x,y), \; B(y)\;, then \; A/B=\{x|\forall y\in B, (x,y)\in A\}$$
- 在SQL中无原生支持

> (续)
>
> 练习8：找出年龄超过20但是没有预定任何船的水手的名字
>
> ```sql
> (select sname
> from Sailors
> where age > 20)
> except
> (select sname
> from Sailors
> where Reserves.sid = Sailors.sid);
> ```
>
> $$
> \pi_{sname}(\sigma_{age>20}(Sailors)) - \pi_{sname}(Sailors \bowtie Reserves)
> $$
>
> ---
>
> 练习9：找出所有红色船的名字
>
> ```sql
> select bname 
> from Boats
> where color = 'red';
> ```
>
> $$
> \pi_{bname}(\sigma_{color='red'}(Boats))
> $$
>
> ---
>
> 练习10：名字为Interlake的船都在哪些天被预定
>
> ```sql
> select day
> from Reserves,Boats
> where bname = 'Interlake'
> and Reserves.bid = Boats.bid;
> ```
>
> $$
> \pi_{day}(\sigma_{bname='Interlake'}(Boats) \bowtie Reserves)
> $$
>
> ---
>
> 练习11：找出预定了红色船或者绿色船的水手名字
>
> ```sql
> -- or
> select sname 
> from Sailors, Boats, Reserves
> where Reserves.sid = Sailors.sid
> and Reserves.bid = Boats.bid
> and (color = 'red' or color = 'green');
> 
> -- union
> (select sname 
> from Sailors, Boats, Reserves
> where Reserves.sid = Sailors.sid
> and Reserves.bid = Boats.bid
> and color = 'red')
> union
> (select sname 
> from Sailors, Boats, Reserves
> where Reserves.sid = Sailors.sid
> and Reserves.bid = Boats.bid
> and color = 'green');
> ```
>
> $$
> \pi_{sname}(\sigma_{color='red'\; or \; color='green'}(Boats) \bowtie Sailors \bowtie Reserves)
> $$
>
> ---
>
> 练习12：找出预定了红色船和绿色船的水手的名字
>
> ```sql
> (select sname 
> from Sailors, Boats, Reserves
> where Reserves.sid = Sailors.sid
> and Reserves.bid = Boats.bid
> and color = 'red')
> intersect
> (select sname 
> from Sailors, Boats, Reserves
> where Reserves.sid = Sailors.sid
> and Reserves.bid = Boats.bid
> and color = 'green');
> ```
>
> 



#### 扩展关系代数与SQL

- 元素是多集或者包，允许重复的元素
- 考虑一下之前关系运算的变化
  - 选择，投影，连接，笛卡尔积，重命名 ：没有变化，允许有多个重复的值
  - 并交差：有不同，集合本身是去重的，但对于包的集合运算，不应该去重
  - 假设对于一个元组$t,R$中有$n_R$个$$t,$$ $S$中有$n_S$个元组$t$，各种运算下$t$的个数为：
    - $$R\cup S =n_R + n_S$$
    - $$R\cap S = min(n_R, n_S)$$
    - $$R- S = max(0,n_R-n_S)$$
  - 集合的运算规律
    - 交换律仍然满足
    - 但对差运算的分配律不再满足

> 练习：包的并交差
>
> $$R=\{a,b,c,c,d,d\}, S=\{a,a,c,d\}$$
>
> - $$R\cup S =\{a,a,a,b,c,c,c,d,d,d\}$$
> - $$R\cap S =\{a,c,d\}$$
> - $$R- S =\{b,c,d\}$$

- 新的运算
  - 去重$\delta$
    - 把一个包变成一个集合
    - 对应 SQL **Distinct**
  - 聚合(Aggregation)：求统计信息
    - COUNT: count(*) 返回记录数
    - AVG: avg(col) 返回列的平均值
    - MAX: max(col) 返回某列的最大值
    - MIN: min(col) 返回某列的最小值
    - SUM: sum(col) 返回某列之和
    - $agg (\delta(\pi_{GPA}(Students)))$
  - 分组运算$\gamma$ 
    - SQL **Group by**
    - $\gamma_{Major, COUNT(ID) \rightarrow Number}(Students)$ 统计各专业的学生人数
    - Where + Group by：$\gamma_{Major, COUNT(ID) \rightarrow Number}(\sigma_{Year \geq 2013 \; and \; Year \leq 2014}Students)$ 统计各专业2013-2014年入学的学生人数
    - Group by之后只可能有(1)用于分组的组别列和(2)聚集的统计结果，其他的列都是违法的
    - 去重可以看做是分组的一个特例，即对所有列的组合进行分组，没有聚集
    - 在分组之前进行选择，即上面所说的where子句
    - 在分组之后进行选择，采用having子句
  - 排序$\tau$
    - SQL **Order by**
    - desc从大到小排序，asc从小到大排序



### 嵌套SQL

- 概念：一个查询select中包含另一个select，内层被称为子查询，外层称为主查询。

> 找出预定了103号船水手的名字
>
> ```sql
> select S.sname 
> from Sailors as S
> where S.sid in (
>     select R.sid
>     from Reserves as R
>     where R.bid = 103
> );
> ```
>
> ---
>
> 找出没有预定103号船水手的名字
>
> ```sql
> select S.sname 
> from Sailors as S
> where S.sid not in (
>     select R.sid
>     from Reserves as R
>     where R.bid = 103
> );
> ```

- 涵义：相当于嵌套循环，主查询在外层，子查询在内层。对于主查询的每条记录，执行完整的子查询。

-  分类

  - 静态子查询：子查询中没有引用外层查询中的表和列，可以先计算一次静态子查询结果，然后反复使用

  - 相关嵌套查询：子查询中引用了外层查询中的表或列

    - > 找出预定了103号船水手的名字
      >
      > ```sql
      > select S.sname 
      > from Sailors as S
      > where exists (
      >     select *
      >     from Reserves as R
      >     where R.bid = 103 and R.sid = S.sid
      >     -- 这里子查询引用的sid是主查询的列
      > );
      > ```

- 子查询的比较操作

  - `in`：属于

  - `not in`：不属于

  - `exists`：存在

  - `not exists`：不存在

  - `unique`：无重复元素

  - `not unique`：有重复元素

  - 前两个使用方法为`attr op(子查询)` 后面的使用方法为`op(子查询)`

  - `Any`数值比较，与任意一个子查询结果相比成立就满足条件

    - >练习13：找出评分高于**某个**名字包含Tom的水手的水手名字
      >
      >```sql
      >select S.sname 
      >from Sailors as S
      >where S.rating > any(
      >    select S2.rating
      >    from Sailors as S2
      >    where S2.sname like %Tom%
      >);
      >```

  - `All`数值比较，与所有子查询结果相比成立才满足条件

    - > 练习14：找出评分高于**所有**名字包含Tom的水手的水手名字
      >
      > ```sql
      > select S.sname 
      > from Sailors as S
      > where S.rating > any(
      >     select S2.rating
      >     from Sailors as S2
      >     where S2.sname like %Tom%
      > );
      > ```
      >
      > ---
      >
      > 练习15：找出评分最高的水手名字
      >
      > ```sql
      > select S.sname 
      > from Sailors as S
      > where S.rating >= any(
      >     select S2.rating
      >     from Sailors as S2
      > );
      > 
      > select S.sname 
      > from Sailors as S
      > where S.rating = (
      >     -- 子查询使用聚集时，返回值为标量，不用加any/all
      >     select max(S2.rating)
      >     from Sailors as S2
      > );
      > ```
      >
      > ---
      >
      > 练习16：找出预定了所有船只的水手的名字
      >
      > ```sql
      > select sname
      > from Sailors
      > where not exists(
      >     -- 所有船
      > 	(select Boats.bid from Boats) 
      >     except
      >     -- sid预定的所有船
      >     (select Reserves.bid from Reserves
      >      where Reserves.sid = Sailors.sid)
      > );
      > -- 判断差集结果是否都为空
      > ```
      >
      > $$
      > \pi_{sname}(\pi_{sid,sname,bid}(Sailors \bowtie Reserves \bowtie Boats)/\pi_{bid}(Boats))
      > $$
      >
      > ---
      >
      > 练习17：计算评价高于8的水手的平均年龄
      >
      > ```sql
      > select avg(age)
      > from Sailors
      > where rating > 8;
      > ```
      >
      > ---
      >
      > 练习18：计算最年长水手的年龄
      >
      > ```sql
      > select max(age)
      > from Sailors;
      > ```
      >
      > ---
      >
      > 练习19：计算最年长水手的名字和年龄
      > ```sql
      > select S.sname,S.age
      > from Sailors as S
      > where S.age = (select max(S2.age) from Sailors as S2);
      > ```
      >
      > ---
      >
      > 练习20：找出比评分为10的最年长的水手年龄还要大的水手的名字
      >
      > ```sql
      > select S.sname
      > from Sailors as S
      > where S.age > (
      > 	select max(S2.age)
      > 	from Sailors as S2
      > 	where S2.rating = 10
      > );
      > ```
      >
      > ---
      >
      > 练习21：找出包含至少两个水手的评价等级
      >
      > ```sql
      > select rating
      > from Sailors
      > group by rating
      > having count(*) > 2;
      > 
      > -- 也可以使用朴素的匹配，但数量多了会很麻烦
      > select S1.rating
      > from Sailors as S1, Sailors as S2
      > where S1.sid != S2.sid
      > and S1.rating = S2.rating;
      > ```
      >
      > ---
      >
      > 练习22：对于至少包含两个水手的评价等级，找出至少18岁的水手的平均年龄
      >
      > ```sql
      > select avg(S1.age)
      > from Sailors as S1
      > where S1.age >= 18 
      > and S.rating in(select S2.rating
      >                from Sailors as S2
      >                group by S2.rating
      >                having count(*) >= 2);
      > ```
      >
      > 

### 完整性约束

#### 简单约束

- 域约束(Domain Constraint)：列的类型
- 主键约束(Primary key)：附加声明只适用于主键是一个属性的情况；独立声明适用于任何情况，主键可以包含多个属性，不可以为NULL
- 候选键约束(Unique)：附加声明和独立声明都同主键，可以为NULL
- 外键约束(Foreign Key ... References ...)， 又称作引用完整性(Referential Integrity)
- Not Null

在记录增(insert)删(delete)改(update)时，数据库系统自动检查完整性约束的正确性，拒绝不正确的操作。此处需要留意外键约束的检查，其他约束只需要检查本表即可。

- 外键约束的检查
  - 主键所在的表插入记录不会有问题，外键所在表删除记录不会有问题
  - 其他需要进行检查
- 解决办法
  - No Action：当检测出现问题，就拒绝执行
  - Set null：外键列设为空
  - Cascade：对外键列执行外键表中相应的修改

#### 复杂约束

- 表约束(Check)

  - SQL **Check(condition)**
  - 对单个属性的检查可以写在列的声明后面，在insert或update修改了check对应的列时，进行检查；delete默认**不检查**check条件(一定要思考一下会不会使得条件出问题，如果会，改用断言)
  - 复杂检查单独写(性能代价可能很大)
  - 约束命名，在约束前使用`Constraint`
    - Deferred延迟执行（在一个事务结束时才检查） `set constraint Name Immediate`
    - 也可以恢复立即执行（默认）`set constraint Name Immediate`

- 断言(Assertion)

  - 断言是schema的一部分，使用create, drop来处理，可以认为是独立于表单独定义的check

  - 创建断言：`create assertion Name check (cond);`

  - 删除断言：`drop assertion;`

  - 在表增删改时都会检查断言

  - 涉及多个表的Check约束，不能写成表约束，因为表约束只会检查当前定义的表

  - ```sql
    create assertion CSassert
    	Check( not exists(
            -- 这里是SQL语句
        	));
    ```

  - 

- 触发器

  - 当出现了给定的事件，系统检查给定的条件，如果条件满足，那么系统执行动作。

  - 事件(Event: insert, delete, update) + 条件(Condition) + 动作(Action: SQL)

  - 主动数据库：采用触发器的数据库

  - ```sql
    create trigger ImproveTrigger
    -- 事件：在修改了GPA之后
    after update of GPA on Student
    referencing
    	old row as OldTuple
    	new row as NewTuple
    for each row
    -- 条件：GPA提升超过10%
    when (NewTuple.GPA > OldTuple.GPA*1.1)
    	insert into StudentImprovement
    	-- 动作：记录到StudentImprovement表中
    	values(NewTuple.ID, OldTuple.GPA, NewTuple.GPA);
    ```

  - 设计

    - 事件：`before / after + update of 'col' on 'table' / (insert / delete) on 'table'`
    - 引用新值和旧值：和具体的修改操作有关，但insert不需要旧值，delete不需要新值
    - for each row：对每个记录都分别执行一次条件加动作
    - for each statement：对于整个修改语句，只进行一次条件加动作
    - 条件：可以缺失，即永远为真
    - 动作：可以是一组语句，用begin和end括起来
    - 有额外性能代价，优先使用前两种。



合法的实例(Legal Instance)：满足完整性约束的实例

### 逻辑设计

ER图 --> 关系模型

- 下划线对应主键

- 参与实体的主键作为外键，联系集的属性

  

单一实体集内部的联系

Key constraint-->ER图的联系集上每个键约束的实体集，主键都是联系集的候选键

Total participation --> not null



弱实体集
满足key constraint + Total participation

### 物理设计：索引

#### 创建索引(Index)

- DDL的一部分：`create、drop`

- 建立索引：`create index 索引名 on 表名(列名,...,列名)`

#### 删除索引

不同数据库语法有区别

- Postgresql:` drop index 索引名`

### 安全设计：视图(View)

视图是一个关系表，但是不直接存储，需要时直接从视图定义计算得到。常用语需要经常查询的内容

- 创建视图（可以对属性重命名）

```sql
create view 视图名(列名，...，列名) as
	-- 下面是正常的sql查询语句
	select 语句;
```

- 删除视图

```sql
drop view 视图名;
```

- 与基本表的对比

|        | 读(select) | 写(insert/delete update) |
| :----- | ---------- | ------------------------ |
| 基本表 | ✅          | ✅                        |
| 视图   | ✅          | 特殊情况下可以更新视图   |

- 视图读

``` sql
select Cname
-- 数据库系统实际上会把视图展开，变成嵌套查询
from 视图名
where Sname = 'A';
```

- 视图写
  - 一般来说不允许对视图的增删改操作
  - 但满足特定条件时可以更新
  - Updatable View：视图的记录直接对应到基本表的记录
    - 不能有连接，From必须是单个关系——因为很难从连接的结果反推原表的内容。
    - Where如果有子查询，子查询不能包含R
    - Select必须包含足够多的列——插入时可以用NULL或默认值填充剩余的列。
    - 没有去重和聚合——每个结果的行只对应一个原始行。
  - 对视图实际操作时是转化为基本表的操作来进行
  - 需要慎重
- SQL通过Grant和Revoke命令来进行控制访问
  - 控制访问：**Grant**，授予特定用户table或view的访问权限
    - `grant 访问权限 on 对象 to 用户[with grant option]`
    - 对象：基本表或者视图
    - 访问权限：
      - select
      - insert(列名)：插入指定列
      - update(列名)：修改指定列
      - delete
      - references
      - all
    - `with grant option`这个用户可以把得到的权限授予给别人
    - 数据定义语句的权利(create, alter, drop)不能授予或收回
    - 建立视图时必须拥有对用到的表的select权限
  - 去除访问权限：**Revoke**
    - `revoke [grant option for] 访问权限 on 对象 from 用户 [cascade|restrict]`
    - 对象：基本表或视图
    - 访问权限同grant
    - `cascade`同`with grant option`(会顺带着回收衍生的权限)
    - `restrict`收回权限，若有衍生权限则拒绝执行
    - `grant option for`只收回进一步授权的权限
  - 授权图
    - 授权者----(授权者，被授权者，权限，yes/no)---->被授权者
    - 如果撤销了基本表授权，对应视图会被删除
  - 

### 数据冗余与范式

#### 数据冗余

冗余：同样的数据在数据库中被存储了多次，会带来冗余存储，更新/插入/删除异常。

解决方法：模式分解

#### 范式介绍

分类

- 函数依赖(FD)
- 多值依赖(MVD)
- 连接依赖(JD)

范式：规范的形式，关系模式的每种范式，消除了一种冗余。

<img src="/Users/yaoyongzhou/Library/Application Support/typora-user-images/NF.png" alt="image-20250703161423096" style="zoom: 33%;" />

函数依赖(FD)

- $X\rightarrow Y$任意两个元组在X上取值相同，则他们在Y上取值也相同
- 称为：**X函数确定Y，Y函数依赖于X**

- 候选键是一种特殊的函数依赖

  - 定义：满足$X\rightarrow U; \; \forall Y \sub X , Y\nrightarrow U$
  - 超键(Super key)：只满足$X\rightarrow U $

- 推导：

  - Armstrong规则
    - 自反律：$if\;Y \subseteq X,\; then\; X\rightarrow Y$
    - 增补律：$if\;Y \rightarrow X,\; then\; \forall Z, XZ\rightarrow YZ$
    - 传递律：$if\;X \rightarrow Y\; and \;Y \rightarrow Z,\; then\; X\rightarrow Z$
  - 分解: $if\;X \rightarrow YZ\; \; then\; X\rightarrow Z \; and \;X\rightarrow Y$
    - 证明：自反+传递
  - 合并: $if\;X\rightarrow Z \; and \;X\rightarrow Y \;then \;X \rightarrow YZ\; $
    - 证明：增补两次
  - 分解合并是等价的

- 函数依赖的闭包

  - 反复应用Amstrong规则，直到不增加新的函数依赖为止

  >
  >求函数依赖的闭包
  >
  >Contracts(**c**ontracted, **s**upplied, **p**rojected, **d**eptid, **p**artid, **q**uantity, **v**alue)
  >
  >假设已知以下函数依赖关系
  >
  >- C是主键, C--> SJDPQV
  >- 一个项目买一种零件只用一个合同：JP-->C
  >- 一个部门从一个供货商至多购买一种零件：DS-->P
  >
  >即，已知$F= \{C\rightarrow SJDPQV,\; JP\rightarrow C,\; DS\rightarrow P\}$，求$F^+$
  >
  >- 增补律：$C\rightarrow SJDPQV\;so\;C\rightarrow CSJDPQV$
  >- 传递律：$JP\rightarrow C\; so\;JP\rightarrow CSJDPQV$
  >- 增补+传递：$DS\rightarrow P,\; JD \rightarrow JP,\; JDS\rightarrow CSJDPQBV$
  >- 这里C、JP、JDS都是候选键

#### 函数依赖与2NF，3NF，BCNF的关系

- 类型1:平凡依赖，属性集X-->单个属性A——不存在数据冗余问题
- 类型2:X是超键——也是正常的，没有数据冗余问题
- 类型3:部分依赖**消除后变成2NF**，X是候选键的一部分，A是非键属性——会导致冗余
- 类型4:非键传递依赖**消除后变成3NF**，X包含非键的属性，X不是超键，A是非键属性——会产生冗余
- 类型5:对于键属性的函数依赖**消除后变成BCNF**——会产生冗余

- 1NF(属性均为原子类型)--(消除部分依赖)-->2NF--(消除非键传递依赖)-->3NF--(消除对键属性的函数依赖)-->BCNF
- 判断方法：找出所有的依赖关系，然后依次判断是否满足几种依赖



#### 关系模式的分解

把关系模式R分解为X和Y($\pi_X(R) \pi_Y(R)$)

分解通过投影实现，投影后要去重——可能会引起信息丢失

无损分解

- 当且仅当分解后两个表的自然连接可以得到原始表，即$\pi_X(R)\bowtie \pi_Y(R) = R$
- 与函数依赖的关系：如果可以证明$R1 \cup R2 = R$且$R1 \cap R2 \rightarrow R1$，那么对R进行R1和R2的分解是无损分解
  - 理解：这个连接的join key：$R_1 \cap R_2$
  - $R_1 \cap R_2 \rightarrow R_1$，R1的主键是R2的外键，主键外键之间的连接是无损的，但这只是无损的必要条件
- BCNF只允许$X\rightarrow A$
  - 平凡依赖：$A\in X$
  - X是超键
- 如果不满足BCNF，则可以无损分解
  - $X\sub R$， $A\in R$是单独属性，$A\notin X$不平凡依赖
  - 对R进行XA和R-A的分解



#### 多值依赖(MVD)

- $X\rightarrow \rightarrow Y$
  - $Z=R-XY$
  - 在X相同的情况下，Y和Z的全组合都出现
- 函数依赖是多值依赖的特例
- 平凡多值依赖
  - $Y\subseteq X$
  - $XY =R$



## TODO: NF这一块太不扎实了



#### 4NF



#### 5NF





## 数据存储与访问



### 概述

#### 数据库系统内部架构概述

通常的系统为Client / Server形式

前端

- **SQL Parser**：将SQL语句变成解析好的内部表达
- **Query Optimizer**：将SQL内部表达变为Query Plan

后端

- **Data storage and indexing**：在硬盘上存储数据并高效访问
- **Buffer pool**：在内存中缓存硬盘的数据，数据重复使用，优化写操作

- **Execution Engine**：根据query plan，完成相应的运算和操作，得到SQL语句的结果
- **Transaction management**：事务管理，实现ACID，后续介绍

#### 数据存储与访问概述

- 存储层次：见之前

- 磁盘阵列(Disak Array)

  - RAID(Redundant Array of Independent Disks)

  - RAID0

    - Striping(条带)
    - <img src="/Users/yaoyongzhou/Library/Application Support/typora-user-images/image-20250703205630219.png" alt="image-20250703205630219" style="zoom:25%;" />
    - 顺序读：映射为K个硬盘的顺序访问，K倍带宽
    - 随机读：映射为单块硬盘的随机访问，并发支持K个随机访问，K倍带宽
    - 随机写/顺序写：同读操作
    - 可见空间：K*单个磁盘空间
    - 不支持恢复

  - RAID1

    - Mirroring（镜像）
    - K个磁盘存储完全一样的数据
    - 顺序读/随机读：K个硬盘并发访问，K倍带宽
    - 随机写/顺序写：每个盘都得写，需执行k次，不能并发执行
    - 可见空间：1*单个磁盘空间，利用率为1/K
    - 恢复：插入一块替换盘，然后复制全盘

  - RAID5

    - Parity(异或)，又称奇偶校验，一组 $A_p = A1\oplus A2 \oplus A3$
    - <img src="/Users/yaoyongzhou/Library/Application Support/typora-user-images/image-20250703210716324.png" alt="image-20250703210716324" style="zoom:25%;" />

    - 把parity块轮流放置是因为单独放置会降低读写性能(这一块盘就不能用了)

    - 顺序读/随机读：类似RAID0

    - 顺序写/随机写：每写一个Stripe Unit要写两个磁盘，分别是数据和Parity，为了计算Parity要读两个盘，即旧的数据和旧的Parity

    - 可见空间：(K-1)*磁盘空间

    - 恢复：一个盘坏了，读剩余K-1个盘，计算新盘数据即可

  - RAID6
    - 类似RAID5，但每组unit会产生2个Parity块
