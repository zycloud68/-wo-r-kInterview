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

#### 1. MyBatis 与 Hibernate 有哪些不同

1. Mybatis 直接编写原生态 sql，可以严格控制 sql 执行性能,如果需要实现支持多种数据库的软件，则需要自定义多套 sql 映射文件，工作量大。

2. Hibernate 对象/关系映射能力强，数据库无关性好，对于关系模型要求高的软件，如果用 hibernate 开发可以节省很多代码，提高效率。

#### 2. #{}和${}之间的区别

**#{}是预编译处理，${}是字符串替换。**Mybatis 在处理#{}时，会将 sql 中的#{}替换为?号，调用 PreparedStatement 的set 方法来赋值；Mybatis 在处理${}时，就是把${}替换成变量的值。
**使用#{}可以有效的防止 SQL 注入**，提高系统安全性。

#### 3. 当实体类中的属性名和表中的字段名不一样 ，怎么办 ？

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
 #### 4. 通常一个 Xml 映射文件，都会写一个 Dao 接口与之对应，请问，这个 Dao 接口的工作原理是什么？Dao 接口里的方法，参数不同时，方法能重载吗?

| 原理 | Dao 接口即 Mapper 接口。接口的全限名，就是映射文件中的 namespace 的值；接口的方法名，就是映射文件中 Mapper 的 Statement 的 id 值；接口方法内的参数，就是传递给 sql 的参数 |
| ---- | ------------------------------------------------------------ |
| 流程 | Mapper接口的方法是不能重载的,原因是使用 "全类名+方法名"的保存方法和寻找策略,mapper接口使用的是JDK动态代理,mybatis运行时使用jdk动态代理为mapper接口生成proxy,代理对象拦截接口方法,执行MapperStatement所代表的sql,执行结果并返回. |

#### 5. MyBatis是如何进行分页的,分页插件的原理是什么?

1. Mybatis使用的是RowBounds对象进行分页的,针对ResultSet结果集执行内存分页,并非物理分页,可以在sql执行书写,也可以使用分页插件来完成物理分页.

2. 原理: 使用Mybatis提供的插件接口,实现自定义插件,在插件中拦截方法内拦截待执行的sql,然后重写sql,根据方言,添加对应的物理分页语句和分页参数.

#### 6. 如何进行批量插入

1. 创建一个简单的insert语句

   ```xml
   <insert id=”insertname”>
   insert into names (name) values (#{value})
   </insert>
   ```

2. 在java语句中执行批处理插入

   ```xml
   list < string > names = new arraylist();
   names.add(“fred”);
   names.add(“barney”);
   names.add(“betty”);
   names.add(“wilma”);
   // 注意这里 executortype.batch
   sqlsession sqlsession =
   sqlsessionfactory.opensession(executortype.batch);
   try {
   		namemapper mapper = sqlsession.getmapper(namemapper.class);
   			for (string name: names) {
   				mapper.insertname(name);
   				}
   	sqlsession.commit();
   } catch (Exception e) {
   e.printStackTrace();
     sqlsession.rollback();
     throw e;
   }
     finally{
     sqlsession.close();
     }
     
   ```

   

#### 7. 如何获取自动生成的(主键)值

```xml
<insert id=”insertname” usegeneratedkeys=”true” keyproperty=”id”>
insert into names (name) values (#{name})
</insert>
```

#### 8. 在mapper中如何传递多个参数?

```java
// 第一种: DAO层函数
public UserselectUser(String name,String area);
// 对应的 xml,#{0}代表接收的是 dao 层中的第一个参数，#{1}代表 dao 层中第二参数，更多参数一致往后加即可
// 第二种: 使用插件带有的@Param注解
public UserselectUser(@Param(name) String name,@Param(area) String area);
// 第三种: 多个参数封装成map对象
```

#### 9. 动态sql执行原理,有那些动态sql?

Mybatis 动态 sql 可以在 Xml 映射文件内，以标签的形式编写动态 sql，执行原理是根据表达式的值 完成逻辑判断并动态拼接 sql 的功能。Mybatis 提供了 9 种动态 sql 标签：trim | where | set | foreach | if | choose| when | otherwise | bind。

#### 10. Xml 映射文件中，除了常见的 select|insert|updae|delete标签外,还有那些标签?

**resultMap,parameterMap,sql,include,selectKey**，加上动态 sql 的 9 个标签，其中sql为 sql 片段标签，通过include标签引入 sql 片段，selectKey为不支持自增的主键生成策略标
签。     

   
