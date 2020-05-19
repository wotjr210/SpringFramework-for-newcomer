
# DI(Dependency Injection)

## DI(Dependency Injection)란?
> '의존성 주입'이란 의미를 가지고 있으며, 객체를 직접 생성하여 사용하는 형태가 아닌 외부에서 생성된 객체를 주입시켜 사용하는 방식입니다.

1. Constructor Injection(생성자를 이용한 주입)
```
public class cellphoneA {
  private Battery battery;
  
  public cellphoneA(){
    battery = new Battery();
  }
}
```
2. Setter Injection(Setter메소드를 이용한 주입)
```
public class cellphoneB {
  private Battery battery;
  
  public cellphoneB(){
  }
  
  public void setBattery(Battery battery){
    this.battery = battery;
  }
}
```

## Spring DI 설정방법
> new연산자를 이용해 객체를 생성해서 사용하는것이아닌, Spring Container에 Bean(객체)을 생성하여 객체를 호출만하여 사용합니다.

resouces 경로 아래의 xml로 구성된다.

1.Constructor
```
[applicationContext.xml]

<!-- id는 사용자정의, class는 사용할 클래스의 풀패키지명.클래스명 -->
<bean id="studentDao" class="ems.member.dao.StudentDao" ></bean>

<bean id="registerService" class="ems.member.service.StudentRegisterService">
  <!-- 생성자의 파마미터로 studentDao객체 주입 -->
  <constructor-arg ref="studentDao" ></constructor-arg>
</bean>
```
* GenericXmlApplicationContext 클래스를 이용하여 Spring Container를 생성한다.
* getBean을 이용하요 생성된 bean(객체)를 호출하여 사용한다.
```
[mainClass.java]

GenericXmlApplicationContext ctx = new GenericXmlApplicationContext("classpath:applicationContext.xml");
EMSInformationService informationService = ctx.getBean("informationService", EMSInformationService.class);
```
2.Setter Method
* settter method를 사용할시 아래와 같이 <property/>를 가진 bean을 생성한다.
* <property/>의 name속성은 반드시 set 이후의 명을 '소문자'로 표기하면된다.
```
public void setJdbcUrl(String jdbcUrl) {
	this.jdbcUrl = jdbcUrl;
}
    
<bean id="dataBaseConnectionInfoDev" class="ems.member.DataBaseConnectionInfo">
	<property name="jdbcUrl" value="jdbc:oracle:thin:@localhost:1521:xe" />
</bean>  
```  
```
파라미터 List타입인 경우,
public void setDevelopers(List<String> developers) {
	this.developers = developers;
}
    
<bean id="dataBaseConnectionInfoDev" class="ems.member.DataBaseConnectionInfo">
	<property name="developers">
			<list>
				<value>Cheney</value>
				<value>Eloy</value>
				<value>Jasper</value>
				<value>Dillon</value>
				<value>Kian</value>
			</list>
		</property>
</bean>  
```  
```
파라미터 Map타입인 경우,
public void setAdministrators(Map<String, String> administrators) 
{ 
  this.administrators = administrators; 
}
    
<bean id="dataBaseConnectionInfoDev" class="ems.member.DataBaseConnectionInfo">
  <property name= "administrators" >
    <map>
      <entry>
        <key>
          <value>Cheney</value>
        </key>
        <value>cheney@springPjt.org</value> 
      </entry>
      <entry>
        <key>
          <value>Jasper</value>
        </key>
        <value>jasper@springPjt.org</value>
      </entry>
    </map>
  </property>
</bean>  
```  

## Spring DI 설정 관리
1. resource 분리
* 성격에 따라 resource를 분리한 경우, Array타입으로 담아서 Spring Container를 생성한다.
```
String[] appCtxs = {"classpath:appCtx1.xml", "classpath:appCtx2.xml", "classpath:appCtx3.xml"};
GenericXmlApplicationContext ctx = new GenericXmlApplicationContext(appCtxs);
```
2. resource import
* <import/>를 이용해 설정한다.
```
<import resource="classpath:appCtx2.xml"/>
```
## Bean의 Scope
1. Singleton
기본적으로  Spring Container에서 생성된 Bean의 경우 동일한 타입에 대해서는  한개만 생성되며, getBean메소드로 호출될때 동일한 객체가 반환된다.
2. Prototype
Singleton과 반대되는 개념이다. 해당 Bean이 Prototype로 사용될경우 별도의 scope 속성이 요구된다.
```
<bean id= "dependencyBean" class = "scope.ex.DependencyBean" scope = "prototype" > 
  <constructor-arg ref= "injectionBean" /> 
  <property name= "injectionBean" ref = "injectionBean" />
</bean>
```
