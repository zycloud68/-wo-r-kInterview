# MyBatis面试题

## MyBatis介绍

#### 一.  什么是MyBatis

1. Mybatis 是一个半 ORM（对象关系映射）框架，它内部封装了 JDBC，开发时
   只需要关注 SQL 语句本身，直接编写原生态 sql，严格控制 sql 执行性
   能，灵活度高。

2. Mybatis可以使用XML或注解配置和映射原生信息,将POJO映射成数据库的记录。

| 编号 | 优点                                                   | 缺点                                                   |
| :--- | ------------------------------------------------------ | ------------------------------------------------------ |
| 1    | SQL语句编写灵活,支持编写动态SQL语句,并可重写           | Sql语句的编写工程较大,字段多,关联表多                  |
| 2    | 消除了JDBC大量冗余代码,对Spring支持有好                | 对开发人员编写sql语句的功底有一定的要求                |
| 3    | 提供映射标签,提供对象关系映射标签,支持对象关系组件维护 | 不能依赖数据库,导致数据库移植性较差,不能随意更换数据库 |

#### 二. 常见面试题

##### 1. MyBatis 与 Hibernate 有哪些不同

1. Mybatis 直接编写原生态 sql，可以严格控制 sql 执行性能,如果需要实现支持多种数据库的软件，则需要自定义多套 sql 映射文件，工作量大。

2. Hibernate 对象/关系映射能力强，数据库无关性好，对于关系模型要求高的软件，如果用 hibernate 开发可以节省很多代码，提高效率。

##### 2. #{}和${}之间的区别

**#{}是预编译处理，${}是字符串替换。**Mybatis 在处理#{}时，会将 sql 中的#{}替换为?号，调用 PreparedStatement 的set 方法来赋值；Mybatis 在处理${}时，就是把${}替换成变量的值。
**使用#{}可以有效的防止 SQL 注入**，提高系统安全性。

##### 3. 当实体类中的属性名和表中的字段名不一样 ，怎么办 ？

1. 通过在查询的sql语句中定义字段名的别名,让字段名的别名和实体类的属性名称一致.

   ```xml
   <select id=”selectorder” parametertype=”int” resultetype=”me.gacl.domain.order”>
   		select order_id id, order_no orderno ,order_price price form  orders where order_id=#{id};
   </select>
   ```

2. 通过<resultMap>来映射字段名和实体类属性名的一一对应的关系.

   ```xml
   <select id="getOrder" parameterType="int"
   resultMap="orderresultmap">
   select * from orders where order_id=#{id}
   </select>
   <resultMap type=”me.gacl.domain.order” id=”orderresultmap”>
   	<!–用 id 属性来映射主键字段–>
   	<id property=”id” column=”order_id”>
   	<!–用 result 属性来映射非主键字段，property 为实体类属性名，column为数据表中的属性–>
   	<result property =“orderno” column =”order_no”/>
   	<result property=”price” column=”order_price” />
   </reslutMap>
   ```

   
