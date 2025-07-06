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

  - 组合RAID
    - 基本思想：RAID X+Y
    - 0+1
    - <img src="F:\work\notes\UCAS_Database\fig\RAID_0_1.png" alt="RAID_0_1" style="zoom:50%;" />
    - 1+0
    - <img src="F:\work\notes\UCAS_Database\fig\RAID_1_0.png" alt="RAID_1_0" style="zoom:50%;" />
    - 5+0
    - <img src="F:\work\notes\UCAS_Database\fig\RAID_5_0.png" alt="RAID_5_0" style="zoom:50%;" />

- 操作系统支持

  - 本地文件系统(Local File System)：用户通过系统调用向内核态的文件系统发送请求

  - 块设备：可以直接打开Raw Partition

  - | 数据库                         | 文件系统                               |
    | ------------------------------ | -------------------------------------- |
    | 存储文件                       | 存储数据表                             |
    | 通用，可以存储任何数据和程序   | 专用，针对关系型数据库进行存储         |
    | 文件无结构，一串字节           | 数据表由记录组成，每个记录包含多个属性 |
    | 操作系统内核中实现             | 用户态程序中实现                       |
    | 提供基本编程接口：open、read等 | 提供SQL接口                            |

  - 共同点：都存储在外存，数据根据硬盘特征分成定长的数据块

- 数据在硬盘上的存储

  - 硬盘最小存储访问单位：一个扇区，512B
  - 文件系统访问硬盘的单位：4KB
  - RDBMS最小的存储单位：database page size，可以设置为1或多个文件系统的page
  - 在磁盘上进行空闲块的管理，一般依托操作系统的文件系统来管理

##### 记录文件格式

- 单个记录怎么存：行式记录结构

  - 目标：记录所有属性，都连续存储在一个字节串中
  - 定长列存在System catalog中，变长列存在每个记录中
  - <img src="F:\work\notes\UCAS_Database\fig\Tuple_structure.png" alt="Tuple_structure" style="zoom:50%;" />
  - Page内部结构
  - <img src="F:\work\notes\UCAS_Database\fig\slotted_page.png" alt="slotted_page" style="zoom:50%;" />
  - 跨块记录：当记录大于一个数据页时，可以划分为多个子记录，存储在不同的数据页中，多个子记录之间相互指向

- 记录文件的组织

  - 堆文件：记录是无序的，多条记录组成数据页，文件由多个数据页组成
    - 插入：找到一个具有足够空间的page，插入新纪录

  - 排序文件：记录按照某个顺序排序
    - 插入：找到该记录应该放置的page，如果page有足够空间就直接插入；否则在临近page中找空间并移动记录，或者把当前page分裂为两个page，page header包含溢出块的地址
  - 删除：找到对应page，删除page上的记录，有时需要保留对应的slot，记录一个删除标记
  - 更新：
    - 修改后长度比原长度短或不变，那么在原纪录位置修改
    - 修改后长度比原长度长，那么就类似插入，可以先移动page内的tuple获得一片连续的空间放置记录；如果空间不足就考虑临近page或者溢出

##### 缓冲池管理

Buffer Pool在内存中，避免每次访问都需要读写硬盘，提高性能，减少I/O

数据访问的局部性(Locality)

- 时间局部性：同一数据元素可能会在一段时间内被多次访问——buffer pool
- 空间局部性：位置相近的数据元素可能会一起被访问——Page

Buffer Pool的组成

- 内存空间分为page大小的单元，每个单元可以缓冲一个page
- 其中，Buffer head结构记录Page的元信息
  - 每次访问一个page都需要查找pageid索引，如果存在，那么返回内存地址并访问

> 给定PageID = A,访问Page A的过程
>
> 1. 查找索引，判断Page A是否在buffer pool之中
>
> - 是，buffer pool hit，直接访问buffer pool中的Page即可
> - 否，buffer pool miss，那么需要在buffer pool中找到一个可用的frame，从硬盘读page A，放入这个frame

- 替换算法
  - 如果有空闲块，直接替换
  - 否则，找一个已经缓存的page（被称为Victim page），替换掉
  - 如果这个块被修改过，是dirty块，则需要写回硬盘
  - LRU(Least Recently Used)算法
    - 找到时间戳最早的页并替换，需要检查N个page，代价为O(N)
    - 也可以设计为，当一个页被访问时，自动将其移动到最前端，这样直接选择最后一个page即可，问题是回带来修改队列的代价
  - Clock算法
    - 在Buffer head中记录R，取值为0或1
      - R=0表示访问时间比较近，R=0表示很久未访问
      - 访问一个页后复制为1
      - 在替换时，根据R进行判断，R=1则修改为0，并查看下一页；R=0替换掉

### 索引概念

```sql
 select Name, GPA
 from Student
 where Major = '计算机';
```

- 数据的顺序访问：顺序读取表的每个page，对于每个page顺序访问每个tuple，检查条件是否成立，如果成立则读取name和gpa。

- 如果表中有M个page的话，上述访问的I/O代价就是M；如果专业太多就会导致效率很低，大部分记录会被扔掉——引入索引，不需要顺序扫描整个表就可以找到相关的记录
- 索引(Index)
  - Key键-->索引-->Value值--记录+记录位置（Record + RecordID）
  - 给定一个键，找到对应的值（比如某一个记录）
  - 分类：树结构索引，哈希索引
  - 聚簇索引（数据页与索引顺序一致）二级索引（不一致）

### B+Tree索引

<img src="F:\work\notes\UCAS_Database\fig\B+Tree.png" alt="B+Tree" style="zoom:50%;" />

- 是二叉树的推广
  - 每个节点是一个page
  - 所有key存储在叶子节点
  - 内部节点完全是索引作用

- 所有叶子逻辑上组成一个数组
  - <img src="F:\work\notes\UCAS_Database\fig\B+_tree_node.png" alt="B+_tree_node" style="zoom:50%;" />
  - 内部Keys从左向右，从小到大排序
- 内部节点
  - <img src="F:\work\notes\UCAS_Database\fig\inter_node.png" alt="inter_node" style="zoom:50%;" />
  - $subtree_0 < key_1 \leq subtree_1 < key_2 \cdots < key_n$
- Search：从根节点到叶子节点，在每个节点中进行二分查找
  - 代价：共有N个Key，每个节点的子节点数为B，树高为$O(log_BN)$，每个节点内部二分查找：$O(log_2B)$，总比较次数为$O(log_BN)\times O(log_2B)=log_2N$
  - I/O次数为$O(log_BN)$
- Insertion：先查找后插入，如果叶子节点未满，直接插入；否则需要节点分裂(node split)
  - 分裂规则：平分为两个叶子节点，并平均分配已有的key，同时将新叶子节点插入父节点，如果父节点满了则继续分裂并向上插入
- Deletion：先查找后删除
  - 节点如何合并？原设计是少于一半合并，实际是节点为空才合并或者完全不合并
- Range Scam：找到起始叶子节点，然后顺着叶子链接往下读，直到遇到范围终止值

> 练习1
>
> 假设每个节点的child/pointer 个数最多为3
>
> 树中包含如下key：2、4、6、8、10、12、14、16、18、20、22、24
>
> 要求
>
> - 节点尽量放满
>
> - 内部节点至少有2个孩子
>
>   <img src="F:\work\notes\UCAS_Database\fig\answer1.png" alt="answer2" style="zoom:50%;" />
>
> ---
>
> 练习2
>
> 假设每个节点的child/pointer 个数最多为3
>
> 树中包含如下key：2、4、6、8、10、12、14、16、18、20、22、24
>
> 要求
>
> - 叶子节点至少保留一个空位
> - 内部节点至少有2个孩子
>
> <img src="F:\work\notes\UCAS_Database\fig\answer2.png" alt="answer2" style="zoom:50%;" />
>
> ---
>
> 练习3：基于练习1的结果，画出`insert(21)`后的$B^+-Tree$
>
> <img src="F:\work\notes\UCAS_Database\fig\answer3.png" alt="answer3" style="zoom:50%;" />
>
> ---
>
> 练习4：基于练习3的结果，画出`insert(17)`后的$B^+-Tree$
>
> <img src="F:\work\notes\UCAS_Database\fig\answer4.png" alt="answer3" style="zoom:50%;" />



##### 索引数据结构访问

```sql
select Name,GPA
from Student
where Major = '计算机';
```

在二级索引中搜索专业为计算机的项，对于每个匹配项，访问Tuple并读取Name和GPA

如果有k个匹配项，则需要读k次

- 选用顺序访问还是二级索引访问？
  - 根据selectivity选择时间小的方案——query optimizer的任务

### 哈希索引

##### 哈希函数h()

目的：键值key-->近乎随机的整数

##### 哈希冲突

原因：不同的键值key对应到同一个整数了

解决方法

1. 扩展hash bucket
   - 每个hash bucket不仅存单个key值，而是可以包含多个slots，每个slot可以存一个key-value
2. 增加额外的空间
   - 在hash bucket数组之外，另外分配空间
     - 链式哈希(chained hashing)
       - 基于内存：每个链表元素包含1个或多个slots
       - 基于外存：每个链表元素是一个page
     - 增加溢出桶(overflow bucket)
       - 插入时如果hash bucket已满，放入溢出桶
       - 查询时先在hash bucket中找，如果没找到且hash bucket已满，再去溢出桶中查找
     - 放入其他索引结构
       - 类似溢出桶，只不过用索引代替溢出桶
3. 允许使用其他hash bucket开放定址(Open Addressing)
   - 允许一个key有多个可以选择的桶
   - 方法
     - 线性探测(Linear Probing)：不断地探测下一个位置，直到找到空位插入
       - 平方探测(Quadratic Probing)：探测间隔变为平方
       - Robin Hood hashing: 
         - psl(probe sequence length): 当前位置-原始哈希位置，尽量减小psl
         - 删除时不能直接删除，Tombstone方法：把被删除位置标记为一个特殊桶，查找时跳过，插入时当做空，可以插入
     - 二选哈希(2-choice hashing)
       - 采用两个哈希函数计算两个桶
       - 插入：插入两个桶中较空的桶
       - 查找：查看两个桶
       - 效果：可以大幅提高空间利用率
     - 二选哈希变形：cuckoo hashing
       - 每个key有两个合法位置
       - 布谷鸟换位：把一个key换到另一个合法位置的桶中

##### Rehashing

为什么需要rehashing?

- 哈希索引需要扩容来存储更多的key，链式哈希需要扩容来提高性能

方法：创建一个新的哈希索引，size变大，把原来的哈希索引插入到新的中，并释放原哈希索引的空间

效果：可用空间翻倍，链式哈希的平均链长减半

问题：rehashing时间长，导致很高的尾延迟

优化目标：减少单次rehashing的时间

- Extendible Hashing(可扩展哈希)
  - 把单次Rehashing分解为多个小步骤，每个小步骤仅Rehashing一个hash bucket，从而降低单次索引的尾延迟
  - 难点：确保每个小步骤之后仍然是合法的哈希索引
  - 方案：采用一种特殊的哈希函数，使得size加倍后，原有的bucket和新的bucket有某种对应关系
    - bucket下标：hash后的整数的前G位(G for global depth)
    - L for Local depth， 每个page中的前L位相同
    - 在插入时，比较分裂页的L和G，如果L==G，只需要G=G+1,；如果L<G，只需要分裂
  - 存在的问题：当数据分布不均匀时，可能有大量的header，会造成一定的浪费和性能问题
- Linear Hashing(线性哈希)：自行了解



### 其他索引结构

#### 字典树Trie/Radix Tree

<img src="F:\work\notes\UCAS_Database\fig\Trie_Tree.png" alt="Trie_Tree" style="zoom:50%;" />

- 把key切分为多个段，每段S bits（S for Span）

- 从最高位到最低位，每段对应一层
- 每段的取值作为下标访问指针数组，找到孩子的指针
- 优点：易于支持变长键；缺点：空间浪费

Span = 1时成为二叉树，空间浪费

- Patricia Trie：按前缀合并，路径压缩

Span = 8 存储一个字节时，也会产生只有一个孩子的节点，造成空间浪费

- ART(Adaptive Radix Trie)：也是路径压缩，设计了256/48/16/4，分别支持对应数量个孩子
- Masstree(Span = 64)：每层8B，每个节点都是一个B+-Tree

#### ISAM索引(Indexed Sequential Access Method)

排序文件+多层索引

<img src="F:\work\notes\UCAS_Database\fig\ISAM.png" alt="ISAM" style="zoom:50%;" />

- 插入：索引部分不变，数据部分增加溢出页

<img src="F:\work\notes\UCAS_Database\fig\ISAM_insert.png" alt="ISAM_insert" style="zoom:50%;" />

#### 支持多个重复的键

相同的key有多个记录？

1. 直接作为不同的项
   - 问题1：需要修改索引结构，对于树而言很麻烦
   - 问题2：很多项都有相同的key时，需要在索引中存很多份，额外空间开销
2. 给key增加后缀
   - New key = (key, suffix)
   - 问题是重复存储key，新的key会变长
3. 间接层
   - 在树或者哈希表中只记录唯一的key
   - 多个RecordId记录在间接层中

#### 位图索引(Bitmap Index)

概念

- 把间接层记录为多个位图
- 每个不同的key对应一个bitmap：1表示取值为key，0表示取值不为key
- 适用于unique key少的情况下：对于姓名显然就不合适

优势

- 过滤条件的运算可以直接通过位运算来实现

压缩

- 当1的个数很少时，可以进行压缩
- 记录两个1之间的距离，使用时解压进行处理

#### 倒排索引(Inverted Index)

主要用于对文本进行索引

目标：给定关键词term，找到相关文档，并对结果排序

需求：同一个key对应着大量的记录

在term上建立索引很简单，记录每个term对应的DocID和TF(term frequency)是难点

引入结构：倒转表(Inverted List)

- Term --> (DocID_1, TF_1), (DocID_2, TF_2), ... ,  (DocID_n, TF_n)
- DocID从小到大排序，只记录相邻两项DocID的差值
- 采用适当的变长整数编码

### 多维索引概念

##### 基本概念：

前面讲的都是多个列上的索引，这里开始介绍多维索引(2-10列，更高维的本课暂不涉及)

- 应用场景：地理信息系统、集成电路设计、多列过滤的实现
- 典型查询类型
  - 部分匹配：制定一维或者多维的值，查找匹配对象
  - 范围查询：给出一维或多维的范围，查找匹配对象
  - 最近邻查询：查找与给定点最近的点

##### 基于单维索引的支持 & 多维编码

- 方法1：简单拼接
  - 多个属性拼接为一个key
  - B+-Tree
    - 可以先比较单个属性，相同时再比较下一个——本质上仍然是单维的索引，但是不同列的重要性不同
    - 对应到上面几个查询类型，如果给出后面属性的值或范围而不指定前面属性的话，很难支持，可能会退化为全树搜索
  - hash表
    - 拼接之后本质上也是单维索引
    - 还是会遇到上面的无法支持典型查询类型的问题
- 方法2：Z-Order编码
  - 基本思想：多维数据放入一维，把二维数据点重排列嵌入一维数轴，这样二维空间中相近的点在排列的一维顺序中也接近
  - <img src="F:\work\notes\UCAS_Database\fig\z_order_map.png" alt="z_order_map" style="zoom:50%;" />
  - 编码转换映射计算：
  - $X=X_kX_{k-1}\dots X_1X_0\;\;Y=Y_kY_{k-1}\dots Y_1Y_0$
  - $Z-code(X,Y) = X_kY_kX_{k-1}Y_{k-1}\dots X_1Y_1X_0Y_0$

##### 

### 多维散列索引

#### 网格文件(Grid File)

- 基本思想：把整个空间切分为一个个长方形的网格，每个网格就是一个桶（存放在数据页中），桶矩阵记录桶的元信息

- 划分：每个网格包含数量基本相似的记录
- 多维：
  - 网格是多维空间中的一个矩形空间
  - 每维的属性，按从小到大排列划分为多个区间
  - 网格文件由两个数据结构组成
    - 桶矩阵，K维矩阵($S_1 \times S_2\times \dots \times S_k$个元素)
    - 每个矩阵元素对应一个网格，记录网格的存储位置，如果数据页不够大，那么采用溢出链

- 查询：

  - 部分查询：先根据一维数据找到相关的桶，然后在桶网格页内搜索
  - 范围查询：找到相关的网格，然后读取网格页，进一步搜索
  - 最近邻查询：先搜索所在网格，再搜索周围网格

- 插入：

  - 可以分配一个溢出页，也可以进一步划分网格

- 关键难点：

  1. 给定一组数据如何计算网格 

  2. 插入数据时如何划分网格



#### 分段散列(Partitioned Hashing)

基本思想：每个纬度一个哈希函数，把各个维度的哈希函数的输出拼接在一起

如何查询：

- 部分匹配：给定部分维，可以穷举其他维，来选择相应的桶
- 范围查询/最近邻：无法支持，哈希不支持不等值比较

对比网格文件：

- 查询上，分段散列不支持范围查询/最近邻；网格文件都支持
- 数据结构上，分段散列把数据比较均匀地分布到桶上；网格文件当维数比较高时，可能存在大量的空网格

### 多维树结构索引

#### 四叉树(Quad Tree)

二维：

- 每个树节点代表二维空间中的一个长方形，内部节点等分为四个子长方形，对应四个孩子节点
- 如果子树的记录点可以存放在一个页中，就可以用一个叶子节点表示；否则就生成一个内部节点，需要进一步划分为4个孩子节点
- <img src="F:\work\notes\UCAS_Database\fig\quad_tree.png" alt="quad_tree" style="zoom:50%;" />
- <img src="F:\work\notes\UCAS_Database\fig\quad_tree_example.png" alt="quad_tree_example" style="zoom:50%;" />
- 点查询：与当前节点比较，判断是四个象限里哪一个，然后找下去，找到一条根到叶子的路径
- 部分匹配：只看一个维度的值进行判断
- 范围查询：同上，依次找
- 最近邻：找最近的块，其余块剪枝掉
- 插入：找到叶子结点，如果不满直接插入；如果已满，则分裂为4部分

K维四叉树：

每个树结点代表k维空间中的一个长方形，内部结点长方形等分为$2^k$个子长方形，对应$2^k$个孩子结点

#### KD-Tree

- 每层轮流用下一个维度，这个维度分为两半

<img src="F:\work\notes\UCAS_Database\fig\kd_tree.png" alt="kd_tree" style="zoom:50%;" />

- <img src="F:\work\notes\UCAS_Database\fig\kd_tree_example.png" alt="kd_tree_example" style="zoom:50%;" />

- 查询：
  - 部分匹配：递归遍历整个树，在对应维度剪枝
  - 范围查询：类似部分匹配
  - 最近邻：类似四叉树
- 插入：
  - 首先查询到插入位置
  - 然后插入新节点
  - 可能会使树不均衡
- 删除：
  - 删除叶子节点无所谓，但删除内部节点需要找到对应维度的替代节点
  - <img src="F:\work\notes\UCAS_Database\fig\delete.png" alt="delete" style="zoom:50%;" />
- 支持外存存储
- 多路KD Tree

#### R-Tree

MBR(Minimum Bounding Rectangle)

外接矩形

每个索引项、树结点都代表一个区域，称为MBR，是包含该索引项、子树中所有索引项的最小的外接矩形

两个树节点的MBR可能有重叠的区域，B+Tree没有

- 叶子节点：
  - 数据记录：存储具体对象的边界点信息
  - MBR：单个对象的外接矩形
  - 没有兄弟链表
- 内部节点：
  - MBR：子树的外接矩形
  - 子树的MBR可能相交
- 对节点的要求
  - 每个节点中MBR的项满足$m<n<M$

<img src="F:\work\notes\UCAS_Database\fig\r_tree.png" alt="r_tree" style="zoom:50%;" />

- 查询：类似四叉树
- 插入：
  - 问题1：重叠的孩子？
    - 目标：减少MBR增量，选择增量最小的孩子节点
  - 问题2：节点分裂？
    - 目标：分裂后的两个节点MBR较小
- 删除：
  - 首先查询，然后删除mbr
  - 如果删除后叶子节点满足$n\geq m$，完成
  - 否则，需要删除整个叶子结点，并将每个MBR都重新插入R-Tree

### 物理数据库设计原则（索引选择）

#### 原则1：是否建立索引？

数据库自动建立的索引：主键、外键

用户建立索引：目的是快速获取满足某种过滤条件的记录，只有where语句中涉及的列才需要索引

#### 原则2：索引key的选择，索引的种类

等值条件：哈希索引，B+树

范围选择：B+树



数据很少写：Bitmap index

文本数据：Inverted index

多维数据：多维索引



#### 原则3：多属性索引key

where语句中包含同一个表的多个属性

可以考虑：单维索引 / 单维索引+多维编码 / 多维索引

#### 是否聚簇(Clustered) 索引

基本原则：一个表只能按照一个索引进行聚簇，仅有一个主索引，其他索引都是二级索引

范围查询收益最大

#### 考虑索引的开销

收益：提高查询速度

开销：

- 空间开销：外存开销、内存开销
- 时间开销：维护开销，当对表的内容进行修改时，也要修改对应的索引



#### 只需要索引就可以执行的查询

如果查询的所有列都可以在索引中找到，那么这个查询实际上可以用索引来完成

- Covering Index

#### 辅助工具



## 查询处理

### 概述

#### 系统目录(System Catalog)

又称目录表，或数据字典，或目录

存储数据的元信息

- table信息
- 索引index的信息
- 视图view的信息

<img src="F:\work\notes\UCAS_Database\fig\catalog_content.png" alt="catalog_content" style="zoom:50%;" />

系统目录的存储：目录也可以存储在关系表中

#### 查询执行方式

查询计划(Query Plan)，由query optimizer产生，最终将表现为一棵operator tree

#### 执行细节

- Operator-at-a-time：实现简单，但是中间结果表的代价较高
- Tuple-at-a-time：产生一个tuple就返回给父节点，减少中间结果
  - 调用很多次，每次返回一条记录
  - 又称火山模型、iterator迭代方法
  - 

### 选择

### 投影

### 排序和外排序



## 事务处理 Transaction Processing

### 事务概念和ACID

典型例子：银行业务、订票、购物等

大量并发用户，少量随机读写操作

#### 事务

概念：

- 由一到多个操作组成

- 读：select

- 写：insert/delete/update

- 组成一个事物的所有操作满足ACID性质

表现形式：

- 默认：单个语句

- 特殊语句来标记事物的开始和结束：多个语句

  - 开始一个Transaction：`begin transaction`

  - 成功结束一个Transaction：`commit transaction`——当前事务成功结束，数据库系统保证事务的任何写操作都不丢失

  - 异常终止一个Transaction：`rollback transaction`——丢弃事物的所有修改状态，返回初始状态

ACID：DBMS保证事物的ACID性质

- **A**tomicity原子性——要么完全执行，要么完全没有执行
  - 数据库内部异常；掉电；事务逻辑本身决定没有达到预期需要非正常终止
  - 前两种需要自动恢复
- **C**onsistency一致性——从一个正确状态转换到另一个正确状态
- **I**solation隔离性——每个事务与其他并发事务互不影响
- **D**urability持久性——commit之后，结果持久有效，crash也不消失



### 并发控制Concurrency Control

#### 数据冲突和可串行化

同一个数据并发读可以，但如果并发写或者一读一写，就会导致数据竞争(data race)，需要合适的调度(Schedule)

- 正确性问题：如何判断一组事务正确执行
- 存在一个顺序，按照这个顺序依次串行执行这些事务，得到的结果与并行执行相同
- 可串行化Serializable

使用优先图来判断一个并发执行是否可串行化

- 图的顶点：每个事务
- 图的有向边：如果$T_i$与$T_j$的操作冲突，并且$T_i$的操作先于$T_j$的操作，那么就有一条从$T_i$指向$T_j$的边
- **可串行** 等价于 **优先图中没有环**
- 拓扑排序即可得到一个串行化的顺序

数据冲突带来的问题

- Read uncommitted data 写后读：T2 commit之前，T1读了T2修改过的数据
- <img src="F:\work\notes\UCAS_Database\fig\data_race_1.png" alt="data_race_1" style="zoom:50%;" />
- Unrepeatable reads读后写：T1在T2 commit之前写了T2读的数据，T2再次读同一个数据，会有不同的值
- <img src="F:\work\notes\UCAS_Database\fig\data_race_2.png" alt="data_race_2" style="zoom:50%;" />
- Overwrite uncommitted data 写后写：T2 commit之前，T1重写了T2已经修改了的数据
- <img src="F:\work\notes\UCAS_Database\fig\data_race_3.png" alt="data_race_3" style="zoom:50%;" />

解决数据冲突的思路：两大解决方案

- 悲观假设：假设数据竞争经常出现
- 乐观假设：数据竞争很少见



### Pessimistic 加锁

#### 2 Phase Locking

集中加锁+集中解锁

- 加锁：在事务中，对每个需要访问的数据加锁
- 解锁：在commit前，集中解锁（解锁开始，就不会再加锁了）
- 可以避免两个事务同时执行



2PL是可串行的，可以使用优先图证明



#### 锁如何实现

- lock manager：维护一个哈希表，key是数据库中对象的标识，value是正在拥有这个锁的事务以及等待这个锁的事务
- 加锁和解锁：修改hash表中的项，必须保证是原子操作
- 细节1：读锁可共享，写锁需要互斥
- 细节2：lock granularity
  - 锁的粒度，写锁的祖先必须为写锁
  - 加锁情况
  - <img src="F:\work\notes\UCAS_Database\fig\lock1.png" alt="lock1" style="zoom:50%;" />
- 实现细节3：deadlock
  - 死锁条件：循环等待
  - 死锁避免：
    1. Preordering of Resources：预先确定lock对象的顺序
    2. NO_WAIT：事务加锁不成功立刻abort，缺点是可能造成很多rollback
    3. WAIT_DIE：规定事务的优先级顺序，加锁不成功时，比较两个事务的优先级
       - 尽量abort优先级低的事务
  - 死锁检测：对长期等待的事务检查是否有循环等待，如果有就选择环上其中一个事务回卷

### 基于时间戳的并发控制

乐观假设：冲突很少见，就不需要加锁

- Time-Stamp Ordering：基于时间戳确定事务顺序
  - 在事务开始时，分配一个时间戳TS(O)
  - 每个数据库对象O
    - RTS(O)：读O的最大事务时间戳
    - WTS(O)：写O的最大事务时间戳
  - 基本思想：确保事务能够按照时间戳顺序执行
    - 当T访问O时，比较TS(O)和RTS(O)、WTS(O)
    - 发现异常时，abort/restart事务
  - <img src="F:\work\notes\UCAS_Database\fig\trans_read.png" alt="trans_read" style="zoom:50%;" />
  - <img src="F:\work\notes\UCAS_Database\fig\trans_write.png" alt="trans_write" style="zoom:50%;" />
  - <img src="F:\work\notes\UCAS_Database\fig\btso.png" alt="btso" style="zoom:50%;" />

### Multi-version Concurrency Control(MVCC)

- 单版本：原地修改记录

- 多版本：tuple+timestamp作为一个版本
  - 在更新tuple时，不修改原来的tuple，而是产生一个新版本
  - 按照版本排序，多个版本组成一个列表
  - 优点
    1. 并发控制：可能减少由于读写冲突引起的abort
    2. time-travel：可以回到过去的时间点完成查询
- 目标：事务执行顺序=事务时间戳顺序
- 与basic time stamp ordering不同的是，每个数据库对象O有多个版本，每个版本有一个写时间戳，每个版本会记录所有的读时间戳
- T读O：找到一个最大的版本满足**写时间戳小于等于TS(T)**
  - 所有的读都可以满足，不会引起abort
- T写O：找到比TS(T)大的下一个version
- <img src="F:\work\notes\UCAS_Database\fig\mvcc_write.png" alt="mvcc_write" style="zoom:50%;" />
- <img src="F:\work\notes\UCAS_Database\fig\mvcc_write1.png" alt="mvcc_write1" style="zoom:50%;" />

### Optimistic Concurrency Control(OCC)

每个事务有一个私有工作区，其他事务不可见

执行过程

<img src="F:\work\notes\UCAS_Database\fig\occ.png" alt="occ" style="zoom:50%;" />

### Snapshot Isolation

Snapshot快照：一个时点的数据库数据状态快照

算法原理：

- 在事务开始时取一个快照
- 读操作总是允许
- 写操作先临时保存，在提交时检查写是否有冲突，有冲突就abort
- 两个存在写冲突的事务，先提交者成功

#### 串行化分析

并不一定是可串行化的，因为在验证时只关注了写写冲突



#### 乐观假设的并发控制

优点：冲突很少时，没有加锁的开销

缺点：冲突较多时，需要不断重试，浪费大量资源



### Crash Recovery崩溃恢复

#### 持久性的实现









