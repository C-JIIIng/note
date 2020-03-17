# Hive

+ Hive是**基于Hadoop的一个数据仓库工具**，**可以将结构化的数据文件映射为一张数据库表**，并提供类SQL查询功能。
+ Hive是SQL解析引擎，它将SQL语句转译成M/R Job然后在Hadoop执行。

与数据库的比较

+ hive采用了类sql的查询语言HQL
+ Hive建立在hadoop之上，所有hive的数据都是存在hdfs之中，sql数据是保存在块设备或本地文件系统中
+ hive是针对数据仓库应用设计的，而数据仓库的内容是读多写少的，不支持对数据的改写，但sql是需要经常进行修改的
+ hive在加载数据的过程中，不会对数据进行任何处理，因此也不会建立索引，sql中通常会针对一个或多个列建立索引。
+ hive的查询延迟较高，sql延迟较低
+ hive底层的查询是通过mr来实现的，sql有自己的执行引擎

## 1. 系统架构

![image-20200310151543763](D:\Desktop\面试\大数据\img\image-20200310151543763.png)

+ 用户接口，包括 CLI，JDBC/ODBC，WebUI
+ 元数据存储，通常是存储在关系数据库如 mysql, derby 中
  + Metastore 默认存储在自带的 derby  数据库中，会导致只能开启一个hive客户端，所以推荐使用MySQL存储 Metastore。
  + 元数据包含：数据库、表、分区、分桶等信息
+ **解释器、编译器、优化器、执行器**
+ Hadoop：用 HDFS 进行存储，利用 MapReduce 进行计算

## 2. 数据模型

![image-20200310230852273](D:\Desktop\面试\大数据\img\image-20200310230852273.png)

+ 数据库（database）
  + 数据库是一个包含若干表的命名空间和磁盘目录，类似于关系型数据库中的数据库
  + 系统会为每个数据库创建一个目录，目录名=数据库名.db
  + 通常无法删除包含表的数据库，应该先删除表，再删除数据库。
+ 表（table）
  + 表是数据管理和存储的基本对象，包含若干字段，类似于关系型数据库中的表
  + 表的元数据存在Metasore中，表的时机数据存储在HDFS中
  + 按所有权分类
    + 管理表（又称内表）
      + 删除内表时，会同时删除表数据，以及Metastore中的元数据
    + 外部表（又称外表）
      + 删除外表时，不会删除表数据，但会删除Metastore中的元数据
  + 按存储格式分类
    + Text 表
      + 系统默认的表类型，无压缩，行存储，仅支持批量insert
      + 分析查询性能低
    + ORC表
      + 优化的列存储表，轻量级索引，压缩比高，仅支持批量insert
      + 分析查询性能高
+ ==分区（partition）==
  + 目的：减少不必要的全表扫描，缩小查询范围，提高查询效率。
  + 含义：==**将表按照某个或某几个字段（分区键）划分为更小的数据集**==
  + 分区数据存储于表目录下的子目录中，一个分区对应一个子目录，目录名为 “分区键=value”
  + 如何选择分区键
    + 分区键通常==**高频出现在select...where条件查询中**==
    + 为了避免产生过多的子目录和小文件，==**只对离散字段进行分区**==，如日期，地域，民族等
    + 由于hdfs不支持大量的子目录，有必要**对分区数量进行估计**
    + 预估分区的数据量，**避免数据倾斜**

+ 分桶（Bucket）
  + 含义：通过分桶键哈希取模（key hashcode % n）的方式，**将表或分区中的数据随机、均匀地分发到n个桶中**，桶数n一般为质数，桶编号为0,1...,n-1
  + 作用
    + 数据划分：均匀随机
    + 数据聚合：key相同的数据在同一个桶中
  + 存储
    + 在表或分区目录下，每个桶存储为一个文件
    + 桶文件的大小应控制在100-200MB之间（orc表压缩后）
    + 如果桶文件小于HDFS Block（128M），那么一个桶对应一个Block，否会存储多个Block
  + 作用
    + ==**提高 Join 查询效率**==：如果两个表的 Join 列都做了分桶，且分桶数相同或成倍数，那么相同列值的数据会被分到编号相同或有对应关系的桶中，这样就不用全表遍历，对应的桶做Join就可以了
  + 用法
    + 通常先分区再分桶
    + 分桶键和分桶数在建表时确定，不允许更改，否则必须重新分桶
    + 分桶键必须出现在表结构中



## 3. 安装与配置

### 3.1 常见交互指令

+  -e <quoted-query-string>         SQL from command line

```shell
bin/hive -e "select * from student;"
```

![image-20200310215527611](D:\Desktop\面试\大数据\img\image-20200310215527611.png)

+ -f <filename>                    SQL from files

```shell
bin/hive -f ./hive.hql 
```

![image-20200310215743805](D:\Desktop\面试\大数据\img\image-20200310215743805.png)

### 3.2 常见配置信息

#### 3.2.1 Hive 数据仓库位置的配置

+ 默认地址是在hdfs上：/user/hive/warehouse





## 4. 数据类型

## 5. DDL 数据定义

### 5.1 数据库操作

> Create/Drop/Alter/Use/Describe/Show Databases

#### 创建数据库

```sql
create database [if not exists] <database name>;
```

#### 删除数据库（先删表后删库）

```sql
drop database if exists <database name>;
```

#### 修改数据库属性

```sql
alter database <database name> set dbproperties('<property_name>'='<property_ value>',...);
```

#### 列出所有数据库

```sql
show databases;
```

#### 切换数据库、查看数据库详情

```sql
use <database name>;
desc database <database name>;
```

### 5.2 表的基本操作

> Create/Drop/Alter/Truncate/Describe/Show Table(s)

#### 完整建表语句

```sql
CREATE [EXTERNAL] TABLE [IF NOT EXISTS] [db_name.] table_name
[(col_name data_type [COMMENT col_comment], ...)]
[COMMENT table_comment]
-- 分区
[PARTITIONED BY (<partition_key> <data_type> [COMMENT '<partition_comment >'], <partition_key> <data_type>...])]
-- 分桶
[CLUSTERED BY (<col_name> [, <col_name>...])
	[SORTED BY (<col_name> [ASC|DESC]，...)] INTO <num_buckets> BUCKETS
-- 行分割	
[ROW FORMAT row_format]
[STORED AS file_format]
-- 指定表在hdfs上的存储位置
[LOCATION '<file_path>'];
```

#### 创建内表

```sql
create table <table_name> (<col_name><data_type>,...);
```

#### 创建外表

+ 创建外表时，通常要指定数据存储位置
+ 删除外表时，只删除元数据，表数据保留

```sql
create external table <table_name> 
(<col_name><data_type>,...)
location '<file_path>';
```

#### 删除表

```sql
drop table <table_name>;
```

#### 修改表

```sql
-- 表重命名
ALTER TABLE <table_name> RENAME TO <new_table_name

-- 修改表属性
ALTER TABLE <table_name> SET TBLPROPERTIES ('<property_name>' = '<property_value>' ...);
ALTER TABLE <table_name> SET SERDEPROPERTIES ('<property_name>' = '<property_value>' ...);
ALTER TABLE <table_name> SET LOCATION '<new_location>';

-- 增加、删除、修改、替换 列
ALTER TABLE <table_ ADD COLUMNS (<col_spec> [, <col_spec>..
ALTERTABLE <table_ DROP [COLUMN] <col_
ALTER TABLE <table_ CHANGE <col_name> <new_col_name> <new_col_
ALTER TABLE <table_ REPLACE COLUMNS (<col_spec> [, <col_spec>
```

#### 列出当前数据库的所有表

```sql
show tables;
```

### 5.3 表的高级操作

#### 1. 分区表

##### 单值分区

+ 创建单值分区时，分区键不能和表结构中的列重复，因为分区键已存储在分区目录名中，分区数据中不包含分区键，否则会造成数据冗余。
+ **只写元数据，不创建分区目录**（数据导入时才会创建分区目录）

```sql
CREATE [EXTERNAL] TABLE <table_name>
(<col_name> <data_type> [, <col_name> <data_type>
PARTITIONED BY partition_key > <data_type>, ...)
[CLUSTERED BY ...]
[ROW FORMAT <row_format>]
[STORED AS TEXTFILE|ORC|CSVFILE]
[LOCATION '<file_path>']
[TBLPROPERTIES ('<property_name>'='<property_value>', ...)];
```

+ 单值分区可以分为两类
  + 单值静态分区：导入数据时，必须手动指定目标分区
  + 单值动态分区：导入数据时，系统可以动态判断目标分区
+ 写入静态分区

```sql
-- 将 HDFS 数据或本地文件移动到分区目录下
LOAD DATA [LOCAL] INPATH '<path>' [OVERWRITE] INTO TABLE <tablename>
PARTITION (<partition_key>=<partition_value>[, <partition_key>=<partition_value>,...]);
```

+ 写入动态分区

```sql
-- 开启动态分区支持，并设置 最大分区数
set hive.exec.dynamic.partition=true;
set hive.exec.max.dynamic.partitions=2000
-- <dpk>为动态分区键, <spk>为静态分区键
INSERT (OVERWRITE|INTO) TABLE <table_name>
PARTITION ([([<spk>=< value>, ..., ] <dpk >>, [..., <dpk>])
SELECT <select_statement>;
```

+ 添加、删除、重命名、清空分区

```sql
ALTER TABLE <table_name> ADD PARTITION (<partition_key>=<value>);
ALTER TABLE <table_name> DROP PARTITION (<partition_key>=<value>);
ALTER TABLE <table_name> PARTITION (<partition_key>=<value>) RENAME TO PARTITION(<partition_key>=<new_value>);
TRUNCATE TABLE <table_name> PARTITION ((<partition_key>=<value>);
```

##### 范围分区



## 6. DML 数据操作

### 6.1 数据导入

#### 6.1.1 Load 导入

将文件导入表或分区

+ 将文件中的数据导入已存在的表或分区中
+ 仅将数据文件移动到表或分区的目录中（如果都在hdfs中，只是修改元数据），不会对数据进行任何处理。
+ 不推荐使用Load导入数据
+ 推荐的数据导入方法：创建外表，并将外表location设置为数据文件所在的目录。
  + 这样的方式，会导致数据库下没有表
  + 不知道inceptor上有没有这个问题

```sql
-- 这里不加 local 会将 hdfs 的数据导入到表中
-- 当时这里实际上并没有做数据的复制，只是修改了 元数据，所以原来存在另一个文件夹下的数据就会显示消失
load data inpath '/haitong_nj' into table haitong.t_ods_user_events_ex;

-- 会将数据拷贝一份到hdfs中
load data local inpath '/root/haitong/haitong.cvs' into table haitong.t_ods_user_events_ex;
```

#### 6.1.2 Insert 导入

将查询结果导入表或分区

```sql
insert (overwrite | into) table <table_name>
select <select_statement>;
```

### 6.2 数据导出

#### insert 导出

将查询结果导出到本地或hdfs

```sql
insert overwrite [local] directory <directory>
select<select_statement>
row format delimated fields terminated '/t';
```



## 7. 查询

**排序**

+ order by (全局排序)
  + 功能：将所有数据交给一个 Reduce任务计算，实现查询结果的全局排序
  + 缺点：如果数据量很大，只要一个reduce会耗费大量时间

+ sort by（局部排序）
  + 功能：在每个reduce任务中对数据进行排序，即局部排序
  + 当启动多个reduce任务时，order by 输出一个文件且全局有序，sort by 输出多个文件且局部有序

**分桶与聚合**

+ distribute by
  + 通过哈希取模的方式，实现数据按 distribute by列值分桶
  + 将distribute by 列值相同的数据发送给同一个reduce任务，实现数据按指定列聚合
  + 通常和sort by 合并使用，实现先聚合后排序，且 distribute by 必须在 sort by 之前
+ cluster by
  + 如果 distribute by列 和 sort by 列完全相同时，且按程序排序，那么cluster by = distribute by... sort by







## 8.函数

### 1. NVL函数

+ 空值转换函数
+ 语法：`NVL(expr1, expr2)`
+ 如果expr1为NULL，返回值为 expr2，否则返回expr1。
+ 适用于数字型、字符型和日期型，但是 expr1和expr2的数据类型必须为同类型。

### 2. 时间类

#### 1）from_unixtime

+ 日期函数UNIX时间戳转日期函数
+ 语法： `from_unixtime(bigint unixtime[, String format])`
+ 返回值：**String**
+ 说明：转化UNIX时间戳（从 1970-01-01 00:00:00 UTC到执行时间的秒数）到当前时区的时间格式
+ **可以识别到 月 一级的时间**

```sql
hive (default)> select from_unixtime(1234532342,'yyyy-MM-dd HH:mm:ss');
```

> 2009-02-13 21:39:02

```sql
hive (default)> select from_unixtime(1234532342,'yyyy-MM-dd');
```

> 2009-02-13

```sql
hive (default)> select from_unixtime(1234532342,'yyyyMMdd');
```

> 20090213

```sql
hive (default)>select from_unixtime(1234532342,'yyyy-MM');
```

> 2009-02

#### 2）unix_timestamp

+ **这个是可以识别到** **月** **的时间格式一级的。可以识别到 月 一级的时间**
+ 获取当前Unix时间戳函数
+ 语法：`unix_timestamp()`
+ 返回值：bigint
+ 说明：获取当前时区的时间戳

```sql
hive>	select unix_timestamp();
```

> 1584023965

+ 日期转为unix时间戳函数
+ 语法：`unix_timestamp(String date)`
+ 返回值：bigint
+ 转换格式为“yyyy-MM-dd HH:mm:ss“的日期到UNIX时间戳。如果转化失败，则返回0。

```sql
hive>   select unix_timestamp('2011-12-07 13:01:03');
1323234063
```

+ 指定格式日期转UNIX时间戳函数
+ 语法：`unix_timestamp(string date, string pattern)`

+ 返回值：bigint
+ 转换pattern格式的日期到UNIX时间戳。如果转化失败，则返回0

```sql
hive>   select unix_timestamp('20111207 13:01:03','yyyyMMdd HH:mm:ss');
1323234063
hive> select unix_timestamp('2011-12-07 13:05','yyyy-MM-dd HH:mm');
1323234300
hive> select unix_timestamp('2011-12','yyyy-MM');
1322668800
```

#### 3) date_format

+ 语法：` date_format(date/timestamp/string ts, string fmt)`
+ **可以识别到** **月** **和** **年** **一级的时间**

```sql
hive> select date_format('2015-04-08', 'y');
2015
hive> select date_format('2015-04-08', 'yyyy');
2015
hive> select date_format('2015-04-08', 'yyyy-MM');
2015-04
hive> select date_format('2015-04-08 10:10:01',	 'yyyy-MM');
2015-04
hive> select date_format('2015-04-08', 'yyyy-MM-dd');
2015-04-08
```

#### 4) to_date

+ 日期时间转日期函数: to_date
+ 语法:  `to_date(string timestamp)`

+ 返回值:  string
+ 说明: 返回日期时间字段中的日期部分。

```sql
hive>   select to_date('2011-12-08 10:03:01');
2011-12-08
hive> select to_date('2011-12-08');
2011-12-08
hive> select to_date('2011-12');
NULL
```

### 3. case when 

### 4. 行转列

+ concat(string str1, string str2...)
+ concat_ws(string separator, string str1, string str2...)
  + 如果分割符时null，返回也是null
  + 会调后分割符后所有的null和空字符串
+ collect_set(col)：函数只接受基本数据类型，主要作用是，将某个字段的值去重汇总，返回array类型字段

```sql
select concat(person_info.constellation,',',person_info.blood_type) as constellation_blood_type,person_info.name
from person_info t1

select t1.constellation_blood_type, concat_ws('|',collect_set(name))
from (
    select concat(person_info.constellation,',',person_info.blood_type) as constellation_blood_type,person_info.name 
    from person_info) t1
group by constellation_blood_type;
```



### 5. 列转行

explode(col)：将hive 一列中 复杂的 array 或者 map 结构拆分为多行

lateral view explode(col) table_tmp as aliasname

```sql
select explode(movie.category) from movie;

select title,category_type
from movie lateral view explode(movie.category) tmp as category_type
```



### 6.窗口函数





## 9 文件存储格式

### 9.1 列式存储和行式存储

![image-20200311173813212](D:\Desktop\面试\大数据\img\image-20200311173813212.png)

+ 行存储：查询满足条件的一整行数据的时候，列存储则需要去每个聚集的字段找到对应的每个列的值，行存储只需要找到其中一个值，其余的值都在相邻地方，所以此时行存储查询的速度更快。
+ 列存储：因为每个字段的数据聚集存储，在查询只需要少数几个字段的时候，能大大减少读取的数据量；每个字段的数据类型一定是相同的，列式存储可以针对性的设计更好的设计压缩算法。

### 9.2 Hive 中常用的存储格式

+ textfile

  + 默认格式，存储方式为行存储

+ ORCfile

  + hive/spark都支持这种存储格式
  + 它存储的方式时采用数据按照行分块，每个块按照列存储，其中每个块都存储有一个索引。
  + 数据压缩率很高

  ![image-20200311174516628](D:\Desktop\面试\大数据\img\image-20200311174516628.png)

+ Parquet

  + Parquet也是一种行式存储，同时具有很好的压缩性能；同时可以减少大量的表扫描和反序列化的时间。





















