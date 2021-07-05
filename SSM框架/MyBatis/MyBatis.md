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
#### 11. 一对一,一对多的关联查询?

```java
// association 是一对一关联查询的关键字
// 实体类的字段名和数据表的字段名映射
// collection 是一对一关联查询的关键字
```

1. mybatis实现一对一有几种方式? 

   **有联合查询和嵌套查询**,**联合查询**是几个表联合查询,**只查询一次**, 通过在
   resultMap 里面配置 association 节点配置一对一的类就可以完成；**嵌套查询是先查一个表**，根据这个表里面的结果的 外键 id，去再另外一个表里面查询数据,也是通过 **association** 配置，但另外一个表的查询**通过 select 属性**配置。

2. mybatis实现一对多有几种方式? 

   **有联合查询和嵌套查询**,**联合查询**是几个表联合查询,**只查询一次**, 通过在
   resultMap 里面配置 collection 节点配置一对一的类就可以完成；**嵌套查询是先查一个表**，根据这个表里面的结果的 外键 id，去再另外一个表里面查询数据,也是通过 **collection** 配置，但另外一个表的查询**通过 select 属性**配置。

#### 12. Mybatis 是否支持延迟加载？如果支持，它的实现原理是什么？

1. **Mybatis 仅支持 association 关联对象和 collection 关联集合对象的延迟加载**，association 指的就是一对一，collection 指的就是一对多查询。在 Mybatis配置文件中，可以配置是否启用延迟加载lazyLoadingEnabled=true|false。

2. 原理: 它的原理是，使用 **CGLIB** 创建目标对象的代理对象，当调用目标方法时，进入拦截器方法，比如调用 a.getB().getName()，拦截器 invoke()方法发现 a.getB()是null 值，那么就会单独发送事先保存好的查询关联 B 对象的 sql，把 B 查询上来，然后调用 a.setB(b)，于是 a 的对象 b 属性就有值了，接着完成a.getB().getName()方法的调用。这就是延迟加载的基本原理。
#### 13. Mybatis 的一级、二级缓存
      
1. 一级缓存: 基于 PerpetualCache 的 HashMap 本地缓存，其存储作用域为Session，当 Session flush 或 close 之后，该 Session 中的所有 Cache 就将清空，默认打开一级缓存。
2. 二级缓存与一级缓存其机制相同，默认也是采用 PerpetualCache，HashMap存储，不同在于其存储作用域为 Mapper(Namespace)，并且可自定义存储源，如 Ehcache。默认不打开二级缓存，要开启二级缓存，使用二级缓存属性类需要实现 Serializable 序列化接口(可用来保存对象的状态),可在它的映射文件中配置<cache/> ;
3. 对于缓存数据更新机制，当某一个作用域(一级缓存 Session/二级缓存Namespaces)的进行了 C/U/D 操作后，默认该作用域下所有 select 中的缓存将被 clear。

#### 14. 什么是Mybatis的接口绑定?有哪些实现方式?

1. 接口绑定，就是在 MyBatis 中任意定义接口,然后把接口里面的方法和 SQL 语句绑定, 我们直接调用接口方法就可以,这样比起原来了 SqlSession 提供的方法我们可以有更加灵活的选择和设置。
2. 接口绑定有两种实现方式,一种是通过注解绑定，就是在接口的方法上面加上@Select、@Update 等注解，里面包含 Sql 语句来绑定；另外一种就是通过 xml里面写 SQL 来绑定, 在这种情况下,要指定 xml 映射文件里面的 namespace 必须为接口的全路径名。当 Sql 语句比较简单时候,用注解绑定, 当 SQL 语句比较复杂时候,用 xml 绑定,一般用 xml 绑定的比较多。

#### 15. 使用Mybatis的mapper接口调用时有那些要求?

1. mapper接口方法名和mapper.xml中定义的每个sql的id相同。
2. mapper接口的输入参数类型和mapper.xml中定义的parameterType类型相同。
3. 接口方法的输出类型和xml定义的sqlresultType类型相同。
4. mapper.xml文件中namespace骑士mapper接口的类路径。

#### 16. Mapper编写有哪几种方式?

1. 接口实现类继承 SqlSessionDaoSupport：使用此种方法需要编写mapper 接口，mapper 接口实现类、mapper.xml 文件。
2. 使用 org.mybatis.spring.mapper.MapperFactoryBean
3. 使用mapper扫描器
4. 使用扫描器后从 spring 容器中获取 mapper 的实现对象。

#### 17. 简述Mybatis的插件运行原理,如何编写一个插件?

1. Mybatis 仅可以编写针对 ParameterHandler、ResultSetHandler、StatementHandler、Executor 这 4 种接口的插件，Mybatis 使用 JDK 的动态代理，为需要拦截的接口生成代理对象以实现接口方法拦截功能，每当执行这 4 种接口对象的方法时，就会进入拦截方法，具体就是 InvocationHandler 的 invoke()方法，当然，只会拦截那些你指定需要拦截的方法。
2. 编写插件：实现 Mybatis 的 Interceptor 接口并复写 intercept()方法，然后在给插件编写注解，指定要拦截哪一个接口的哪些方法即可，记住，别忘了在配置文件中配置你编写的插件。

      
  
   
