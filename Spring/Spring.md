# Spring学习
	

#### 1.轮子理论
	不要重复发明轮子(直接使用写好的代码)
	spring框架的宗旨：不重新发明技术，让原有技术使用起来更加方便

#### 2.Spring
	Ioc 解耦
	AOP 扩展
	事务管理

#### 3.IoC 控制反转
    1.IoC是什么？
		原先由程序员主动通过new实例化的事情交给spring来管理
    2.作用：解耦
    	解除了程序员与对象之间的耦合

#### 4.环境搭建(导入jar包)
> 4个核心 1个依赖

	beans、core、context、expression
	commons-loggins

#### 5.编写配置文件applicationContext.xml
	通过bean标签来创建对象(默认是配置文件被加载时创建对象)
		<?xml version="1.0" encoding="UTF-8"?>
		<beans xmlns="http://www.springframework.org/schema/beans"
			xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
			xsi:schemaLocation="http://www.springframework.org/schema/beans
				http://www.springframework.org/schema/beans/spring-beans.xsd">
			<!-- 
				id表示获取到对象标识
				 class 创建哪个类的对象
			 -->
			<bean id="peo" class="com.wzy.pojo.People"/>
		</beans>
		
	测试方法中使用
		ApplicationContext ac=new ClassPathXmlApplicationContext("applicationContext.xml");
		// 参数：<Bean>标签的id   返回值类型(默认为object)
		People peo = ac.getBean("peo",People.class);
	来获取对象

#### 6.创建对象的方式
	1.通过构造方法创建(无参构造、有参构造)
	2.实例工厂
	3.静态工厂
	
	1.如果匹配多个构造方法(默认执行最后的构造方法)
	
	工厂设计模式：帮助创建类对象，一个工厂可以生产多个对象
	2.实例工厂
		需要创建工厂，才能生产对象
		
	3.静态工厂(在创建对象的方法前面添加static关键字)
		不需要创建工厂,快速创建对象.

**applicationContext.xml**

		<?xml version="1.0" encoding="UTF-8"?>
		<beans xmlns="http://www.springframework.org/schema/beans"
			xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
			xsi:schemaLocation="http://www.springframework.org/schema/beans
				http://www.springframework.org/schema/beans/spring-beans.xsd">
			<!-- 
				id表示获取到对象标识
				 class 创建哪个类的对象
			 -->
			<bean id="peo" class="com.wzy.pojo.People"/>
			 <bean id="peo1" class="com.wzy.pojo.People">
				<constructor-arg index="0" value="666" ></constructor-arg>
				<constructor-arg index="1" value="zhiyuan"></constructor-arg>
			 </bean>
			 <bean id="peo2" class="com.wzy.pojo.People">
				<constructor-arg name="id" value="666" ></constructor-arg>
				<constructor-arg index="1" value="zhiyuan"></constructor-arg>
			 </bean>
			 <!-- 创建实例工厂 -->
			 <bean id="factory" class="com.wzy.pojo.PeopleFactory"></bean>
			 <!-- 实例工厂创建对象 -->
			 <bean id="peo3" factory-bean="factory" factory-method="newInstance"></bean>
			 
			 <!-- 静态工厂创建对象 -->
			 <bean id="peo4" class="com.wzy.pojo.PeopleFactory" factory-method="newInstance1"></bean>
			 
		</beans>
	
		构造工厂的代码
			public class PeopleFactory {

				public People newInstance() {
					return new People();
				}
				
				// 静态实例
				public static People newInstance1() {
					return new People();
				}
			}
**测试代码**

			public class Test {
				public static void main(String[] args) {
					ApplicationContext ac=new ClassPathXmlApplicationContext("applicationContext.xml");
					// 无参构造
					People peo = ac.getBean("peo",People.class);
					// 有参构造
					People peo1 = ac.getBean("peo1",People.class);
					People peo2 = ac.getBean("peo1",People.class);

					// 实例工厂创建对象
					People peo3 = ac.getBean("peo3",People.class);
					
					// 静态工厂创建对象
					People peo4 = ac.getBean("peo4",People.class);
					System.out.println(peo4);
				}
			}

#### 7.给Bean的属性赋值(注入)
	1.通过构造方法来注入
	2.设置注入(通过set方法来注入)

#### 8.DI 依赖注入
	DI:当一个对象(A)中需要依赖另一个对象(B)时，把另一个对象(B)赋值给这个对象(A)的过程叫做DI

#### 9.Spring来简化Mybatis
	1.导包
		Mybatis需要的jar包
		Spring运行需要的包
		spring-jdbc、mybatis-spring、spring-tx、spring-aop、spring-web
	2.写配置文件
		dataSource
		sqlSessionFactory
		MapperScannerConfigurer(mybatis.xml下的mappper标签下的package属性)
		注意：
			给属性注入需要spring管理属性所在类
			如果要上传至tomcat，需要编写web.xml文件
			spring无法管理Servlet
				<!-- 上下文参数 -->
				<context-param> <param-name>contextConfigLocation</param-name>
				<!-- spring 配置文件 -->
				<param-value>classpath:applicationContext.xml</para m-value> </context-param>
		
#### 10.AOP 面向切面编程
##### 1.正常执行顺序：纵向流程
						demo1()
						demo2()
						demo3()
##### 2.AOP
		
						demo1()
			前置通知	demo2()		后置通知
						demo3()	
		在程序原有纵向流程中，针对某一个或者某些方法添加通知，形成横切面过程就叫做面向切面编程。
			高扩展性
			相当于释放了部分逻辑，让职责更加明确
##### 3.常用概念
		1.原有功能：切点
		2.前置通知 beforeAdvice
		3.后置通知 afterAdvice
		4.异常通知 throwAdvice
		5.所有功能总称叫做切面
		6.织入 把切面嵌入原有功能叫做织入
##### 4.实现方式
		1.schame-base方式
			每个通知需要实现接口或者类(MathBeforeAdvice、AfterReturningAdvice)
			配置spring文件时是在<aop:config>中配置
		2.aspectJ方式
			每个通知不需要实现接口或者类
			配置spring文件时是在<aop:config>标签的子标签<aop:aspect>中配置
	Schame-base实现代码
		4.实现前置通知	
			public class MyBeforeAdvice implements MethodBeforeAdvice{

				@Override
				public void before(Method arg0, Object[] arg1, Object arg2) throws Throwable {
					// TODO Auto-generated method stub
					System.out.println("执行前置通知");
				}
			}
##### 5.实现后置通知
			public class MyAfterAdvice implements AfterReturningAdvice {

				@Override
				public void afterReturning(Object arg0, Method arg1, Object[] arg2, Object arg3) throws Throwable {
					// TODO Auto-generated method stub
					System.out.println("执行后置通知！");
				}

			}
		
##### 6.applicationContext.xml
			<?xml version="1.0" encoding="UTF-8"?>
			<beans xmlns="http://www.springframework.org/schema/beans"
				xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
				xmlns:aop="http://www.springframework.org/schema/aop"
				xsi:schemaLocation="http://www.springframework.org/schema/beans
					http://www.springframework.org/schema/beans/spring-beans.xsd
					http://www.springframework.org/schema/aop
					http://www.springframework.org/schema/aop/spring-aop.xsd">
				<!-- 
					aop配置
				 -->
				<aop:config>
					<!-- 定义切点 -->
					<aop:pointcut expression="execution(* com.wzy.test.Demo.demo2())" id="mypoint"/>
					<!-- 引入通知 -->
					<aop:advisor advice-ref="myBeforeAdvice" pointcut-ref="mypoint"/>
					<aop:advisor advice-ref="myAfterAdvice" pointcut-ref="mypoint"/>
				</aop:config>
				
				<!-- 上面引入通知的时候需要注入通知类 -->
				<bean id="myBeforeAdvice" class="com.wzy.advice.MyBeforeAdvice"></bean>
				<bean id="myAfterAdvice" class="com.wzy.advice.MyAfterAdvice"></bean>
				
				<!-- 需要将引入通知方法的对象交给spring来管理，测试的时候使用 -->
				<bean id="demo" class="com.wzy.test.Demo"></bean>
			</beans>
##### 6.配置异常通知
		只有切点报了异常才能触发异常通知，否者无效
		使用了try-catch处理之后也不会触发
		1.schema-base方法
			新建一个类实现ThrowsAdvice接口
			必须重写afterThrowing()方法 
			参数必须是一个或者四个
				public class MyThrowAdvice implements ThrowsAdvice {
					public void afterThrowing(Exception ex) throws Throwable { 					
						System.out.println("执行异常通过-schema-base 方式 "); 
					}
				}	
		2.AspectJ方式	
			新建任意类 任意方法
			public class MyThrowAdvice{ 
				public void myexception(Exception e1){ 	
					System.out.println("执行异常通知 "+e1.getMessage()); 
				} 
			}
		applicationContext.xml文件配置
			<aop:config>
				<!-- Schema-base方式 -->
				<aop:pointcut expression="execution(* com.wzy.test.*.*(..))" id="mypoint"/>
				<aop:advisor advice-ref="myThrowAdvice" pointcut-ref="mypoint"/>
				
				<!-- AspectJ方式 -->
				<aop:aspect ref="myThrowAdvice1">
					<aop:pointcut expression="execution(* com.wzy.test.*.*(..))" id="mypoint1"/>
					<aop:after-throwing method="myThrowingAspectJ" pointcut-ref="mypoint1" />
				</aop:aspect>
			</aop:config>
			
			<!-- AspectJ方式 -->
			<bean id="myThrowAdvice1" class="com.wzy.advice.MyThrowAdvice"></bean>
			<!-- Schema-base方式 -->
			<bean id="myThrowAdvice" class="com.wzy.advice.MyThrowAdvice"></bean>
			
			
##### 7.环绕通知：那前置通知和后置通知都写在一个通知中7.环绕通知：那前置通知和后置通知都写在一个通知中
		
		1.schema-base
			public class MyAroundAdvice implements MethodInterceptor{
				@Override
				public Object invoke(MethodInvocation arg0) throws Throwable {
					// TODO Auto-generated method stub
					System.out.println("环绕---前置通知");
					Object result = arg0.proceed(); // 相当于放行
					System.out.println("环绕---后置通知");
					return result;
				}

			}
		2.AspectJ
			public class MyAdvice {
				public Object myarround(ProceedingJoinPoint p) throws Throwable {
					System.out.println("执行环绕");
					System.out.println("AspectJ环绕--前置");
					Object result = p.proceed();
					System.out.println("AspectJ环绕--后置");
					return result;
				}
			}
	8.使用注解配置AOP
		由于spring不知道哪些包里面的类可能有注解
		因此需要扫描包
			<!-- 扫描可能有注解的包  多个包用逗号隔开-->
			<context:component-scan base-package="com.wzy.advice,com.wzy.test"></context:component-scan>
						
			
#### 11.自动注入

#### 12.加载属性文件
	<context:property-placeholder location="classpath:db.properties"/>
	
	在取的时候${jdbc.username}但是要保证sqlSessionfactor类对象id为factory
	可以使用@Value注解来获取属性文件的值		
			
#### 13.声明式事务
> 	编程式事务和声明式事务的区别？
		编程式事务，是在代码中直接添加处理事务的逻辑。声明式事务的事务控制代码已经由spring写好，我们只用声明出哪些方法需要进行事务控制。

		<aop:config>
			<!-- 定义切点 -->
			<aop:pointcut expression="execution(* com.wzy.service.impl.*.*(..))" id="mypoint"/>
			<aop:advisor advice-ref="txAdvice" pointcut-ref="mypoint"/>
		</aop:config>
		<!-- 配置通知类 需要连接数据库 -->
		<bean id="txManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
			<property name="dataSource" ref="dataSource"></property>
		</bean>
		<!-- 配置声明式事务  -->
		<tx:advice id="txAdvice" transaction-manager="txManager">
			<tx:attributes>
				<!-- 哪些方法需要事务管理 -->
				<tx:method name="ins*" />
				<tx:method name="upd*" />
				<tx:method name="del*" />
				<tx:method name="*" read-only="true"/>
			</tx:attributes>
		</tx:advice>			
###### <tx:method>属性说明
	1.name="" 哪些方法需要事务控制
	2.read-only=""
		true:告诉数据库此事务为只读事务。
		false(默认值):事务需要提交
	3.propagation=""	
		控制事务传播行为：当一个具有事务管理的方法被另一个有事务管理的方法调用，应该怎么处理
		REQUIRED：如果当前有事务，就在当前事务执行，不会新建事务；没有就新建一个事务
		SUPPORTS：如果当前有事务，就在当前事务执行；没有就在非事务状态下进行。
		MANDATORY：必须在事务内部执行，如果当前有事务，就在事务执行；没有事务就会报错。
		REQUIRES_NEW：必须在事务中执行，如果当前有事务，将事务挂起；没有事务，新建事务。
		NOT_SUPPORTED：必须在非事务下进行，有事务，挂起；没有事务，正常进行。
		NEVER：必须在非事务下进行；没有事务正常执行；有事务，报异常；
		NESTED：必须在事务状态下进行；没有事务，新建事务；有事务，会新建一个嵌套事务
	4.isolation=""
		事务隔离级别:多线程或者高并发下如何保证数据具有完整性。
		DEFAULT：默认值，有数据库自动判断使用什么隔离级别
		READ_UNCOMMITED:可以读取未提交的数据，可能会出现脏读，不可重复读和幻读
		READ_COMMITED:只能读取到其他事物已经提交的数据：可以防止脏读，可能出现不可重复读和幻读
		REPEATABLE_READ:读取的数据被添加锁：放指不可重复读和脏读，可能出现幻读
		SERIALIZABLE：排队操作，对整个表添加锁，一个事务在操作数据时，另一个事务等待事务操作完成后才能操作整个表。

		脏读：一个事务A读取了另一个事务B为提交的数据，另一个事务B中的数据可能进行了改变，此时A事务读取的数据可能和数据库中的不一致,此时认为数据是脏数据。读取脏数据的过程为脏读。
		不可重复读：针对的对是某行数据的修改操作，两次读的过程实在同一个事务内
			当事务A在第一次读取事务后，事务B对事务A读取的数据进行修改，事务A中再次读取的数据和之前读取的数据不一致，过程不可重复读。
			
			解决：读取数据的时候限制这条数据不能修改
			例子：A去柜台取钱，问柜台人员卡里有多少钱，回答88888元，此时A老婆B在ATM取出88880元。A说取出10000元，柜台人员正要取的时候发现余额为8元
		
		幻读：针对新增或者删除，两次事务的结果
			事务A按照特定条件查询出结果，事务B去新增了一条符合条件的数据。事务A中查询的数据和数据库中的数据不一致，事务A好像出现了幻觉，这种情况称为幻读。
			例：发工资的时候，会计A准备发工资，开始查询工资在5000-10000之间的人有多个。发现有10个，此时另一个会计B录入一个员工的工资为8888.此时数据库中符合A查询条件的有11个。
			
			解决：锁定整个表

#### 14.spring中常用注解

    1. @Component 相当于配置一个<bean>标签
    2. @Service 和@Component功能相同，只是语义的不同，给开发者一个提示
    3. @Controller 同上  
    4. @Repository  同上
    5. @Resource  javax注解   默认按照byName注入，没有就按byType注入	
    6. @Autowired spring注解   默认按照byType注入
      @Resource  @Autowired 都不需要写对象的get和set方法
      为了提高效率建议把对象名和spring容器中的对象名改为一致
    7. @Value	获取属性文件(db.properties)的值
    8. @Pointcut 定义切点
    9. @Aspect() 定义切面类
    10. @Before() 前置通知
    11. @After 后置通知
    12. @AfterReturning 后置通知,必须切点正确执行
    13. @AfterThrowing 异常通知
    14. @Arround 环绕通知