# Ch01 Course introduction & Relational Model
Oracle
## Introduction
Outline

>  **Relational Databases
>  Storage
>  Execution
>  Concurrency Control
>  Recovery
>  Distributed Databases
>  Potpourri


BusTub(on Github)

Advanced Discussion:
* Database Group Meetings
	* https://db.cs.cmu.edu

* Advanced DBMS Developer Meetings
	* https://github.com/cmu-db/terrier

### Concept: Collection of data that is related in some way

最简单的数据库：逗号分隔的.csv文件

实例：线上音乐系统

实体Entity——Artist(name, year, country)，Album(name, artist, year)

>如果我们采用基本的csv文件存储数据（也叫 flat file），我们会面对下面这些问题：
>
>1 假如数据库中 Artist的name存在拼写错误或者更改，怎么去维护数据一致性？尤其是其相关的Album中artist的字段。
>
>2 假如有人修改了一个album的year为一个无效的字符串，怎么去分析和识别？我们希望是对于album的year规定一个标准的格式——四位数字，在读取数据时，判断标准格式是否和数据格式一致。
>
>3 怎么去存储具有多位artist的album数据？
>
>4 如何查找一个特定的记录（遍历会很慢）
>
>5 如何利用同一个数据库建立一个新的应用？
>
>6 如果有两个线程同时要写同一个文件怎么办？（特别是写的数据存在冲突时）
>
>7 如果在更新数据时程序崩溃了，如何推断正确的状态（数据安全性）
>
>8 如果想要把数据库部署在多台机器上，怎么保持高可用性？
>

Definition：DB就是一个专用软件，用来帮助程序存储和分析数据，通用数据库管理系统（general-purpose DBMS）的设计允许用户进行数据库的定义、创建、查询、更新和管理，且不用担心实现的具体细节。

在关系型数据库出现之前，人们往往会不断重构（或者说重写）的方式来维护数据库代码（包括存储结构和查询方式等），这是一种十分低效的工作模式。直到Ted Codd提出关系型数据库的概念，这种数据库通过以下方式避免重复劳动：
1、Store database in simple data structures
2、Access data through high-level language
3、Physical storage left up to implementation

本质上，数据库软件为逻辑层和物理层的数据提供了一个接口。它向上对应用程序隐去接口的实现细节（查询、数据存储细节），向下存储数据到硬盘。

> A Relational data model is collection of concepts for describing the data in a database.
 
也存在其他的data model（比如说key-value，Graph，Document、Network等等），模型之间没有高下之分，只有适合和不适合的场景。数据模型将会抽象地定义我们要在数据库中存储的数据，是“我们如何组织数据”的模型。

大部分DBMS都采用关系型数据模型。

>A schema is a description of a particular collection of data, using a given data model. 

数据库中实际存储数据的方式会在schemma中定义。

## Relation Model

——Relational Model can model anything.

>**Structure:** The definition of relations and their contents.
>
>**Integrity:** Ensure the database's centents satisfy constraints.
>
>**Manipulation:** How to access and modify a database's contents.

原始的关系型数据模型中，属性值只能是原子类型（整数、浮点数、字符串），但是后来的更新（至少19年已经可以）支持存储一些JSON对象和数组，这方面的限制被放宽了。

e.g
| name          | year | country |
| ------------- | ---- | ------- |
| Wu Tang Clan  | 1992 | USA     | 
| Notorious BIG | 1992 | USA     |
| Ice Cube      | 1989 | USA     |


> A relation is unordered set that contain the relationship of attributes that represents entities.
> 
> A tuple is a set of attribute values(also known as its domain)in the relation.
> -> Values are normally atomatic/scalar
> ->The special value NULL is a member of every domain.

_n-ary_ **Relation** = **Table** with _n_ columns

### Primary key
A relation's <u>primary key</u> **uniquely identifies a single tuple**.

一些数据库会自动创建主键（如果没设置）

自动生成的主键：
-> SEQUENCE(SQL:2003)
-> AUTO_INCREMENT(MySQL)

在之前的artist和album两个表加入id主键后

Artist(<u>id</u>, name, year, country)
| id  | name          | year | country |
| --- | ------------- | ---- | ------- |
| 123 | Wu Tang Clan  | 1992 | USA     |
| 456 | Notorious BIG | 1992 | USA     |
| 789 | Ice Cube      | 1989 | USA     | 

Album(<u>id</u>, name, artists, year)
| id  | name                 | artists | year |
| --- | -------------------- | ------- | ---- |
| 11  | Enter the Wu Tang    | 123     | 1993 |
| 22  | St.Ides Mix Tape     | 456     | 1994 |
| 33  | Americ's Most wanted | 789     | 1990 | 


### Foreign key
A <u>foreign key</u> specifies that an attribute from one relation has to map to a tuple in another relation.

有了外键之后，当我们想要满足之前所提到的，一个album有多个artist参与创作的场景需求，我们可以把Album中的artist属性拿出来成为一个新的表

ArtistAlbum(<u>artist_id</u>, <u>album_id</u>)
| artist_id | album_id |
| --------- | -------- |
| 123       | 11       |
| 123       | 22       |
| 789       | 22       |
| 456       | 22       | 

这里的artist_id和album_id都是外键。
同时，这样做还有一个好处就是当我添加新的album和artist的关系时，我不能添加不存在的artist。提高了数据添加的可靠性。


### DML(Data Manipulation Languages)
How to store and retrieve information from a db. 2ways:

**Procedural:(*Relational Algebra*)**
-> The query specifies the (high-level) strategy the DBMS should use to find the desired result.

**Non-Procedural:(*Relational Calculus*)**
-> The query specifies only what data is wanted and not how to find it.

#### Relational Algebra * 7
![[Pasted image 20220627161006.png]]
6个经典运算符

- Select
	Choose a subset of the tuples from a relation that satisfies a selection predicate.
	- Predicate act as a filter to retain only tuples that fulfills its qualifying requirement. 
	- Can combine multiple predicates using conjunctions / disjunctions.
![[Pasted image 20221031211442.png]]
SQL Syntax: SELECT ... FROM ... { WHERE ... };

- Projection
	Generate a relation with tuples that contains only the specified attributes.
	- Canrearrange attributes' ordering.
	- Can manipulate the values.
![[Pasted image 20221031212012.png]]

SQL Syntax: SELECT b_id-100, a_id FROM R WHERE a_id = "a2" ;

- Union
	Generate a relation that contains all tuples that appear in either only one or both input relations.
![[Pasted image 20221103162201.png]]
SQL Syntax: (SELECT * FROM R) UNION ALL (SELECT * FROM S);

- Intersection
	Generate a relation that contains only the tuples that appear in  both of the input relations.
![[Pasted image 20221031213225.png]]
SQL Syntax: (SELECT * FROM R) INTERSECT (SELECT * FROM S);

- Difference
	Generate a relation that contains only the tuples that appear in the first and not the second of the input relations.
![[Pasted image 20221031213534.png]]
SQL Syntax: (SELECT * FROM R) EXCEPT (SELECT * FROM S);

- Product
	Generate a relation that contains all possible combinations of tuples from the input relations.
![[Pasted image 20221103162218.png]]
SQL Syntax: 
- SELECT * FROM R, S;
- SELECT * FROM R CROSS JOIN S;

- Join( Natural Join )
	Generate a relation that contains all tuples that a combination of two tuples( one from each input relation ) with a common value(s) for one or more attributes.
	![[Pasted image 20221031214325.png]]
SQL Syntax: SELECT * FROM R NATURAL JOIN S;

#### Relational Algebra Operators * 6
![[Pasted image 20221031215417.png]]

Relational Algebra still defines the high-level steps of how to compute a query.
高级表达不影响结果，但是任务执行效率会根据表达式的计算顺序而截然不同。
![[Pasted image 20221103162309.png]]

>所以实际上只告诉数据库要计算什么要好于指定计算过程，具体怎么操作都交给数据库优化和执行。这样的好处是即使数据库的规模发生了较大变化，但是应用程序本身并不需要更改。

>关系模型独立于任何查询语言而存在，SQL是一种标准化的语言而已。

# Ch02 Advanced SQL

## History 

SQL: origin from SEQUEL——structed english query language, invented by IBM

SQL means structed query language

## Relational Languages

Be composed of 3 languages: 
- Data Manipulation Language (DML) 操作数据
- Data Definition Language (DDL) 库、表结构定义
- Data Control Language (DCL) 安全性、访问权限

Also includes:
- View definition
- Intergrity & Referential Constraints
- Transactions

Important : SQL is based on **bags** (allow duplicates)

## Agenda
- Aggregation + Group By
- String / Date / Time Operations
- Output Control + Redirection 
- Nested Queries
- Common Table Expressions
- Window Functions

### Example  Database

![[Pasted image 20221101212213.png]]

### Aggregates

![[Pasted image 20221101212550.png]]

Tip: Aggregate functions can only be used in the SELECT output list.

![[Pasted image 20221103170331.png]]
这里的三种表述具有同样的结果，但是可能会在运行效率上存在差异。

![[Pasted image 20221103170508.png]]
多聚合同时使用

![[Pasted image 20221103170517.png]]
DISTINCT 关键字使用

Error Case:
![[Pasted image 20221103170623.png]]
有的数据库会报错，有的会显示一个随机的cid

### Group By

指定某一属性，按照该属性的值对所有的元组（tuple）分桶（bucket），同值的元组在一个桶内。所以这时 Aggregate 聚合做的就是在每一个桶内单独计算结果。
![[Pasted image 20221103170707.png]]
exp：
![[Pasted image 20221103170840.png]]
所以这种情况下在输出语句中的属性，也必须在Group By后面
![[Pasted image 20221103170917.png]]

Error Case：
![[Pasted image 20221103170942.png]]
对于WHERE从句来说，输出序列中的聚合还没有产生，它的结果自然是无法访问的。这时候就可以用HAVING

### Having

若将上面的 Error Case 改为下例：
![[Pasted image 20221103171244.png]]
问题出在运行到Having的时候，列还没有重命名，所以avg_gpa是不存在的
应该为如下：
![[Pasted image 20221103171334.png]]

### String Operations

不同的数据库对于字符串的处理也存在一定差异，具体表现在大小写敏感和单双引号引用上，如下图：
![[Pasted image 20221103171351.png]]

因为MySQL是唯一一个大小写不敏感的，所以对于一些大小写模糊的查询来说会比较方便：
![[Pasted image 20221103171357.png]]

**LIKE**
used for string matching. Here are two operators:
- % Match any substring( including empty strings ).
-  _ Match any one character
![[Pasted image 20221103171409.png]]

SQL-92 defines string functions. While many DBMSs also have their own unique functions.

**String functions can be used anywhere !**
![[Pasted image 20221103171429.png]]

SQL-92 standards says to use '||' operator to concatenate two or more strings together.
![[Pasted image 20221103171438.png]]
注：中间那个是MicroSoft的，下面的是MySQL的。老师还就MySQL的奇怪特性——把中间没有符号的两个字符串自动合并。演示在28:42。

### Date / Time Operations

Can be used anywhere

这边从30:00~37:40，老师在三种不同的数据库（Postgre、MySQL、SQLite）上分别演示了其不同的关于时间的各种基本函数和全局变量。

```SQL
当前时间戳
Postgre:
SELECT NOW();
SELECT CURRENT_TIMESTAMP;

MySQL:
SELECT NOW();
SELECT CURRENT_TIMESTAMP();
SELECT CURRENT_TIMESTAMP;

SQLite:
SELECT CURRENT_TIMESTAMP;

提取天数
Postgre:
SELECT EXTRACT(DAY FROM DATE('2018-08-29'));

MySQL:
SELECT EXTRACT(DAY FROM DATE('2018-08-29'));

SQLite:
//No such function!

日期相减
Postgre:
SELECT DATE('2018-08-29') - DATE('2018-01-01') AS days;
//240, 同样的式子在MySQL里会得到一个错误的结果，在SQLite会得到0。

MySQL:
SELECT ROUND((UNIX_TIMESTAMP(DATE('2018-08-29')) - UNIX_TIMESTAMP(DATE('2018-01-01'))) / (60*60*24), 0) AS days;
SELECT DATEDIFF(DATE('2018-08-29'), DATE('2018-01-01')) AS days;

SQLite:
SELECT julianday(CURRENT_TIMESTAMP) - julianday('2018-01-01'); //展示结果是240.690833333414
SELECT CAST((julianday(CURRENT_TIMESTAMP) - julianday('2018-01-01')) AS INT ) AS days;

```

顺便一提，老师提到这三个数据库中当时最广泛应用的是SQLite

### Output Redirection

将一个查询的结果存放到一个新的表或者已经存在的表中，而不是直接输出在终端或者哪里。

如果是作为一个新的表，不存在什么要求，因为DB已经知道了表结构。但是如果是插入一个已经存在的表，就需要这个表的各个属性和查询结果对齐。

建立新表：
![[Pasted image 20221103171459.png]]
PS：下面这条是MySQL的

存入旧表：
![[Pasted image 20221103171535.png]]
PS：这条是SQL-92，不过感觉MySQL应该有类似的功能支持。
另外老师上课提到，如果这个表存在某些 **约束**（比如某些值的不可重复之类的），这时不同数据库的实现会导致不同的结果。有的数据库可能报错回滚；有的可能不报错，但是丢弃不符合约束的部分，插入没问题的部分；还有的可能就直接crash。

### Output Control

- Order By
	虽然对数据库来说，它不会天然地把数据有序地存储（老师管这个叫bag）。但是在输出时，我们可以将其按一定顺序输出。
	![[Pasted image 20221103171611.png]]
	如果不指定是升序还是降序，默认是升序。
	![[Pasted image 20221103171854.png]]
	![[Pasted image 20221103171936.png]]
	这里提到了一个区分点，就是对于 Group By 和 Order By 来说，前者必须包含所有输出属性当中的非聚合属性，但是后者则不必包含，上图就是一个例子。
	
	PS：Order By 子句也可以接受任意表达式（比如 Order By 1+1）。
- Limit
	简单理解，就是根据需要返回一部分数据
	![[Pasted image 20221103171950.png]]
	![[Pasted image 20221103172021.png]]
	现在我理解API里那些分页操作是怎么来的了emmm。
	顺便一提，如果是做分页，为了查询结果的稳定，需要把 Limit 和 Order By 配合使用。

### Nested Queries

嵌套查询，顾名思义。

这类查询一般很难优化，内部查询可以出现在几乎任何位置
![[Pasted image 20221103172043.png]]

e.g
![[Pasted image 20221103172116.png]]
**这里的处理顺序是：对于外部查询的每一行，检查是否在子查询的结果中存在匹配的结果，如果存在则保留。**

![[Pasted image 20221103174307.png]]
**如果采用如上方式进行查询，则访问顺序发生了改变：对于Enroll表的每一行，通过WHERE子句过滤之后，在学生表中进行一个 match。**

嵌套查询的四个谓词：ALL、ANY、IN、EXISTS

![[Pasted image 20221103173127.png]]




