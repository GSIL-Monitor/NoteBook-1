# Mysql

## Mysql常见命令

| 功能                           | 命令                                    |
| ------------------------------ | --------------------------------------- |
| 查询Mysql当前连接数            | show status like 'Threads%'             |
| 查询数据库当前设置的最大连接数 | show variables like '%max_connections%' |

----

## 创建和操纵表

### 删除表

```mysql
DROP TABLE tablename;
```

## 存储过程

存储过程就是为以后使用而保存的一条或多条sql语句

## Mysql分区

**目的：** 在特定的SQL操作中减少数据读写的总量以缩减sql语句的响应时

### 分区类型

#### 水平分区 

- RANGE：基于属于一个给定连续区间的列值，把多行分配给分区
- LIST：基于列值匹配一个离散值集合中的某个值来进行选择
- HASH/LINEAR HASH：基于用户定义的表达式的返回值来选择分区，改表达式使用将要插入到表中的这些行的列值进行计算。这个函数可以包含Mysql中有效的、产生非负整数值的任何表达式(表达式返回值必须是整数)*
- KEY：类似于按HASH分区，区别在于KEY分区只支持计算一列或多列，且MySQL服务器提供其自身的哈希函数。必须有一列或多列包含整数值
- Composite（复合分区）：以上四种分区的组合

#### 垂直分区

### 分区实例

**查看数据库是否支持分区：** （返回yes表示支持）

```mysql
SHOW VARIABLES LIKE "%partition%"
```

**建立分区: **

- RANGE分区

  ```mysql
  create table user(
  id int primary key auto_increment,
  username varchar(20) ,
  age int
  )
  partition by range(id)
  (
  partition p1 values less than(100),
  partition p2 values less than(200),
  partition p3 values less maxvalue
  );
  ```

- LIST分区

  ```mysql
  create table user(
  id int primary key auto_increment,
  username varchar(20) ,
  age int,
  sex int,   -- 0：女 1：男 2：保密 3：雌雄同体
  primary key(id,sex)
  )
  partition by list(sex)
  (
  partition p1 values in (0),
  partition p2 values in (1),
  partition p3 values in (2,3)
  )
  ```

- HASH分区（放到哪个分区中由数据库决定，可以理解为通过计算hash值，将数据平分的放到几个分区中）

  ```mysql
  create table user(
  id int primary key auto_increment,
  username varchar(20) ,
  age int,
  sex int,   -- 0：女 1：男 2：保密
  primary key (id,sex)
  )
  partition by hash(sex)
  partitions 3;
  ```

- KEY分区

  ```mysql
  create table user(
  id int primary key auto_increment,
  username varchar(20) ,
  age int,
  sex int,   -- 0：女 1：男 2：保密
  primary key(id,sex)
  )
  partition by key(sex)
  partitions 3;
  ```

- Composite（复合分区）

  复合分区就是在已经建立的分区上再建立分区，类似于HTML中的树结构

  *（MySQL允许在range和list的分区上再进行hash和key的子分区）*

  **注意：**

  1. 如果一个分区中创建了子分区，其他分区也要有子分区
  2. 如果创建了了分区，每个分区中的子分区数必有相同
  3. 同一分区内的子分区，名字不相同，不同分区内的子分区名子可以相同

  ```mysql
  create table user(
  id int  auto_increment,
  username varchar(20) ,
  age int,
  sex int,   -- 0：女 1：男 2：保密
  primary key(id,sex)
  )
  partition by range(id)
  subpartition by hash(sex)
  subpartitions 3
  (
  partition p1 value less than (200),
  partition p2 value less than maxvalue
  );
  ```

  以上，建立2和range分区，然后再range分区的基础上建立了3个hash分区，分区的数量为：
  $$
  2*3=6
  $$









**删除分区：**

*(不能删除HASH或者KEY分区)*

*注意：*在删除分区时，会将该分区下的数据同时删除

```mysql
alter table user drop partition p1; 
alter table user drop partition p1,p2;
```

**增加分区：** 

*（与建表时增加分区一样）*

```mysql
alter table user add partition (partition p4 values less than (300));
alter table user add partition (partition p4 value in (3));
```

**分解分区：** 

*（分区分解不会丢失数据）*

```mysql
alter table user reorganize partition p1 into
(
partition p1 values less than (50),
partition p4 values less than (100)
)
```

**合并分区：**

```mysql
alter table user reorganize partition p1,p4 into(partition p1 values less than (100));
```

**删除所有分区：**

```mysql
alter table user remove partitioing;
```

***剩余的还有：重建、优化、分析、检查分区。***

### 增删改查

分区对数据库的使用来说是透明的，因此有分区的表的增删改查与无分区的表的增删改查方式一致

### 自动分区

Mysql不能自动创建分区，需要使用Mysql event事件的方式自动创建分区

首先定义一个存储过程，然后在一定的条件下执行存储过程，能实现自动创建分区

1. 创建表

   ```mysql
   CREATE TABLE `test1` (
     `id` char(32) COLLATE utf8mb4_unicode_ci NOT NULL COMMENT '自增主键(guid)',
     `create_time` timestamp NOT NULL DEFAULT CURRENT_TIMESTAMP COMMENT '创建时间',
     `partition_key` int(8) NOT NULL COMMENT '分区键(格式:yyyyMMdd)',
     PRIMARY KEY (`id`,`partition_key`),
     UNIQUE KEY `id_UNIQUE` (`id`,`partition_key`)
   ) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_unicode_ci
   PARTITION BY RANGE (partition_key)
   (PARTITION p0 VALUES LESS THAN (20180619) ENGINE = InnoDB,
    PARTITION p20180619 VALUES LESS THAN (20180620) ENGINE = InnoDB,
    PARTITION p20180621 VALUES LESS THAN (20180622) ENGINE = InnoDB,
    PARTITION p20180622 VALUES LESS THAN (20180623) ENGINE = InnoDB,
    PARTITION p20180623 VALUES LESS THAN (20180624) ENGINE = InnoDB);　
   ```

2. 创建分区的存储过程

   ```mysql
   DELIMITER $$           #将';'临时替换为'$$'
   USE 'demo' $$          #demo为表存在的数据库名
   DROP PROCEDURE IF EXISTS 'creat_partition_by_day' $$   #creat_partition_by_day为存储过程
   CREATE PROCEDURE 'creat_partition_by_day' (IN_SCHEMANAME VARDHAR(64), IN_TABLENAME VARCHAR(64))                             #IN_SCHEMANAME和IN_TABLENAME为输入参数
   BEGIN
   	#当前日期存在的分区的个数
   	DECLARE ROWS_CNT INT UNSIGNED;  #DECLARE在复合语句中声明变量，UNSIGNED禁用负数
   	#目前日期，为当前日期的后一天
   	DECLARE TARGET_DATE TIMESTAMP;  #TIMESTAMP时间戳，唯一的表示某一时刻的时间
   	#分区的名称
   	DECLARE PARTITIONNAME VARCHAR(9);
       #当前分区名称的分区值上限，即为 PARTITIONNAME + 1
       DECLARE PARTITION_ADD_DAY VARCHAR(9);
       SET TARGET_DATE = NOW() + INTERVAL 1 DAY;     #SET用于赋值操作
       SET PARTITIONNAME = DATE_FORMAT( TARGET_DATE, 'p%Y%m%d' );
       SET TARGET_DATE = TARGET_DATE + INTERVAL 1 DAY;
       SET PARTITION_ADD_DAY = DATE_FORMAT( TARGET_DATE, '%Y%m%d' );
       SELECT COUNT(*) INTO ROWS_CNT FROM information_schema.partitions
       WHERE table_schema = IN_SCHEMANAME AND table_name = IN_TABLENAME AND 			partition_name = PARTITIONNAME;
       IF ROWS_CNT = 0 THEN
           SET @SQL = CONCAT( 'ALTER TABLE `', IN_SCHEMANAME, '`.`', IN_TABLENAME, '`',
           ' ADD PARTITION (PARTITION ', PARTITIONNAME, " VALUES LESS THAN (",
               PARTITION_ADD_DAY ,") ENGINE = InnoDB);" );
           PREPARE STMT FROM @SQL;
           EXECUTE STMT;
           DEALLOCATE PREPARE STMT;
        ELSE
          SELECT CONCAT("partition `", PARTITIONNAME, "` for table `",IN_SCHEMANAME, ".", IN_TABLENAME, "` already exists") AS result;
        END IF;
   END$$
   DELIMITER ;
   ```

3. 数据库执行定时任务（每小时执行一次）

   ```mysql
   DELIMITER $$
   #该表所在的数据库名称
   USE `demo`$$
   CREATE EVENT IF NOT EXISTS `daily_generate_partition`
   ON SCHEDULE EVERY 1 hour   #执行周期，还有天、月等等
   STARTS '2018-06-20 00:00:00'
   ON COMPLETION PRESERVE
   ENABLE
   COMMENT 'Creating partitions'
   DO BEGIN
       #调用刚才创建的存储过程，第一个参数是数据库名称，第二个参数是表名称
       CALL datacollectcenter.create_partition_by_day('demo','test1');
   END$$
   DELIMITER ;
   ```

**根据时间进行自动分区的实例**

> https://blog.csdn.net/Guns_NRoses/article/details/53083053

## mysql索引