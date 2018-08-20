# Troubleshooting
## Partitioning USB
### USB not showing
https://www.pendrivelinux.com/restoring-your-usb-key-partition/

## Create a configuration in SpringBoot
* Lets us try to autowire a SessionFactory object. For this, we need to configure a SessionFactory bean. Go-To application.properties and add ```spring.jpa.properties.hibernate.current_session_context_class=
org.springframework.orm.hibernate4.SpringSessionContext```.

* Our configuration class is one where ```@SpringBootApplication``` is declared. GoTo that class and add
```
	@Bean
	public HibernateJpaSessionFactoryBean sessionFactory() {
    return new HibernateJpaSessionFactoryBean();
	}
```

* New way of writing it in update versions of springboot : 
```
	@Bean
	@SuppressWarnings("deprecation")
	public HibernateJpaSessionFactoryBean sessionFactory(EntityManagerFactory emf) {
	    HibernateJpaSessionFactoryBean fact = new HibernateJpaSessionFactoryBean();
	    fact.setEntityManagerFactory(emf);
	    return fact;
	}
```
In application.properties of a springboot application : ```spring.jpa.properties.hibernate.current_session_context_class=
org.springframework.orm.hibernate5.SpringSessionContext```
[Link for reference](https://stackoverflow.com/questions/25063995/spring-boot-handle-to-hibernate-sessionfactory)

Goto your class where you want SessionFactory and add :
```
	@Autowired
	private SessionFactory sessionFactory;
```

## Autowiring constructor vs field
* We have (A) Field Injection : 
    ```
    	@Component
    	public class SomeService {
         @Autowired private SomeOtherService someOtherService;
    }
    ```
    and (B) Constructor Injection:
    ```
    	@Component
    	public class SomeService {
    	    private final SomeOtherService someOtherService;
    
    	    @Autowired
    	    public SomeService(SomeOtherService someOtherService){
    	        this.someOtherService = someOtherService;
    	    }
    	}
    ```
    
* In Option(A), you are allowing anyone (in different class outside/inside the Spring container) to create an instance using default constructor (like new SomeService()), which is NOT good as you need SomeOtherService object (as a dependency) for your SomeService.

* Option(B) is preferred approach as it does NOT allow to create SomeService object without actually resolving the SomeOtherService dependency.

* We do [not need](https://stackoverflow.com/questions/10141235/spring-dependency-injection-autowired-without-setter) setters while autowiring.

[Constructor vs setter injection](http://www.javainterviewpoint.com/difference-between-setter-and-constructor-injection/)
[Reference Link](https://stackoverflow.com/questions/40620000/spring-autowire-on-properties-vs-constructor)
[Reference Link on  Setter vs Constructor Injection](https://www.logicbig.com/tutorials/spring-framework/spring-core/types-of-dependency-injection.html)

## Java transient vs JPA @Transient
* **transient** is a java keyword that is used on fields to make them no serializable. **@Transient** is a annotation provided by JPA. Using this on a field makes them , they will get serialized but will not get persisted into database.

* This means that **@Transient** is stronger condition then **transient**.

* NEED for this? suppose inside an entity class we have a field :
    ```	
        @Transient
    	private List<OrderRatings> ratings;
    ```
    Now suppose we want to persist that entity into the database. ByteStream of `ratings` will get created but column for this field will not be created in the database.

* THIS ```@Transient``` field can be used normally like any other field of a class. For eg : saving JSON DATA through a postman call will save List of the ratings into the object and we can use it as we want.

## Using @Query JPQL
* If we want to add a ```@Query``` on a user-defined method of a repository implementing ```CrudRepository```, make sure that in a paramterized query parameters are passed with the name corresponding to fields in entity class.

Entity Class :
```
@Component
@Entity
@Table(name="ORD_Ratings")
public class OrderRatings implements Serializable{

	private static final long serialVersionUID = 1L;

	@Id
	private String variant_id;
	
	@ManyToOne
	@JoinColumn(name="orderNo", nullable = false)
	@JsonIgnore
	private OrderRequest request;

	/**
	 * Rating given by customer to a particular product present in the OrderRequest
	 */
	@Column
	private byte rating_food;

```
Corresponding query used in repository : 
```	
	@Query("SELECT COUNT(o) FROM OrderRatings o WHERE o.request.orderNo = :orderNo ")//TODO use of pagination for limit
	Integer findOrderRatingsByOrdersNo(@Param("orderNo") String orderNo);

``` 
HERE we are using `request` field defined inside the `OrderRatings` model class.

* Also notice that we have a field object inside entity class mentioned above : `private OrderRequest request;`. If we want to compare use this field inside query, we can use `.` to access them. : for eg : 	
```
	@Query("SELECT COUNT(o) FROM OrderRatings o WHERE o.request.orderNo = :orderNo ")//TODO use of pagination for limit
```
Here we are accessing `orderNo` field of `OrderRequest` class.

[Reference on how to select a way for querying...](https://stackoverflow.com/questions/48520827/use-queryhint-when-using-jpaspecificationexecutor#answer-48541323)

[Reference on using Pageable class as a alternate to impose limit on our @Query...](https://stackoverflow.com/questions/20679237/jpql-limit-query)

## @Component vs @Entity vs @Repository vs @Service vs @Controller
> `@Repository` : DAO Layer : Directly intreracts with database.
> `@Service` : Service Layer : Uses DAO layer and provides different functionalities.
> `@Controller` : Presentation Layer : classes handling/getting direct requests in apis.
> `@Entity` : On entity classes : Specifies class as an entity to be persisted.

* We can use `@Component` for all others too.
* Thus, if you are choosing between using `@Component` or `@Service` for your service layer, @Service is clearly the better choice. Similarly, as stated above, `@Repository` is already supported as a marker for automatic exception translation in your persistence layer.
* There is no need to define an interface with `@Repository`. But, while using Spring-data-jpa, we dont need interfaceImplementation and in such case we need to define interface itself with `@Repository`

[Reference Discussion...](https://stackoverflow.com/questions/12658136/springmvc-annotations-for-dao-interface-and-dao-implementation)

## Error : LoggerFactory is not a Logback LoggerContext but Logback is on the classpath
**Solution** : Springboot by default adds LogBack and it collides with one that you may specifically provide using slf4j.
```
	<dependency>
	  <groupId>org.springframework.boot</groupId>
	  <artifactId>spring-boot-starter</artifactId>
	  <exclusions>
	    <exclusion>
	      <groupId>org.springframework.boot</groupId>
	      <artifactId>spring-boot-starter-logging</artifactId>
	    </exclusion>
	  </exclusions>
	</dependency>

	<dependency>
	  <groupId>org.springframework.boot</groupId>
	  <artifactId>spring-boot-starter-web</artifactId>
	  <exclusions>
	    <exclusion>
	      <groupId>org.springframework.boot</groupId>
	      <artifactId>spring-boot-starter-logging</artifactId>
	    </exclusion>
	  </exclusions>
	</dependency>
```
[Reference update dependecy](https://stackoverflow.com/questions/23984009/disable-logback-in-springboot)

## Running JUnit tests in parallel
[Reference doc](https://maven.apache.org/surefire/maven-surefire-plugin/examples/junit.html)

## When to use different log levels
Basic use :

* **Debug** - Information that is diagnostically helpful to people more than just developers (IT, sysadmins, etc.).
* **Info** - Generally useful information to log (service start/stop, configuration assumptions, etc). Info I want to always have available but usually don't care about under normal circumstances. This is my out-of-the-box config level.
* **Warn** - Anything that can potentially cause application oddities, but for which I am automatically recovering. (Such as switching from a primary to backup server, retrying an operation, missing secondary data, etc.)
* **Error** - Any error which is fatal to the operation, but not the service or application (can't open a required file, missing data, etc.). These errors will force user (administrator, or direct user) intervention. These are usually reserved (in my apps) for incorrect connection strings, missing services, etc. 

[Reference related...](https://stackoverflow.com/questions/2031163/when-to-use-the-different-log-levels)

## SpringBoot Junit Testing Controller
* Create an API controller to be tested.
* Add following dependency to test :
```
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-test</artifactId>
        <scope>test</scope>
    </dependency>
```
* Create a test class :
```
@RunWith(SpringRunner.class)
@SpringBootTest
    public class ApplicationTest {
        @Test
        public void contextLoads() throws Exception {
        }
    }
```
We can use `@WebMvc` or `@MockMvc` also instead of `@SpringBootTest`. 
* Use MockMvc to test your apis. Example : 
    ```
    @RunWith(SpringRunner.class)
    @WebMvcTest(GreetingController.class)
    public class WebMockTest {
    
        @Autowired
        private MockMvc mockMvc;
    
        @MockBean
        private GreetingService service;
    
        @Test
        public void greetingShouldReturnMessageFromService() throws Exception {
            when(service.greet()).thenReturn("Hello Mock");
            this.mockMvc.perform(get("/greeting")).andDo(print()).andExpect(status().isOk())
                    .andExpect(content().string(containsString("Hello Mock")));
        }
    }
    ```
[Reference Spring docs...](https://spring.io/guides/gs/testing-web/)
[Github link for an example module...](https://github.com/namikaze-nitin/springData-rating-daalchini/tree/master/src/test/java/co/nitin/springbootRest/controller)

## Unable to set runtime Local Server Port in Spring Boot Test 
Use [WebEnvironment.DefinedPort](https://stackoverflow.com/questions/43491893/unable-to-set-runtime-local-server-port-in-spring-boot-test-1-5) OR [Follow github Test example](https://github.com/namikaze-nitin/springData-rating-daalchini/blob/master/src/test/java/co/nitin/springbootRest/controller/OrderRequestRatingControllerTest.java)

## What Is a Spring Context?
Spring contexts are also called Spring IoC containers, which are responsible for instantiating, configuring, and assembling beans by reading configuration metadata from XML, Java annotations, and/or Java code in the configuration files.

## Use of @WebMvc and @MockMvc and @SpringBootTest
* `@SpringBootTest` annotation tells Spring Boot to go and look for a main configuration class (one with @SpringBootApplication for instance), and use that to start a Spring application context. SpringBootTest loads complete application and injects all the beans which is can be slow.

* `@WebMvcTest` is used for testing the controller layer and you need to provide remaining dependencies required using Mock Objects.

* Some more annotations available for testing are :
    ```
    @WebMvcTest - for testing the controller layer
    @JsonTest - for testing the JSON marshalling and unmarshalling
    @DataJpaTest - for testing the repository layer
    @RestClientTests - for testing REST clients
    ```
[Official spring docs...](https://spring.io/guides/gs/testing-web/)
[Worth-a-read stackoverflow discussions... ](https://stackoverflow.com/questions/39865596/difference-between-using-mockmvc-with-springboottest-and-using-webmvctest/39869110)

## What testing to use
Which kind of unit testing to use [reference doc...](https://blog.zenika.com/2013/01/15/spring-mvc-test-framework/)

## Testing JSON POST 
[Github example module...](https://github.com/namikaze-nitin/springData-rating-daalchini/blob/master/src/test/java/co/nitin/springbootRest/controller/APIControllerTest.java)
[DZone blog](https://dzone.com/articles/simple-spring-boot-post)
[Example by Nixsmash](https://nixmash.com/post/testing-json-posting-with-spring-boot-jacksontester)
[]()

## Error : SpringBoot test unable to inject MockMvc
* Either use `@WebMvcTest`. OR 
* Who is interested in loading the full application... should try using `@SpringBootTest` combined with `@AutoConfigureMockMvc` rather than the @WebMvcTest.

[Reference stackoverflow discussion...](https://stackoverflow.com/questions/38084872/issue-with-testing-spring-mvc-slice-in-springboot-1-4)

## Github
Using github : [reference link...](https://help.github.com/articles/adding-an-existing-project-to-github-using-the-command-line/)

## Error : Found multiple declarations of @BootstrapWith for test class
### Stack Trace:
```
java.lang.IllegalStateException: Configuration error: found multiple declarations of @BootstrapWith for test class [co.nitin.springbootRest.controller.APIControllerTest]: [@org.springframework.test.context.BootstrapWith(value=class org.springframework.boot.test.context.SpringBootTestContextBootstrapper), @org.springframework.test.context.BootstrapWith(value=class org.springframework.boot.test.autoconfigure.web.servlet.WebMvcTestContextBootstrapper)]
	at org.springframework.test.context.BootstrapUtils.resolveExplicitTestContextBootstrapper(BootstrapUtils.java:155)
	at org.springframework.test.context.BootstrapUtils.resolveTestContextBootstrapper(BootstrapUtils.java:126)
	at org.springframework.test.context.TestContextManager.<init>(TestContextManager.java:105)
	at org.springframework.test.context.junit4.SpringJUnit4ClassRunner.createTestContextManager(SpringJUnit4ClassRunner.java:152)
	at org.springframework.test.context.junit4.SpringJUnit4ClassRunner.<init>(SpringJUnit4ClassRunner.java:143)
	at org.springframework.test.context.junit4.SpringRunner.<init>(SpringRunner.java:49)
	at sun.reflect.NativeConstructorAccessorImpl.newInstance0(Native Method)
	at sun.reflect.NativeConstructorAccessorImpl.newInstance(NativeConstructorAccessorImpl.java:62)
	at sun.reflect.DelegatingConstructorAccessorImpl.newInstance(DelegatingConstructorAccessorImpl.java:45)
	at java.lang.reflect.Constructor.newInstance(Constructor.java:423)
	at org.junit.internal.builders.AnnotatedBuilder.buildRunner(AnnotatedBuilder.java:104)
	at org.junit.internal.builders.AnnotatedBuilder.runnerForClass(AnnotatedBuilder.java:86)
	at org.junit.runners.model.RunnerBuilder.safeRunnerForClass(RunnerBuilder.java:59)
	at org.junit.internal.builders.AllDefaultPossibilitiesBuilder.runnerForClass(AllDefaultPossibilitiesBuilder.java:26)
	at org.junit.runners.model.RunnerBuilder.safeRunnerForClass(RunnerBuilder.java:59)
	at org.junit.internal.requests.ClassRequest.getRunner(ClassRequest.java:33)
	at org.eclipse.jdt.internal.junit4.runner.JUnit4TestLoader.createUnfilteredTest(JUnit4TestLoader.java:87)
	at org.eclipse.jdt.internal.junit4.runner.JUnit4TestLoader.createTest(JUnit4TestLoader.java:73)
	at org.eclipse.jdt.internal.junit4.runner.JUnit4TestLoader.loadTests(JUnit4TestLoader.java:46)
	at org.eclipse.jdt.internal.junit.runner.RemoteTestRunner.runTests(RemoteTestRunner.java:522)
	at org.eclipse.jdt.internal.junit.runner.RemoteTestRunner.runTests(RemoteTestRunner.java:760)
	at org.eclipse.jdt.internal.junit.runner.RemoteTestRunner.run(RemoteTestRunner.java:460)
	at org.eclipse.jdt.internal.junit.runner.RemoteTestRunner.main(RemoteTestRunner.java:206)
```
### Solution
* You have added mulltiple notation on test class that helps in configuration testing. Remove `@SpringBootTest` or `@WebMvcTest` from the class :
```
	@RunWith(SpringRunner.class)
	@SpringBootTest(webEnvironment = WebEnvironment.DEFINED_PORT)
	@WebMvcTest
    public class APIControllerTest
```


## Error : Unsatisfied dependency expressed through field 'mockMvc'
### StackTrace
```
org.springframework.beans.factory.UnsatisfiedDependencyException: Error creating bean with name 'co.nitin.springbootRest.controller.APIControllerTest': Unsatisfied dependency expressed through field 'mockMvc'; nested exception is org.springframework.beans.factory.NoSuchBeanDefinitionException: No qualifying bean of type 'org.springframework.test.web.servlet.MockMvc' available: expected at least 1 bean which qualifies as autowire candidate. Dependency annotations: {@org.springframework.beans.factory.annotation.Autowired(required=true)}
	at org.springframework.beans.factory.annotation.AutowiredAnnotationBeanPostProcessor$AutowiredFieldElement.inject(AutowiredAnnotationBeanPostProcessor.java:588)
	at org.springframework.beans.factory.annotation.InjectionMetadata.inject(InjectionMetadata.java:88)
	at org.springframework.beans.factory.annotation.AutowiredAnnotationBeanPostProcessor.postProcessPropertyValues(AutowiredAnnotationBeanPostProcessor.java:366)
	at org.springframework.beans.factory.support.AbstractAutowireCapableBeanFactory.populateBean(AbstractAutowireCapableBeanFactory.java:1272)
	at org.springframework.beans.factory.support.AbstractAutowireCapableBeanFactory.autowireBeanProperties(AbstractAutowireCapableBeanFactory.java:386)
	at org.springframework.test.context.support.DependencyInjectionTestExecutionListener.injectDependencies(DependencyInjectionTestExecutionListener.java:118)
	at org.springframework.test.context.support.DependencyInjectionTestExecutionListener.prepareTestInstance(DependencyInjectionTestExecutionListener.java:83)
	at org.springframework.boot.test.autoconfigure.SpringBootDependencyInjectionTestExecutionListener.prepareTestInstance(SpringBootDependencyInjectionTestExecutionListener.java:44)
	at org.springframework.test.context.TestContextManager.prepareTestInstance(TestContextManager.java:230)
	at org.springframework.test.context.junit4.SpringJUnit4ClassRunner.createTest(SpringJUnit4ClassRunner.java:228)
	at org.springframework.test.context.junit4.SpringJUnit4ClassRunner$1.runReflectiveCall(SpringJUnit4ClassRunner.java:287)
	at org.junit.internal.runners.model.ReflectiveCallable.run(ReflectiveCallable.java:12)
	at org.springframework.test.context.junit4.SpringJUnit4ClassRunner.methodBlock(SpringJUnit4ClassRunner.java:289)
	at org.springframework.test.context.junit4.SpringJUnit4ClassRunner.runChild(SpringJUnit4ClassRunner.java:247)
	at org.springframework.test.context.junit4.SpringJUnit4ClassRunner.runChild(SpringJUnit4ClassRunner.java:94)
	at org.junit.runners.ParentRunner$3.run(ParentRunner.java:290)
	at org.junit.runners.ParentRunner$1.schedule(ParentRunner.java:71)
	at org.junit.runners.ParentRunner.runChildren(ParentRunner.java:288)
	at org.junit.runners.ParentRunner.access$000(ParentRunner.java:58)
	at org.junit.runners.ParentRunner$2.evaluate(ParentRunner.java:268)
	at org.springframework.test.context.junit4.statements.RunBeforeTestClassCallbacks.evaluate(RunBeforeTestClassCallbacks.java:61)
	at org.springframework.test.context.junit4.statements.RunAfterTestClassCallbacks.evaluate(RunAfterTestClassCallbacks.java:70)
	at org.junit.runners.ParentRunner.run(ParentRunner.java:363)
	at org.springframework.test.context.junit4.SpringJUnit4ClassRunner.run(SpringJUnit4ClassRunner.java:191)
	at org.eclipse.jdt.internal.junit4.runner.JUnit4TestReference.run(JUnit4TestReference.java:86)
	at org.eclipse.jdt.internal.junit.runner.TestExecution.run(TestExecution.java:38)
	at org.eclipse.jdt.internal.junit.runner.RemoteTestRunner.runTests(RemoteTestRunner.java:538)
	at org.eclipse.jdt.internal.junit.runner.RemoteTestRunner.runTests(RemoteTestRunner.java:760)
	at org.eclipse.jdt.internal.junit.runner.RemoteTestRunner.run(RemoteTestRunner.java:460)
	at org.eclipse.jdt.internal.junit.runner.RemoteTestRunner.main(RemoteTestRunner.java:206)
Caused by: org.springframework.beans.factory.NoSuchBeanDefinitionException: No qualifying bean of type 'org.springframework.test.web.servlet.MockMvc' available: expected at least 1 bean which qualifies as autowire candidate. Dependency annotations: {@org.springframework.beans.factory.annotation.Autowired(required=true)}
	at org.springframework.beans.factory.support.DefaultListableBeanFactory.raiseNoMatchingBeanFound(DefaultListableBeanFactory.java:1493)
	at org.springframework.beans.factory.support.DefaultListableBeanFactory.doResolveDependency(DefaultListableBeanFactory.java:1104)
	at org.springframework.beans.factory.support.DefaultListableBeanFactory.resolveDependency(DefaultListableBeanFactory.java:1066)
	at org.springframework.beans.factory.annotation.AutowiredAnnotationBeanPostProcessor$AutowiredFieldElement.inject(AutowiredAnnotationBeanPostProcessor.java:585)
	... 29 more

```

### Solution
Class not annotated to find MockMvc and we have a field of type `MockMvc autowired in class`.
```
@RunWith(SpringRunner.class)
@SpringBootTest(webEnvironment = WebEnvironment.DEFINED_PORT)
public class APIControllerTest {

		@Autowired private MockMvc mockMvc;
}
```
Add : `@SpringBootTest` with `AutoConfigureMockMvc` to test...
```
@RunWith(SpringRunner.class)
@SpringBootTest(webEnvironment = WebEnvironment.DEFINED_PORT)
@AutoConfigureMockMvc
public class APIControllerTest {

		@Autowired private MockMvc mockMvc;
}
```

## EntityManagerFactory vs SessionFactory
* `EntityManagerFactory` is the standard implementation, it is the same across all the implementations. If we migrate our ORM for any other provider, there will not be any change in the approach for handling the transaction. In contrast, if you use hibernate’s session factory, it is tied  to hibernate APIs and ca not migrate to new vendor easily.
* One dis-advantage of using the standard implementation is that, it is not providing the advanced features. There is not much control provided in the EntityManager APIs. Whereas, hibernate’s SessionFactory has lot of advanced features which can not done in JPA. One such thing is retrieving the ID generator without closing the transaction, batch insert, etc.
* We can use entity manger and session factory together. In this approach, entity manage delegates session handling to the hibernate by invoking the unwrap method. Like this:

```Session session = entityManager.unwrap(Session.class);```

Using EntityManagerFactory approach allows us to use callback method annotations like @PrePersist, @PostPersist,@PreUpdate with no extra configuration. Using similar callbacks while using SessionFactory will require extra efforts.

* `Persistence Context` : To be very precise, a persistent context manages a set of entities which in turn is managed by the EntityManager. A persistent context keeps track of the state (or the changes) that an entity object may undergo. And the EntityManager takes the support of this persistence context to commit or to undo the changes. As soon as an EntityManager object is created, it is implicitly associated with a persistence context for managing a set of entities.

## JPA vs Hibernate

## Double quotes in print statement `::`
* This is called method reference and is a syntactical replacement for lamda expressions. Example :
```
	List<Integer> numbers = Arrays.asList(1, 2, 3, 4, 5, 6);
	numbers.forEach(System.out::println);
```
* In java-8 Streams Reducer in simple works is a function which takes two values as input and returns result after some calculation. This result is fed in next iteration. For eg : In case of Math:max function, method keeps returning max of two values passed and in the end you have largest number in hand.
[reference discussion...](https://stackoverflow.com/questions/20001427/double-colon-operator-in-java-8)

## Dates in java
* `java.util.Date` provides date information in java.
* `Date date = new Date()` will create a `Date` object with current Timestamp in it i.e., it will consist of "Date Time" object.
* Example use :
```
    String query = "SELECT v.machineId, MAX(v.updatedTime) FROM MachineHealthDetails v GROUP BY v.machineId";
    List<Object[]> resultSet = em.createQuery(query).getResultList();

    for(Object[] obj : resultSet) {
    	
    	Long id = (Long) obj[0];
    	Date date = (Date) obj[1];
    	Date curDate = new Date ();
    	
    	int diffInMins = (int) ((curDate.getTime()-date.getTime())/1000/60);
    	
    	boolean working = diffInMins <= 2 ? true:false;	    	
    	System.out.println(id + " working? : " + working);
    	System.out.println("Machine with id:" + id 
    			+ "not working since : " + diffInMins/3600
    			+ "days " +  diffInMins/60
    			+ "hrs " + diffInMins%60 + "mins");
    }
```
* Cuurent Date :
```
Date date = new Date();
DateFormat dateFormat = new SimpleDateFormat("hh:mm:ss a");
String formattedDate = dateFormat.format(date);
```

## Job Scheduling in java
* For spring-boot example: 
	* Annotate your initialzer class with `@EnableScheduling` to enable scheduling:
	```
		@SpringBootApplication
		@EnableScheduling
		public class HealthVendingMachineApplication {
			...
		}
	```
	* Create a method annotated with `@Scheduled` to perform a scheduling task:
	```
	@Scheduled(fixedRate = 2000)
	//Or Use @Scheduled(cron='* * * *') Ref-link : https://spring.io/guides/gs/scheduling-tasks/
	public void scheduleTaskWithFixedRate() {
		...
		System.out.println("King in the north... ");
	}
	```

[ref-link](https://spring.io/guides/gs/scheduling-tasks/)

* For Spring-Mvc :
	* Create a configuration class to enable scheduling:
	```
		@Configuration
		@EnableScheduling
		public class SpringConfig {
		   ...
		}
	```
	* Create a component class with methods performing scheduling operations:
	```
		@Component
		public class MyScheduler {
			...
		    @Scheduled(cron = "0 * * * * *")
		    public void myJob() {
				System.out.println("King in the north... ");
		    }
		}
	``` 
	* You can use different fields provided by `@Scheduled` annotation. For eg, to schedule job at exactly x=2 seconds use `@Scheduled(fixedRate = 2000)`.

* For enabling configuration using xml file : [click here...](http://websystique.com/spring/spring-job-scheduling-using-xml-configuration/).
* For other example list and ref, [click here](http://www.quartz-scheduler.org/documentation/quartz-2.x/examples/Example3.html).

* You can modify these calls to support scheduling on different weekdays. For eg, you might not want your scheduling to work on weekends, for that you can modify your cron job as : `@Scheduled(cron="*/5 * * * * MON-FRI")`

>In essence, if your goal is to implement a quick and basic form of job/task scheduling then Spring Scheduler will be ideal. On the other hand, if you need clustering as well as JobPersistence support then Quartz may serve better.

[Ref-Link for Spring schuling vs Quartz scheduling...](https://stackoverflow.com/questions/38564101/difference-between-quartz-job-and-scheduling-tasks-with-spring)

## Import SQL table in database

## Try springboot API requests using @RequestParam
https://stackoverflow.com/questions/28039709/what-is-difference-between-requestbody-and-requestparam

## ERROR : Java compiler level does not match the version of the installed Java project facet.

### Solution
* This mainly happens due to difference in versions of the project build and one that eclipse is using.
* Steps :
	* Right Click on project 
	* Select Properties
	* Search for Project facets
	* Goto java sub-tab
	* Set java version to one which your eclipse is using inside `pom.xml` (if using maven)

> This problem mainly arises when there is conflict in parent-project version of java and the one that eclipse/your project is using. just set pom dependency to the one that parent is using :
```
    <plugin>
      <artifactId>maven-compiler-plugin</artifactId>
        <configuration>
          <source>1.6</source>
          <target>1.6</target>
        </configuration>
    </plugin>
```

## cURL Request in Java
* cURL stands for clients for URL.
* For sending and recieving data/file using URL requests.
* In java it can be done in many ways. Mentioning using `HttpURLConnection` here :
	* Create a `URL` :
	```
		URL url = new URL("https://jsonplaceholder.typicode.com/posts");
	```
	* Create a `HttpUrlConnection` annd add headers to it:
	```
		HttpURLConnection conn = (HttpURLConnection)url.openConnection();
		conn.setDoOutput(true);
		conn.setRequestProperty("content-type", "application/json");
		conn.setUseCaches(false);
		conn.setRequestMethod("POST");
	```
	* `OutputStreamReader` to request it post the url:
	```
		OutputStreamWriter out = new OutputStreamWriter(conn.getOutputStream());
		out.write(data);
		out.close();
	```
	* `BufferedReader` and `InputStreamReader` to fetch the response:
	```
		BufferedReader reader = new BufferedReader(new InputStreamReader(conn.getInputStream()));		
		StringBuilder builder = new StringBuilder();
		while(true) {
			String line = reader.readLine();
			if(line == null)break;
			builder.append(line);
		}
		System.out.println(builder.toString());
	```

> In order to be sure about your json structure and request, first copy it into POSTMAN `import menu > post raw text > click import button`.
> This will give you structure for JSON, moreover will tell you about headers and content-type details.

## Maven War plugin error
### Stack Trace 
* Showed that un-met dependency or some sort of error in `maven-war-plugin`

### Solution
* Update maven dependency to latest one.

## Removing unused imports in eclipse
* Eclipse IDE will show a yellow underline for all those imports at the top of our class and message is “The import xxx is never used“.
* To remove those unused imports automatically, just click on the class and press the shortcut `Ctrl + Shift + O` to initilize the `Organize imports` feature. 

## How to enable automatic formatting and cleanup

By default automatic formatting and cleanup is disabled. To enable it, do the following:

* Go to Window > Preferences > Java > Editor > Save Actions.
* Select Perform the selected actions on save.
* Select Format Source code. Make sure Format all lines is selected. Below I discuss when to use the other option.
* Make sure Organize imports is selected.
* Select Additional actions.
* Click Ok, edit some code, save it and watch Eclipse format it automatically.

## Use of ThreadPoolTaskExecutor

## Maven with Multiple projects
[Reference link](https://books.sonatype.com/mvnex-book/reference/multimodule-sect-building-multimodule.html)

## Java @override annotation
https://www.journaldev.com/817/java-override-annotation

## @Component vs @Convfiguration
http://dimafeng.com/2015/08/29/spring-configuration_vs_component/

## @RequestParam vs @PathVariable
https://javabeat.net/spring-mvc-requestparam-pathvariable/

## Git Use
### Branch on master
#### Create a branch
* Will create a new branch and `checkout` that created branch : `git checkout -b <branch-name>`

* This branch is currently created in your local repo. In order to make this branch in remote repo, you need to create/add some files to this branch and then push it in. 
```
 gedit README.md
```
#### Commit
* Before making any commit you need to stage those changes : `git add .`
* Now commit these changes to your local repo : `git commit -m "put some not-so-stupid comment here explaining the changes that you did"`.
* Now push it in.


#### Deleting a branch
* If only single branch is present, we need to create a new branch and from this branch then delete that branch.
`git checkout -b <branch-name>`
* Problem may-be that my new branch has base as "branch-to-delete" and it wont allow me to delete the current branch. Which resulted in : my head-branch being the branch that I want to delete... weird han!!!
* Need to change my default branch(master branch from settings using UI).
* And then `git push origin --delete <branch-name>`

#### Push and pull
* While pushing/pulling make sure you push into your branch only
`git push/pull origin <branch-name>`.
* Mentioning branch name is a good practice to be on safe side.

#### Fetch and Checkout
* In order to fetch all the branches from repo : `git fetch`.
* Now, in order to use a particular branch : `git checkout <branch-name>`

[Reference for common git commmands](https://gist.github.com/hofmannsven/6814451)

## Maven files
* These files are from Maven wrapper. It works similarly to the Gradle wrapper.

* This allows you to run the Maven project without having Maven installed and present on the path. It downloads the correct Maven version if it's not found (as far as I know by default in your user home directory).

* The `mvnw` file is for Linux (bash) and the `mvnw.cmd` is for Windows environment.

## Different quering methods
https://www.petrikainulainen.net/programming/spring-framework/spring-data-jpa-tutorial-creating-database-queries-with-the-query-annotation/

## @Autowire vs @PersistenceContext for EntityManager
https://stackoverflow.com/questions/31335211/autowired-vs-persistencecontext-for-entitymanager-bean