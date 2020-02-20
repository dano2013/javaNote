**1 Oracle性能调优整体介绍**

**1.1 Oracle性能调优调整层次**

1. 环境调整：对数据为服务器、网络以及磁盘输入输出系统进行调整
2. 数据库服务器调整：oracle数据库服务器调整是进行所有oracle调整的先决条件。如果数据库服务器的cpu不足，那么不管多少oracle调整都无法解决这个问题
3. 网络调整：必须对网络底层进行调整以确保在网络协议下不存在数据包传输问题
4. 磁盘调整：磁盘输入输出瓶颈和磁盘访问问题是oracle调整中的绝对的先决条件
5. 实例调整：包括对SGA内存区和oracle后台处理行为进行调整，对SQL的默认优化器模式进行设置。
6. 对象调整：对实例进行调整后，要对每一个oracle对象进行调整，这一步骤包括对所有的存储参数进行正确的设置。
7. SQL调整：包括定义高频使用的SQL语句，对语句进行调整，以及确保优化的执行计划持久化。

**1.2 性能收益**

设计>SQL>环境>实例

**2 优化器与优化模式的介绍**

**2.1 优化器与优化模式**

**SQL优化器**是一个为所有的SQL语句创建执行计划的工具

**优化模式**是表现优化器的数据库参数

**2.2 基于规则的优化器**

- 不使用任何的表或索引统计信息
- 总是使用索引
- 总是从驱动表开始
- 只有在不可避免的情况下，才使用全表扫描
- 任何索引都可以

**2.3 基于成本的优化器**

Oracle基于成本的优化器的创建目的是为基于规则的优化提供更加复杂的替代方法，需要优化器了解表和索引中数据的细节，包括

- 表数据
- 记录的数目
- 物理数据块的数目
- 索引数据
- 索引中惟一值的数目
- 索引中值的分布
- 索引的可选择性

**2.4 优化器模式**

**Rule模式**：完全基于数据字典信息生成执行计划

**Choose模式**：允许Oracle选择最合适的优化器目标。如果统计数据不存在的话，将会使用rule模块

**First_rows模式**：这是基于成本的优化器模式，将以最快的速度返回记录，但是会造成总体查询速度的下降或是消耗更多的资源

**All_rows模式**：这是一个基于成本的优化器模式，将确保总体查询时时间最短，但是可能在收到第一条记录的操作上花费更长的时间。

**3 SQL调整的理论要点**

**3.1 SQL调整的目标**

- 去掉不必要的大型表的全表扫描
- 缓存小型表的全表扫描
- 检验优化索引的使用
- 检验优化连接技术

**3.2 SQL调整过程**

**定位高频使用的SQL语句**：SQL调整的第一步是定位经常执行的SQL语句。

**调整SQL语句**：对SQL语句进行调整包括生成执行计划，以及通过下列方式对可替代的执行计划进行评估

**添加索引**：可以通过添加索引去掉不必要的全表扫描

**更改优化器模式**：可以尝试将优化器模式做改。

**添加提示**：可以通过添加提示来强行对执行计划进行修改。

**将调整持久化**：在完成了对SQL语句的调整以后，必须通过定位和更改SQL源代码或是使用优化器计划稳定性来使所做的更改持久化。

**3.3 定位高频使用的SQL**

SELECT u.username, s.sql_text, s.executions FROM v$sql s, dba_users u WHERE s.parsing_user_id = u.user_id ORDER BY s.executions DESC;

**3.4 定位占用资源多的SQL**

SELECT b.username,    a.disk_reads ,--磁盘读取    a.buffer_gets,--内存消耗    a.executions ,    a.disk_reads / decode(a.executions, 0, 1,  a.executions) rds_exec_ratio,    a.sql_text  FROM v$sqlarea a, dba_users b WHERE a.parsing_user_id = b.user_id ORDER BY a.disk_reads--,buffer_gets  DESC;

**3.5 定位'低效执行'的SQL**

SELECT EXECUTIONS,DISK_READS,BUFFER_GETS,    ROUND（BUFFER_GETS_DISK_READS）/BUFFER_GETS,2） Hit_radio,     ROUND（DISK_READS/EXECUTIONS, 2） Reads_per_run,    SQL_TEXT  FROM Ⅴ$SSQLAREA  WHERE EXECUTIONS>0 AND BUFFER_GETS >0 AND （BUFFER_GETS_DISK_READS）BUFFER_GETS < 0.8 ORDER BY 4 DESC

**3.6 理解SQL执行**

**解析SQL**: 检查安全性，检查SQL语法，在共享池中查找SQL语句

**捆绑**：捆绑过程将在SQL语句中扫描捆绑变量，然后为每个变量指定数据值

**执行**：包括执行计划的创建和表数据的切实获取

**显示结果集**：对字段数据执行所有必要的排序、转换和重表格式化。

**3.7 表的访问方式**

**全表扫描** ：这种方法读取表中的每一条记录，顺序地读取每一个数据块直到结尾标志。

**散列获取**：这种方法使用符号散列主键来为带有匹配散列值表中的记录创建ROWID

**ROWID访问**：这种访问方式通过指定ROWID的方式在表中选定一个单独的记录。ROWID在数据块中指定记录的块号与偏移量。这是访问记录的最快方式。

**3.8 索引访问方式**

**索引范围扫描**：它是指从索引中读取一个或多个ROWID。索引的数据通常以升序方式进行扫描

**单个索引扫描**：它是指从索引中读取一个单独的ROWID

**降序索引范围扫描**： 它是指从索引中读取一个或多个ROWID。索引的数据通常以降序方式进行扫描

**3.9 用索引提高效率**

索引是表的一个概念部分,用来提高检索数据的效率. 实际上,ORACLE 使用了一个复杂的自平衡B-tree 结构. 通常,通过索引查询数据比全表扫描要快. 当ORACLE 找出执行查询和Update 语句的最佳路径时, ORACLE 优化器将使用索引. 同样在联结多个表时使用索引也可以提高效率. 另一个使用索引的好处是,它提供了主键(primary key)的唯一性验证。

除了那些LONG 或LONG RAW 数据类型, 你可以索引几乎所有的列. 通常, 在大型表中使用索引特别有效. 当然,你也会发现, 在扫描小表时,使用索引同样能提高效率。

虽然使用索引能得到查询效率的提高,但是我们也必须注意到它的代价. 索引需要空间来存储,也需要定期维护, 每当有记录在表中增减或索引列被修改时, 索引本身也会被修改. 这意味着每条记录的INSERT , DELETE , UPDATE 将为此多付出4 , 5 次的磁盘I/O . 因为索引需要额外的存储空间和处理,那些不必要的索引反而会使查询反应时间变慢。

**3.10 多个平等的索引**

当SQL语句的执行路径可以使用分布在多个表上的多个索引时， ORACLE会同时使用多个索引并在运行时对它们的记录进行合并，检索出仅对全部索引有效的记录。

在 ORACLE选择执行路径时，唯一性索引的等级高于非唯一性索引。然而这个规则只有当 WHERE子句中索引列和常量比较才有效。如果索引列和其他表的索引列相比较。这种子句在优化器中的等级是非常低的。

如果不同表中两个相同等级的索引将被引用，FROM子句中表的顺序将决定哪个会被率先使用。FROM子句中最后的表的索引将有最高的优先级。

如果相同表中两个相同等级的索引将被引用， WHERE子句中最先被引用的索引将有最高的优先级。

举例：

DEPTNO上有一个非唯一性索引， EMP CAT也有一个非唯一性索引

SELECT ENAME FROM EMP  WHERE DEPT_NO=20 AND EMP_CAT='A'

这里， DEPTNO索引将被最先检索，然后同EMP_CAT索引检索出的记录进行合并。执行路径如下

TABLE ACCESS BY ROWID ON EMP     AND-EQUAl        INDEX RANGE SCAN ON DEPT_IDX        INDEX RANGE SCAN ON CAT_IDX

**3.11 分离表和索引**

总是将表和索引建立在不同的表空间内(TABLESPACES). 决不要将不属于ORACLE 内部系统的对象存放到SYSTEM表空间里. 同时,确保数据表空间和索引表空间置于不同的硬盘上，主要是提高数据物理读取的并行性，理论上是达到多个磁头并行读取所需数据，并且数据均匀分布在多块磁盘上面。

**3.12 用EXPLAIN PLAN分析SQL语句**

EXPLAIN PLAN 是一个很好的分析SQL 语句的工具,它甚至可以在不执行SQL 的情况下分析语句. 通过分析,我们就可以知道ORACLE 是怎么样连接表,使用什么方式扫描表(索引扫描或全表扫描)以及使用到的索引名称。

按照从里到外,从上到下的顺序解读分析的结果。

EXPLAIN PLAN 分析的结果是用缩进的格式排列的, 最内部的操作将被最先解读, 如果两个操作处于同一层中,带有最小操作号的将被首先执行。

**4 SQL语句性能优化**

在应用系统开发初期，由于开发数据库数据比较少，对于查询SQL 语句，复杂视图的的编写等体会不出SQL 语句各种写法的性能优劣，但是如果将应用系统提交实际应用后，随着数据库中数据的增加，系统的响应速度就成为目前系统需要解决的最主要的问题之一。

对于海量数据，劣质SQL 语句和优质SQL 语句之间的速度差别可以达到上百倍，可见对于一个系统不是简单地能实现其功能就可，而是要写出高质量的SQL 语句，提高系统的可用性。

**4.1 共享SQL语句（绑定变量）**

为了不重复解析相同的SQL 语句,在第一次解析之后, ORACLE 将SQL 语句存放在内存中.这块位于系统全局区域SGA(system global area)的共享池(shared buffer pool)中的内存可以被所有的数据库用户共享. 因此,当你执行一个SQL 语句(有时被称为一个游标)时,如果它和之前的执行过的语句完全相同, ORACLE 就能很快获得已经被解析的语句以及最好的执行路径. ORACLE 的这个功能大大地提高了SQL 的执行性能并节省了内存的使用。

**4.2 满足共享SQL的条件**

向ORACLE 提交一个SQL 语句,ORACLE 会首先在这块内存中查找相同的语句.这里需要注明的是,ORACLE 对两者采取的是一种严格匹配,要达成共享,SQL 语句必须完全相同(包括空格,换行等).共享的语句必须满足三个条件:

当前被执行的语句和共享池中的语句必须完全相同

两个语句所指的对象必须完全相同

两个SQL 语句中必须使用相同的名字的绑定变量(bind variables)

**4.3 选择最有效率的表名顺序**

ORACLE 的解析器按照从右到左的顺序处理FROM 子句中的表名,因此FROM 子句中写在最后的表(基础表 driving table)将被最先处理. 在FROM 子句中包含多个表的情况下,你必须选择记录条数最少的表作为基础表.当ORACLE 处理多个表时, 会运用排序及合并的方式连接它们.首先,扫描第一个表(FROM 子句中最后的那个表)并对记录进行排序,然后扫描第二个表(FROM 子句中最后第二个表),最后将所有从第二个表中检索出的记录与第一个表中合适记录进行合并。

如果有3 个以上的表连接查询, 那就需要选择交叉表(intersection table)作为基础表, 交叉表是指那个被其他表所引用的表。

**4.4** **WHERE子句中的连接顺序**

ORACLE 采用自下而上的顺序解析WHERE 子句,根据这个原理,表之间的连接必须写在其他

 WHERE 条件之前, 那些可以过滤掉最大数量记录的条件必须写在WHERE 子句的末尾。

**4.5** **SELECT子句中避免使用\***

在SELECT 子句中列出所有的COLUMN 时,使用动态SQL 列引用 ‘*’ 是一个方便的方法.不幸的是,这是一个非常低效的方法. 实际上,ORACLE 在解析的过程中, 会将‘*’依次转换成所有的列名, 这个工作是通过查询数据字典完成的, 这意味着将耗费更多的时间。

**4.6** **避免使用HAVING 子句**

避免使用HAVING 子句, HAVING 只会在检索出所有记录之后才对结果集进行过滤. 这个处理需要排序,总计等操作. 如果能通过WHERE 子句限制记录的数目,那就能减少这方面的开销.例如:

低效：

SELECT REGION，AVG(LOG_SIZE) FROM LOCATION GROUP BY REGION HAVING REGION REGION != ‘SYDNEY’ AND REGION != ‘PERTH’

高效:

SELECT REGION，AVG(LOG_SIZE) FROM LOCATION WHERE REGION REGION != ‘SYDNEY’ AND REGION != ‘PERTH’ GROUP BY REGION

HAVING中的条件一般用于对一些集合函数的比较，如count()等等，除此之外，一般的条件应该写在where子句中。

**4.7 减少对表的查询**

在含有子查询的SQL 语句中,要特别注意减少对表的查询.例如：

低效:

 UPDATE EMP  SET EMP_CAT = (SELECT MAX(CATEGORY) FROM EMP_CATEGORIES),  SAL_RANGE = (SELECT MAX(SAL_RANGE) FROM EMP_CATEGORIES)  WHERE EMP_DEPT = 0020;

高效:

 UPDATE EMP  SET (EMP_CAT, SAL_RANGE)  = (SELECT MAX(CATEGORY) , MAX(SAL_RANGE)  FROM EMP_CATEGORIES)  WHERE EMP_DEPT = 0020;

**4.8** **总是使用索引的第一个列**

如果索引是建立在多个列上, 只有在它的第一个列(leading column)被where 子句引用时,优化器才会选择使用该索引。

**4.9** **避免改变索引列的类型**

当比较不同数据类型的数据时, ORACLE 自动对列进行简单的类型转换.假设 EMPNO 是一个数值类型的索引列.

SELECT … FROM EMP WHERE EMPNO = ‘123’

实际上,经过ORACLE 类型转换, 语句转化为:

SELECT … FROM EMP WHERE EMPNO = TO_NUMBER(‘123’)

幸运的是,类型转换没有发生在索引列上,索引的用途没有被改变.现在,假设EMP_TYPE 是一个字符类型的索引列.

SELECT … FROM EMP WHERE EMP_TYPE = 123

这个语句被ORACLE 转换为:

SELECT … FROM EMP WHERE TO_NUMBER(EMP_TYPE)=123

因为内部发生的类型转换, 这个索引将不会被用到！

为了避免Oracle对你的SQL进行隐式的类型转换，做好吧类型转换用显式表现出来；注意当字符和数值比较时，Oracle会优先转换数值类型到字符类型。

**4.10 避免使用耗费资源的操作**

带有DISTINCT,UNION,MINUS,INTERSECT,ORDER BY 的SQL 语句会启动SQL 引擎执行耗费资源的排序(SORT)功能. DISTINCT 需要一次排序操作, 而其他的至少需要执行两次排序.

例如,一个UNION 查询,其中每个查询都带有GROUP BY 子句, GROUP BY 会触发嵌入排序(NESTED SORT) ; 这样, 每个查询需要执行一次排序, 然后在执行UNION 时, 又一个唯一排序(SORT UNIQUE)操作被执行而且它只能在前面的嵌入排序结束后才能开始执行. 嵌入的排序的深度会大大影响查询的效率.

通常, 带有UNION, MINUS , INTERSECT 的SQL 语句都可以用其他方式重写.

**4.11** **删除重复记录**

最高效的删除重复记录方法（因为使用了rowid）

DELETE FROM EMP E WHERE E.ROWID > (    SELECT MIN(X.ROWID)    FORM EMP X    WHERE X.EMP_NO=E.EMP_NO )

**5 SQL编写注意问题**

在某些where 子句中，即使某些列存在索引，但是由于编写了劣质的 SQL，系统在运行该SQL 语句时也不能使用该索引，而同样使用全表扫描，这就造成了响应速度的极大降低。

**5.1** **IS NULL 和 IS NOT NULL**

任何SQL 语句，只要在where 子句中使用了is null 或is not null，那么Oracle 优化器就不允许使用索引了，因为空值不存在于索引列中

**5.2** **连接列**

对于有联接的列，即使最后的联接值为一个静态值，优化器是不会使用索引的。我们一起来看一个例子， 假定有一个职工表（ employee ） ， 对于一个职工的姓和名分成两列存放（FIRST_NAME 和LAST_NAME），现在要查询一个叫比尔.克林顿（Bill Cliton）的职工。下面是一个采用联接查询的SQL 语句，

select * from employss where first_name||''||last_name ='Beill Cliton';

上面这条语句完全可以查询出是否有Bill Cliton 这个员工，但是这里需要注意，系统优化器对基于last_name 创建的索引没有使用。 当采用下面这种SQL 语句的编写，Oracle 系统就可以采用基于last_name 创建的索引。

Select * from employee where first_name ='Beill' and last_name ='Cliton';

**5.3** **避免在索引列上使用计算**

WHERE 子句中，如果索引列是函数的一部分，优化器将不使用索引而使用全表扫描．举例:

低效：

SELECT … FROM DEPT WHERE SAL * 12 > 25000;

高效:

SELECT … FROM DEPT WHERE SAL > 25000/12;

**5.4** **带通配符%的like语句**

要求在职工表中查询名字中包含cliton 的人。可以采用如下的查询SQL 语句：

select * from employee where last_name like '%cliton%';

这里由于通配符（%）在搜寻词首出现，所以Oracle 系统不使用last_name 的索引。在很多情况下可能无法避免这种情况，但是一定要心中有底，通配符如此使用会降低查询速度。然而当通配符出现在字符串其他位置时，优化器就能利用索引。在下面的查询中索引得到了使用：

select * from employee where last_name like 'c%';

**5.5** **避免在索引列上使用NOT**

我们在查询时经常在where 子句使用一些逻辑表达式，如大于、小于、等于以及不等于等等，也可以使用and（与）、or（或）以及not（非）。NOT 可用来对任何逻辑运算符号取反。下面是一个NOT 子句的例子：

... where not (status ='VALID')

如果要使用NOT，则应在取反的短语前面加上括号，并在短语前面加上NOT 运算符。NOT运算符包含在另外一个逻辑运算符中，这就是不等于（<>）运算符。换句话说，即使不在查询where 子句中显式地加入NOT 词，NOT 仍在运算符中，见下例：

... where status <>'INVALID';

再看下面这个例子：

select * from employee where salary<>3000;

对这个查询，可以改写为不使用NOT：

select * from employee where salary<3000 or salary>3000;

虽然这两种查询的结果一样，但是第二种查询方案会比第一种查询方案更快些。第二种查询允许Oracle 对salary 列使用索引，而第一种查询则不能使用索引。

**5.6** **用EXISTS替代IN**

有时候会将一列和一系列值相比较。最简单的办法就是在where 子句中使用子查询。在where 子句中可以使用两种格式的子查询。第一种格式是使用IN 操作符：

... where column in(select * from ... where ...);

 第二种格式是使用EXIST 操作符：

... where exists (select 1 from ...where ...);

相信绝大多数人会使用第一种格式，因为它比较容易编写，而实际上第二种格式要远比第一种格式的效率高。在Oracle 中可以几乎将所有的IN 操作符子查询改写为使用EXISTS 的子查询。第二种格式中，子查询以‘select 1'开始。运用EXISTS 子句不管子查询从表中抽取什么数据它只查看where 子句。这样优化器就不必遍历整个表而仅根据索引就可完成工作（这里假定在where 语句中使用的列存在索引）。相对于IN 子句来说，EXISTS 使用相连子查询，构造起来要比IN 子查询困难一些。通过使用EXIST，Oracle 会首先检查主查询，然后运行子查询直到它找到第一个匹配项，这就节省了时间。Oracle 在执行IN 子句时，系统先将主查询挂起，然后执行子查询，并将获得的结果列表存放在在一个加了索引的临时表中，最后执行主查询。这也就是使用EXISTS 比使用IN 通常查询速度快的原因。同时应尽可能使用NOT EXISTS 来代替NOT IN，尽管二者都使用了NOT（不能使用索引而降低速度），NOT EXISTS 还是要比NOT IN 查询效率更高。

**5.7** **用表连接替代EXISTS**

通常来说，采用表连接的方式比exists更有效率

SELECT ENAME  FROM EMP E  WHERE EXISTS （     SELECT 'X'    FROM DEPT     WHERE DEPT_NO= E.DEPT_NO     AND DEPT_CAT='A ）

SELECT ENAME  FROM DEPT D, EMPE  WHERE E.DEPT_NO= D.DEPT_NO  AND DEPT CAT='A'

在RBO的情况下，前者的执行路径包括FILTER，后者使用NESTED LOOP

**5.8** **用>=替代>**

如果DEPTNO 上有一个索引,

高效:

SELECT * FROM EMP WHERE DEPTNO >=4

低效:

SELECT * FROM EMP WHERE DEPTNO >3

两者的区别在于, 前者DBMS 将直接跳到第一个DEPT 等于4 的记录，

而后者将首先定位到DEPTNO=3 的记录并且向前扫描到第一个DEPT 大于3 的记录。

**5.9** **用IN替代OR**

下面的查询可以被更有效率的语句替换:

低效:

SELECT… FROM LOCATION WHERE LOC_ID = 10 OR LOC_ID = 20 OR LOC_ID = 30;

高效:

SELECT… FROM LOCATION WHERE LOC_IN IN (10,20,30);

**5.10** **用UNION替代OR**

通常情况下, 用UNION 替换WHERE 子句中的OR 将会起到较好的效果. 对索引列使用OR将造成全表扫描. 注意, 以上规则只针对多个索引列有效. 如果有column 没有被索引, 查询效率可能会因为你没有选择OR 而降低.在下面的例子中, LOC_ID 和REGION 上都建有索引.

高效:

SELECT LOC_ID , LOC_DESC , REGION FROM LOCATION WHERE LOC_ID = 10 UNION SELECT LOC_ID , LOC_DESC , REGION FROM LOCATION WHERE REGION = “MELBOURNE”

低效:

SELECT LOC_ID , LOC_DESC , REGION FROM LOCATION WHERE LOC_ID = 10 OR REGION = “MELBOURNE”

如果你坚持要用OR, 那就需要返回记录最少的索引列写在最前面.

WHERE KEY1 = 10 (返回最少记录) OR KEY2 = 20 (返回最多记录) --内部将以上转换为 WHERE KEY1 = 10 AND ((NOT KEY1 = 10) AND KEY2 = 20)

**5.11** **用UNION ALL替代UNION**

当SQL 语句需要UNION 两个查询结果集合时,这两个结果集合会以UNION ALL 的方式被合并, 然后在输出最终结果前进行排序。

如果用UNION ALL 替代UNION, 这样排序就不是必要了，效率就会因此得到提高。

**5.12** **order by语句**

ORDER BY 语句决定了Oracle 如何将返回查询结果排序。任何在Order by 语句的非索引项或者有计算表达式都将降低查询速度。解决这个问题的办法就是重写order by 语句以使用索引，同时应绝对避免在order by 子句中使用表达式。

**5.13 使用提示（Hints）**

对于改变SQL书写都没法得到理想的执行计划，可以对SQL加上提示。

因提示会改变执行计划，在CBO的优化器下业务系统语句中慎用。