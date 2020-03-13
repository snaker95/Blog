### Mysql通过字段field查到所在table

[doc]

```sql
select * from information_schema.COLUMNS where COLUMN_NAME='labels_desc';
```

### 

### Information_schema 

Information_schema 是MySQL系统自带的数据库, 提供了数据库元数据的访问方式; 记录了数据库当中大部分的信息: 字符集, 权限相关, 数据库实体对象信息, 外键约束, 分区, 压缩表, 表信息, 索引信息, 参数, 优化, 锁 和事务...

#### 字符集和排序规则

* CHARACTER_SETS: 存储数据库相关字符集信息
* COLLATIONS: 字符集对应的排序规则
* COLLATION_CHARACTER_SET_APPLICABILITY: 字符集和连线校对的对应关系

1. CHARACTER_SETS 和 COLLATIONS 的区别:
   * 字符集CHARACTER_SETS 存储字符串, 是人类语言中最小的表义符号. 比如 'A', 'B'
   * 排序规则COLLATIONS 规则比较字符串, 在同一字符集内字符之间的比较规则
   * 每个字符序唯一对应一种字符集, 但一个字符集可以对应多种字符序, 其中一个就是默认字符序(Default Collation)
   * MySQL字符序名称遵从命名惯例:
     * 以字符序对应的字符集名称开头
     * 以_ci 或 _cs 或 _bin 结尾 **(ci: 忽略大小写; cs: 大小写敏感; bin:按编码值比较)**
2. 字符集和校对相关的MySQL变量:
   * Character_set_server:
   * Character_set_client:
   * Character_set_connection:
   * Character_set_results
   * Character_set_database
   * character_set_system
3. MySQL字符集转换过程:
   * MySQL Server收到请求时将请求数据从 character_set_client转换为character_set_connection;
   * 进行内部操作前将请求数据从 character_set_connection转换为内部操作字符集:
     * 使用每个字段的character_set的设定值
     * 否则, 使用对应数据表的Default Character Set设定值
     * 否则, 使用对应数据库的Default Character Set设定值
     * 否则, 使用Character_set_server设定值
   * 将操作结果从内部操作字符集转换为character_set_results.

#### 权限相关表

* SCHEMA_PRIVILEGES: 提供数据库的相关权限
* TABLE_PRIVILEGES: 提供数据表的相关权限
* COLUMN_PRIVILEGES: 这个表可以清楚就能看到表授权的用户的对象，那张表那个库以及授予的是什么权限，如果授权的时候加上with grant option的话，我们可以看得到PRIVILEGE_TYPE这个值必须是YES。
* USER_PRIVILEGES: 提供的是表权限相关信息，信息是从 mysql.user 表中加载的



#### 数据库的实体对象相关表

* COLUMNS: 存储表的字段信息，所有的存储引擎
* INNODB_SYS_COLUMNS ：存放的是INNODB的元数据， 他是依赖于SYS_COLUMNS这个统计表而存在的。
* ENGINES ：引擎类型，是否支持这个引擎，描述，是否支持事物，是否支持分布式事务，是否能够支持事物的回滚点
* EVENTS ：记录MySQL中的事件，类似于定时作业
* FILES ：这张表提供了有关在MySQL的表空间中的数据存储的文件的信息，文件存储的位置，这个表的数据是从InnoDB in-memory中拉取出来的，所以说这张表本身也是一个内存表，每次重启重新进行拉取。也就是我们下面要说的INNODB_SYS_DATAFILES这张表。还要注意一点的是这张表包含有临时表的信息，所以说和SYS_DATAFILES 这张表是不能够对等的，还是要从INNODB_SYS_DATAFILES看。如果undo表空间也配置是InnoDB 的话，那么也是会被记录下来的。
* PARAMETERS ：参数表存储了一些存储过程和方法的参数，以及存储过程的返回值信息。存储和方法在ROUTINES里面存储。
* PLUGINS ：基本上是MySQL的插件信息，是否是活动状态等信息。其实SHOW PLUGINS本身就是通过这张表来拉取道德数据
* ROUTINES：关于存储过程和方法function的一些信息，不过这个信息是不包括用户自定义的，只是系统的一些信息。
* SCHEMATA：这个表提供了实例下有多少个数据库，而且还有数据库默认的字符集
* TRIGGERS :这个表记录的就是触发器的信息，包括所有的相关的信息。系统的和自己用户创建的触发器。
* VIEWS :视图的信息，也是系统的和用户的基本视图信息。





参考: [十分钟了结MySQL information_schema](https://www.cnblogs.com/shengdimaya/p/6920677.html)

