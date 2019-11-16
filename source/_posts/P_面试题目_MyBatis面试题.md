
##P_面试题目_MyBatis面试题

###1、#{}和${}的区别是什么？
	#{}是预编译处理，Mybatis会将sql中的#{}替换为?号
	${}是字符串替换
###2、当实体类中的属性名和表中的字段名不一样 ，怎么办 ？
	1、别名
	2、通过<resultMap>
###3、通常一个Xml映射文件，都会写一个Dao接口与之对应，请问，这个Dao接口的工作原理是什么？Dao接口里的方法，参数不同时，方法能重载吗？
	
	Dao接口，就是人们常说的Mapper接口，接口的全限名，就是映射文件中的namespace的值，接口的方法名，就是映射文件中MappedStatement的id值，接口方法内的参数，就是传递给sql的参数。
	Mapper接口是没有实现类的，当调用接口方法时，接口全限名+方法名拼接
	字符串作为key值，可唯一定位一个MappedStatement，举例：
	
	com.mybatis3.mappers.StudentDao.findStudentById，
	可以唯一找到namespace为com.mybatis3.mappers.StudentDao下面id = findStudentById的MappedStatement。
	在Mybatis中，每一个<select>、<insert>、<update>、<delete>标签，都会被解析为一个MappedStatement对象。
	
	Dao接口的工作原理是JDK动态代理，Mybatis运行时会使用JDK动态代理为Dao接口生成代理proxy对象，代理对象proxy会拦截接口方法，转而执行MappedStatement所代表的sql，然后将sql执行结果返回

	
	Dao接口里的方法，是不能重载的，因为是全限名+方法名的保存和寻找策略。

###4、Mybatis是如何进行分页的？分页插件的原理是什么？(重点)

	1、Mybatis使用RowBounds对象进行分页，它是针对ResultSet结果集执行的内存分页，而非物理分页，可以在sql内直接书写带有物理分页的参数来完成物理分页功能，也可以使用分页插件来完成物理分页。
	
	2、分页插件的基本原理是使用Mybatis提供的插件接口，实现自定义插件，在插件的拦截方法内拦截待执行的sql，然后重写sql，根据dialect方言，添加对应的物理分页语句和物理分页参数。

###5、Mybatis动态sql是做什么的？都有哪些动态sql？能简述一下动态sql的执行原理不？
	Mybatis动态sql可以让我们在Xml映射文件内，以标签的形式编写动态sql，完成逻辑判断和动态拼接sql的功能。Mybatis提供了9种动态sql标签：trim|where|set|foreach|if|choose|when|otherwise|bind。
	
	其执行原理为，使用OGNL从sql参数对象中计算表达式的值，根据表达式的值动态拼接sql，以此来完成动态sql的功能。


###6、 一对一、一对多的关联查询 ？

	在resultMap里面配置关联关系


###7、 什么是MyBatis，和其他的ORM的区别？

	MyBatis是一个可以自定义SQL、存储过程和高级映射的持久层框架

	Hibernate属于全自动ORM映射工具，使用Hibernate查询关联对象或者关联集合对象时，可以根据对象关系模型直接获取，所以它是全自动的。
    而Mybatis在查询关联对象或关联集合对象时，需要手动编写sql来完成，所以，称之为半自动ORM映射工具

###8、 讲下MyBatis的缓存？（配置文件中存在）

	MyBatis的缓存分为一级缓存和二级缓存,一级缓存放在session里面,默认就有,二级缓存放在它的命名空间里,默认是不打开的,使用二级缓存属性类需要实现Serializable序列化接口(可用来保存对象的状态),可在它的映射文件中配置<cache/>


###9、简述Mybatis的插件运行原理，以及如何编写一个插件？(重点)

	1）Mybatis仅可以编写针对ParameterHandler、ResultSetHandler、StatementHandler、Executor这4种接口的插件，
	Mybatis通过动态代理，为需要拦截的接口生成代理对象以实现接口方法拦截功能，每当执行这4种接口对象的方法时，就会进入拦截方法，具体就是InvocationHandler的invoke()方法，当然，只会拦截那些你指定需要拦截的方法。
	
	2）实现Mybatis的Interceptor接口并复写intercept()方法，然后在给插件编写注解，指定要拦截哪一个接口的哪些方法即可，记住，别忘了在配置文件中配置你编写的插件。


###10、Mybatis是否支持延迟加载？如果支持，它的实现原理是什么？

	1）Mybatis仅支持association关联对象和collection关联集合对象的延迟加载，association指的就是一对一，collection指的就是一对多查询。
	在Mybatis配置文件中，可以配置是否启用延迟加载lazyLoadingEnabled=true|false。
	
	2）它的原理是，使用CGLIB创建目标对象的代理对象，当调用目标方法时，进入拦截器方法，
	    比如调用a.getB().getName()，拦截器invoke()方法发现a.getB()是null值，那么就会单独发送事先保存好的查询关联B对象的sql，把B查询上来，然后调用a.setB(b)，于是a的对象b属性就有值了，接着完成a.getB().getName()方法的调用。这就是延迟加载的基本原理。


###11、简述Mybatis的Xml映射文件和Mybatis内部数据结构之间的映射关系？
	Mybatis将所有Xml配置信息都封装到All-In-One重量级对象Configuration内部。在Xml映射文件中，
	<parameterMap>标签会被解析为ParameterMap对象，其每个子元素会被解析为ParameterMapping对象。
	<resultMap>标签会被解析为ResultMap对象，其每个子元素会被解析为ResultMapping对象。
	每一个<select>、<insert>、<update>、<delete>标签均会被解析为MappedStatement对象，标签内的sql会被解析为BoundSql对象

###12、Mybatis都有哪些Executor执行器？它们之间的区别是什么？
	
	Mybatis有三种基本的Executor执行器，SimpleExecutor、ReuseExecutor、BatchExecutor。
	1）SimpleExecutor：
	每执行一次update或select，就开启一个Statement对象，用完立刻关闭Statement对象。
	2）ReuseExecutor：执行update或select，
	以sql作为key查找Statement对象，存在就使用，不存在就创建，用完后，不关闭Statement对象，而是放置于Map
	3）BatchExecutor：完成批处理。


###13、使用MyBatis的mapper接口调用时有哪些要求？

	1）Mapper接口方法名和mapper.xml中定义的每个sql的id相同
	2）Mapper接口方法的输入参数类型和mapper.xml中定义的每个sql 的parameterType的类型相同
	3）Mapper接口方法的输出参数类型和mapper.xml中定义的每个sql的resultType的类型相同
	4）Mapper.xml文件中的namespace即是mapper接口的类路径。