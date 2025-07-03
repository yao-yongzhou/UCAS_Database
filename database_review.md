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

    - 事件：before / after + update of 'col' on 'table' / (insert / delete) on 'table'
    - 引用新值和旧值：和具体的修改操作有关，但insert不需要旧值，delete不需要新值
    - for each row：对每个记录都分别执行一次条件加动作
    - for each statement：对于整个修改语句，只进行一次条件加动作
    - 条件：可以缺失，即永远为真
    - 动作：可以是一组语句，用begin和end括起来
    - 有额外性能代价，优先使用前两种。



合法的实例(Legal Instance)：满足完整性约束的实例

### 逻辑设计

ER图 --> 关系模型

### 物理设计



### 安全设计

