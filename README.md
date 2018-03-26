
# Workshop for implementing/migrating Java web application on AWS with microservce, CICD and DevSecsOps

<hr>

## 0. Preparation
##### 1. Install all required SDK, packages in your dev environment
- Java SDK 8, Git client, Maven (3.5.3)
- Eclipse Oxygen 2
- AWS plugin for Eclipse 
- AWS CLI in your development environment

The installation time generally takes 10~30mins. Attendees should prepare all installations for their developing environment before starting this workshop.

<hr>

## 1. Module-01 :  Web application - Building a first web application with Spring Boot (time duration : 30 mins)
- This is a simple web application using Spring Boot and MySQL
- Check module-01 application and run this application to check the application structure and it's execution.

##### 1. Download source codes 
```
git clone https://github.com/aws-asean-builders/aws-java-spring-dev-workshop
```

##### 2. Configure environment.
- Setup MySQL and configure username/password
- install MySQL in your development environment, it depends on your dev OS, for example MacOs or Linux
- After installataion, create database and configure user name and password, for example (demouser/12345678)

```
## create database, user and it's privilege

mysql> create database workshop; -- Create the new database
mysql> create user 'demouser'@'localhost' identified by '12345678'; -- Creates the user
mysql> grant all on workshop.* to 'demouser'@'%'; -- Gives all the privileges to the new user on the newly created 


## crate User table

CREATE TABLE `User` (
  `id` integer NOT NULL AUTO_INCREMENT,
  `name` varchar(255) DEFAULT NULL,
  `email` varchar(255) DEFAULT NULL,
  PRIMARY KEY (`id`)
) ENGINE=InnoDB AUTO_INCREMENT=4 DEFAULT CHARSET=utf8 
```

##### 3. Check the structure of application
- Check **application.properties** and **spring.factories** in META-INF of resource folder. This file is for **CustomConfigListner.java** to change the environment configuration using Configuration Listner
- Check package structure, boot, controller, model, repository
- UserRepository is for the JPA 
- Check the **pom.xml**, it contains Spring Boot, JPA, MySQL, Thymeleaf

```
  <dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-jpa</artifactId>
  </dependency>    
  <dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
  </dependency>
  <dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-test</artifactId>
    <scope>test</scope>
  </dependency>
  <!-- thymeleaf-->  
  <dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-thymeleaf</artifactId>
  </dependency>     
  <dependency>
    <groupId>org.webjars</groupId>
    <artifactId>bootstrap</artifactId>
    <version>3.3.7-1</version>
  </dependency>			    		     
  <!-- Use MySQL Connector -->
  <dependency>
    <groupId>mysql</groupId>
    <artifactId>mysql-connector-java</artifactId>
  </dependency> 
 ```
##### 4. Run your test codes
Run MainControllerTest with JUnit Runner and check the console output and it's result. if you get a error messages then take a look at how to fix the problem.
We have 2 kinds of unit test, one is mock test, the other is integration test, please check 2 files in test folder.


##### 5. Test app using 'curl' for API
Launch your application in your Eclipse IDE and run 'curl' command like below

```
curl 'localhost:8080/workshop/deleteall'
curl 'localhost:8080/workshop/add?name=First&email=ex1@gmail.com'
curl 'localhost:8080/workshop/all'
```

##### 6. Run web page localhost:8080/index.html
- Run CRUD for User data
- see user list, add/update/delete user

##### Appedix. create a Spring Boot project from scratch
Please check this blog for creating a spring boot project from scratch using Maven. 
[add later]

##### run maven
```
mvn package && java -jar target/module-01-0.1.0.jar
```

<hr>

## Module-02 : First use of AWS service(Parameter Store) and it's integration (time duration : 40 mins)
- This module is creating CustomListner for retrieving environment parameters from Parameter Store in AWS System Manager. 
- There are many environment paramenters in Spring Data applications, for example, database connection URL, database user name, password or AWS access key and secret key, and there are described in application.properties, generally. the more secured way to retrieve these information is required. 
- Start from moudle-01 and complete the codes with below information.
- module-02 is a starting points to use AWS services with AWS Java SDK.
 
##### 1 add packages in pom.xml

```
  <dependencyManagement>
    <dependencies>
      <dependency>
        <groupId>com.amazonaws</groupId>
        <artifactId>aws-java-sdk-bom</artifactId>
        <version>1.11.289</version>
        <type>pom</type>
        <scope>import</scope>
      </dependency>
    </dependencies>
  </dependencyManagement>    
    
  <!-- AWS SDK System Manager -->  
  <dependency>
    <groupId>com.amazonaws</groupId>
    <artifactId>aws-java-sdk-ssm</artifactId>
  </dependency> 
  <dependency>
    <groupId>com.amazonaws</groupId>
    <artifactId>aws-java-sdk-s3</artifactId>
  </dependency>
  <dependency>
    <groupId>com.amazonaws</groupId>
    <artifactId>aws-java-sdk-dynamodb</artifactId>
  </dependency>
   
    
  <!-- AWS SDK rekognition -->  
	<dependency>
	  <groupId>com.amazonaws</groupId>
	  <artifactId>aws-java-sdk-rekognition</artifactId>
	</dependency>  
	<!-- AWS SDK translate -->  
	 <dependency>
	  <groupId>com.amazonaws</groupId>
	  <artifactId>aws-java-sdk-translate</artifactId>
	</dependency>            

```
##### 2. Configure AWS CLI to allow application to get access key and secret key
```
> aws configure
> AWS Access Key ID [None]: [your key]
> AWS Secret Access Key [None]: [your key]
```

##### 3. Configuration properties programmatically
- Create CustomConfigListner.java using following information 
- [spring boot application properites](https://stackoverflow.com/questions/29072628/how-to-override-spring-boot-application-properties-programmatically)
- [springboot prior to startup](https://stackoverflow.com/questions/33072452/log-configurationproperties-in-springboot-prior-to-startup)
	
- Create CustomConfigListner.java in hello package
- Create **src/main/resources/META-INF/spring.factories** and register above class in it

```
org.springframework.context.ApplicationListener=hello.CustomConfigListner
```

##### 4. Configure ParameterStore in System Manager 
AWS Systems Manager Parameter Store provides secure, hierarchical storage for configuration data management and secrets management. You can store data such as passwords, database strings, and license codes as parameter values.
Complete the following tasks to configure application parameters for ParameterStore (default region is us-east-1)

	1. Open the Amazon EC2 console at https://console.aws.amazon.com/ec2/
	2. Create parameters in ParameterStore for database URL, database username and password

![Parameter Store](./images/module-02/paramter-store-01.png)


##### 5. Check the availability of parameters in ParameterStore
- Run ParameterStoreTest.java 


##### 6. Modify CustomConfigListner.java 
- Integrate with your parameters in System Manager.
- Add additional parameters you need in your application


##### 7. Check features using unit tests
- Modify Test classes
- Run CustomConfigTest

##### 8. For next implementation
- Please check classes in hello.logics and unit test in hello.logics

<hr>

## Module 3 : Using AWS services (time duration : 30 mins)
From this module, we are beginning to develop application using AWS services.
We will complete the following tasks.
- Resize a file and save it to local folder
- Upload a file to S3 using AWS SDK
- Retrieve information from picture using Amazon Rekognition and Translate text using Amazon Translate
- Change database from Mysql to Aurora for Mysql 
- Store a file meta data to DynamoDB

**Start from the module-02**

#### 1. references
Please refer the following information to complete the tasks
 
[Develop S3](https://docs.aws.amazon.com/sdk-for-java/v1/developer-guide/examples-s3-objects.html#upload-object)

[Develop Rekognition](https://docs.aws.amazon.com/rekognition/latest/dg/get-started-exercise.html)

[Develop Translate](https://docs.aws.amazon.com/translate/latest/dg/examples-java.html)

#### 2. Basic structure to use AWS services
	1. Declare a client for the services to call
	2. Initialzie a client with various information

for example

```

AmazonTranslate translate = AmazonTranslateClientBuilder
							.standard()
							.withRegion(region)   // set region
							.withCredentials(new AWSStaticCredentialsProvider(credentials)) //set credentials
							.build();

```

##### 3. Implement logics
- Check AWSAIServicesTest and complete AWSAIServices.java, S3FileTransfer.java based on Unit Test
- Create S3 file transferring and translate 

##### 4. Implement DynamoDB 
- Check DynamoDBTest
- Complete the tasks to implement the DDB logics in Unit Test (not logics)

#### 5. Change the database from local MySQL to Aurora MySQL

	1. Open the Amazon RDS console : https://console.aws.amazon.com/rds/home?region=us-east-1#
	2. Select Aurora for MySQL 5.7 Database engine 
	3. Create a DB instance configuring databasename, username, password.
	
![Parameter Store](./images/module-03/01.png)

	4. Wait until Aurora for MySQL launching
	5. Change parameter values in Parameter Store in EC2 to Aurora instance

#### 6. Test a service
Test a IntegratedTransTest.java and Implement a IntegratedTrans.java

<hr>

## Module-04 : Using multiple repositories using Spring Data (time durations : 30 mins)
In this module, we will learn how to configure the multiple repositories of Aurora and DynamoDB using Spring Data.
- Create configuration files for DynamoDB and MySQL DB. 
- Create Model classes for each database (User for MySQL, PhotoInfo for DynamoDB)
- Create a model, repository packages and change Model and Repository package path to distinguish it in two repositories 
- Change application.properties
- Test 2 repositories with Unit Test codes

##### 1. Add Spring Data for Database
We arg going to use Spring Data for DynamoDB 

reference : [derjust's Github](https://github.com/derjust/spring-data-dynamodb)

Add it to Pom.xml

```
  <!-- spring-data-dynamo-db -->
  <dependency>
      <groupId>com.github.derjust</groupId>
      <artifactId>spring-data-dynamodb</artifactId>
      <version>4.5.0</version>
  </dependency> 
		    
```

##### 2. Implement DB configuration classes
reference : [dynamodb put item request](https://github.com/aws-samples/aws-dynamodb-examples/blob/master/src/main/java/com/amazonaws/codesamples/lowlevel/LowLevelItemCRUDExample.java)

We need to create configuration class for MySQL and DynamoDB to use each repository
Here is MysqlDBConfig example.

```
@Configuration
@EnableTransactionManagement
@EnableJpaRepositories(
		entityManagerFactoryRef = "mysqlEntityManager", 
		transactionManagerRef = "mysqlTransactionManager", 
		basePackages = "hello.repository.mysql"
)
public class MysqlDBConfig {
	
	@Autowired
	public ConfigurableEnvironment environment;
	
	/**
	 * MySQL datasource definition.
	 * 
	 * @return datasource.
	 */
	@Bean
	@ConfigurationProperties(prefix = "spring.mysql.datasource")
	public DataSource mysqlDataSource() {
		System.out.println("##### DataSource called");
		DataSource ds = DataSourceBuilder.create().build();
		System.out.println("##### DataSource url = " + environment.getProperty("spring.mysql.datasource.url").toString());
		return ds ;
	}
 
	/**
	 * Entity manager definition. 
	 *  
	 * @param builder an EntityManagerFactoryBuilder.
	 * @return LocalContainerEntityManagerFactoryBean.
	 */
	@Bean(name = "mysqlEntityManager")
	public LocalContainerEntityManagerFactoryBean mysqlEntityManagerFactory(EntityManagerFactoryBuilder builder) {
		return builder
					.dataSource(mysqlDataSource())
					.properties(hibernateProperties())
					.packages(User.class)
					.persistenceUnit("mysqlPU")
					.build();
	}
 
	/**
	 * @param entityManagerFactory
	 * @return
	 */
	@Bean(name = "mysqlTransactionManager")
	public PlatformTransactionManager mysqlTransactionManager(@Qualifier("mysqlEntityManager") EntityManagerFactory entityManagerFactory) {
		return new JpaTransactionManager(entityManagerFactory);
	}
 
	private Map<String, Object> hibernateProperties() {
 
		Resource resource = new ClassPathResource("hibernate.properties");
		try {
			Properties properties = PropertiesLoaderUtils.loadProperties(resource);
			return properties.entrySet().stream()
											.collect(Collectors.toMap(
														e -> e.getKey().toString(),
														e -> e.getValue())
													);
		} catch (IOException e) {
			return new HashMap<String, Object>();
		}
	}

```
Change **spring.datasource** properties to **spring.mysql.datasource** in CustomConfigListner.java

```
ConfigurableEnvironment environment = event.getEnvironment();
Properties props = new Properties();
props.put("spring.mysql.jpa.hibernate.ddl-auto", "update");
props.put("spring.mysql.datasource.url", url);
props.put("spring.mysql.datasource.username", username);
props.put("spring.mysql.datasource.password", password);
props.put("spring.mysql.datasource.driver-class-name", "com.mysql.jdbc.Driver");
environment.getPropertySources().addFirst(new PropertiesPropertySource("myProps", props));

```
These new application properties will be used in this module.

##### 3 Separate Model classes into different package.
hello.model.mysql.User
hello.model.ddb.PhotoInfo

##### 4. Create a test code
Create PhotoInfoTest in hello.repository(test)

```
@Autowired
PhotoInfoRepository repository;

@Test
public void sampleTestCase() {
  repository.deleteAll();
  PhotoInfo p = new PhotoInfo("a.jpeg", "hello", "hallo");	
  repository.save(p);
  
  List<PhotoInfo> result2 = (List<PhotoInfo>) repository.findAll(); 
  
  assertTrue("Not empty", result2.size() > 0);
}
```

<hr>

##  Module-05 : Change Logics to Lambda (time durations : 40 mins)  
This module requires a knowledge session for StepFunction service.
Start from module-05-begin

##### reference
[Invoking AWS Lambda Functions from Java](https://aws.amazon.com/blogs/developer/invoking-aws-lambda-functions-from-java/)


##### 1. Create a first Lambda project
Create a Lambda project using AWS Eclipse plugin
	
	1. Open AWS Explorer in your Eclipse
	2. Right click on AWS Lambda and create a new Lambda Project "module-05-lambda-custom"
	3. Select "Custom Event" and create a project

![create a Lambda project](./images/module-05/01.png)

	4. Before uploading a Lambda function, you need to create a Role for your Lambda Function.
	5. Upload function to "US-EAST-1" name as "My-Custom-Function" (choose a Role created step 4)

![create a Lambda project](./images/module-05/02.png)

	6. Test a Lambda function in local and on AWS


##### 2. Create a Lambda Invoking Classes
To invoke this function from Java code, we’ll first define POJOs representing the input and output JSON
CustomEventInput.java and CustomEventOutput.java
You must implement a constructor in CustomEventOutput

```
public class CustomEventInput {
  private List<Integer> values;
  public List<Integer> getValues() {
      return values;
  }
  public void setValues(List<Integer> values) {
      this.values = values;
  }
}

public class CustomEventOutput {
	public CustomEventOutput()  // must 
	{}
  private Integer value;
  public CustomEventOutput(int value) {
      setValue(value);
  }
  public Integer getValue() {
      return value;
  }
  public void setValue(Integer value) {
      this.value = value;
  }
    
```
##### 3. Define Lamdba Invoking service interface
Define an interface representing our microservice, and annotate it with the name of the Lambda function to invoke when it’s called

```
public interface MyLambdaServices {
	@LambdaFunction(functionName="MyCustomFunc")
	CustomEventOutput myCustumFunc(CustomEventInput input);
}
```
##### 4. Create Unit test code
We invoke our service using this unit test code;

```
@Test
public void callCustomLamdba()
{
  
  final MyLambdaServices myService = LambdaInvokerFactory.builder()
  		 .lambdaClient(AWSLambdaClientBuilder.defaultClient())
  		 .build(MyLambdaServices.class);
  
  CustomEventInput input = new CustomEventInput();
  List<Integer> list = new ArrayList();
  list.add(1);
		list.add(5);
		input.setValues(list);

  CustomEventOutput output = myService.myCustumFunc(input);  
  assertEquals((int)output.getValue(), (int)5);
  
}

```

##### 5. implement 3 Lambda functions
We implement 3 Lambda functions for DynamoDB, Rekognition and Translate. This Lambda function will replace AWSAIServices.java
 
- Retrieve information from images
- Translate text
- Save image meta information to DDB

Create model class for each service in *hello.aws.lambda.io* package

Here is one Lambda function example (DynamoDB)

```
  @Override
  public DDBEventOutput handleRequest(DDBEventInput input, Context context) {
  		this.initDynamoDbClient();
  		DDBEventOutput customEventOutput = new DDBEventOutput();
  		try {
  			PutItemResult result = persistData(input);
  			
  			customEventOutput.setResult("SUCCESS");
	  		customEventOutput.setMessage(result.toString());
	  		
  		} catch (Exception e)
  		{
  			customEventOutput.setResult("FAIL : error!");
  			customEventOutput.setMessage(e.getMessage());

  		}
  		return customEventOutput;
 
  	}
    	
  private PutItemResult persistData(DDBEventInput input) throws ConditionalCheckFailedException {
    
    Map<String, AttributeValue> item1 = new HashMap<String, AttributeValue>();
    item1.put("id", new AttributeValue().withS(UUID.randomUUID().toString()));
    item1.put("bucket", new AttributeValue().withS(input.getPrefix()));
    item1.put("prefix", new AttributeValue().withS(input.getPrefix()));
    item1.put("text", new AttributeValue().withS(input.getText()));
    item1.put("translated", new AttributeValue().withS(input.getTranslated()));
  	
 		PutItemRequest request = new PutItemRequest().withTableName(TABLE_NAME).withItem(item1);
 		PutItemResult output = amazonDynamoDB.putItem(request);
 		return output;
  }
     
  private void initDynamoDbClient() {
  		amazonDynamoDB = AmazonDynamoDBClientBuilder.standard().withRegion(region).build();
  } 
```

We need to change **MyLambdaServices** to add 3 implemented Lambda services

```
@LambdaFunction(functionName="MyFunction-workshop-dynamodb")
DDBEventOutput myDynamoDBFunc(DDBEventInput input);	
@LambdaFunction(functionName="MyFunction-workshop-rekognition")
RekoEventOutput myRekognitionFunc(RekoEventInput input);
@LambdaFunction(functionName="MyFunction-workshop-translate")
TransEventOutput myTranslateFunc(TransEventInput input);	
```
##### 6. Upload 3 Lambda Services
1. Upload 3 functions from a lambda-custom-project
2. Select a handler classs to upload like a below image
![Upload Lambda function](./images/module-05/03.png)


##### 7. Create a Test Code
This is a code snippet for testing Lambda function

```
@Test
public void callRekognitionLamdba()
{
	final MyLambdaServices myService = LambdaInvokerFactory.builder()
	 		 .lambdaClient(AWSLambdaClientBuilder.defaultClient())
	 		 .build(MyLambdaServices.class);
	 
	RekoEventInput input = new RekoEventInput();
	
	input.setBucket("seon-virginia-2016");
	input.setPath("images/a.jpeg");
	 
	RekoEventOutput output = myService.myRekognitionFunc(input);  
	assertNotNull(output.getText());
}	
```
Run a unit test 

##### 8. implement a services to call Lambda
Create **IntegratedTransLambda** in *hello.logics package*
Crate a unit test for IntegratedTransLambda

```
	@Autowired
	IntegratedTransLambda tr;
	
	@Test
	public void testRetrieveAndSave()
	{
		String result = tr.RetrieveAndSave(bucket, photoPath, region);
		assertEquals(result, "SUCCESS");
	}

```

<hr>

## module-06 : Create StepFunction and use a Stepfucntion in your application (20 mins)
This module need a knowledge session delivery of StepFunction 
##### Reference

[Create a Step function](https://docs.aws.amazon.com/step-functions/latest/dg/tutorial-lambda-state-machine-cloudformation.html)
[Call a StepFunction] (https://aws.amazon.com/blogs/developer/stepfunctions-fluent-api/)

##### 1. Change a Input/Output classes of Lambda Function
Create a common Model class (StepEventInput, StepEventOutout)

StepEventInput.java (omit getter/setter)

```
	private String id;
	private String bucket;
	private String prefix;
	private String text;
	private String translated;
	private String sourceLangCode;
	private String targetLangCode;
```

StepEventOutput.java (omit getter/setter)

```
	private String text;
	private String error_message;
```
	
 
##### 2. Create a StepFunction using JSON
Create a step-function.json in *main/resources/aws*

```
{
  "Comment" : "Machine learning execution with spot instance",
  "StartAt" : "RetrieveInfoFromPhotoUsingRekognition",
  "States"  : {
    "RetrieveInfoFromPhotoUsingRekognition": {
      "Type"      : "Task",
      "Resource"  : "arn:aws:lambda:us-east-1:550622896891:function:MyFunction-workshop-rekognition",
      "Retry" : [ {"ErrorEquals":["HandledError"], "MaxAttempts":3} ],
      "Next"      : "TransInfoUsingTranlate"
    }, 
    "TransInfoUsingTranlate": {
      "Type"      : "Task",
      "Resource"  : "arn:aws:lambda:us-east-1:550622896891:function:MyFunction-workshop-translate",
      "Next"      : "StoreTransDataIntoDynamoDB"
    },
    "StoreTransDataIntoDynamoDB": {
      "Type"      : "Task",
      "Resource"  : "arn:aws:lambda:us-east-1:550622896891:function:MyFunction-workshop-dynamodb",    
      "Retry" : [ {"ErrorEquals":["HandledError"], "MaxAttempts":3} ],
      "End": true
  		}
  	}
}

```

Create a input file in same folder (step-input.json)

```
{
  "bucket":"seon-virginia-2016", 
  "prefix":"images/a.jpeg",
  "text" : "Hello, hello",
  "translated" : "",
  "sourceLangCode" :"en",
  "targetLangCode" : "es"
}
```

##### 3. Implement a test code
Create StepFunctionTest in *hello.aws.stepfunction*

```
final AWSStepFunctions stepFunctionclient = AWSStepFunctionsClientBuilder.defaultClient();

URL inputFile = Application.class.getResource("/aws/step-input.json");
String input = jsonFileRead(inputFile);
StartExecutionRequest startExecutionRequest 
= new StartExecutionRequest()
.withInput(input)
.withStateMachineArn("arn:aws:states:us-east-1:5591:stateMachine:workshop-stepfunction").withSdkRequestTimeout(30000);

StartExecutionResult executionResult = stepFunctionclient.startExecution(startExecutionRequest);
```
Test a code and check the result.

<hr>

## Module-07 Add X-Ray

##### reference

[X-Ray SDK](https://docs.aws.amazon.com/xray/latest/devguide/xray-sdk-java.html)

##### 1. Setup X-Ray daemon for local and server
The AWS X-Ray daemon is a software application that listens for traffic on UDP port 2000, gathers raw segment data, and relays it to the AWS X-Ray API. The daemon works in conjunction with the AWS X-Ray SDKs and must be running so that data sent by the SDKs can reach the X-Ray service. 
[install X-Ray daemon](https://docs.aws.amazon.com/xray/latest/devguide/xray-daemon.html)

	1 download X-Ray dsaemon 
	2 run daemon (if it is MacOS, then command like a below)
	
```
/xray_mac -o -n us-east-1 & (for example)
```

##### 2. Add packages in pom.xml

```
	<dependencyManagement>
	  <dependencies>
...		
	    <!-- xray -->
	    <dependency>
	      <groupId>com.amazonaws</groupId>
	      <artifactId>aws-xray-recorder-sdk-bom</artifactId>
	      <version>1.3.1</version>
	      <type>pom</type>
	      <scope>import</scope>
	    </dependency>			        	    	    
	  </dependencies>
	</dependencyManagement> 
	
    <!-- AWS SDK xray -->        
		<dependency>
	    <groupId>com.amazonaws</groupId>
	    <artifactId>aws-xray-recorder-sdk-core</artifactId>
	  </dependency>
	  <dependency>
	    <groupId>com.amazonaws</groupId>
	    <artifactId>aws-xray-recorder-sdk-apache-http</artifactId>
	  </dependency>
	  <dependency>
	    <groupId>com.amazonaws</groupId>
	    <artifactId>aws-xray-recorder-sdk-aws-sdk</artifactId>
	  </dependency>
	  <dependency>
	    <groupId>com.amazonaws</groupId>
	    <artifactId>aws-xray-recorder-sdk-aws-sdk-instrumentor</artifactId>
	  </dependency>
	  <dependency>
	    <groupId>com.amazonaws</groupId>
	    <artifactId>aws-xray-recorder-sdk-sql-postgres</artifactId>
	  </dependency>
	  <dependency>
	    <groupId>com.amazonaws</groupId>
	    <artifactId>aws-xray-recorder-sdk-sql-mysql</artifactId>
	  </dependency>  	
	
```

##### Add X-Ray configuration 
[X-Ray Java configuration](https://docs.aws.amazon.com/xray/latest/devguide/xray-sdk-java-configuration.html)
The X-Ray SDK for Java provides a class named AWSXRay that provides the global recorder, a TracingHandler that you can use to instrument your code. You can configure the global recorder to customize the AWSXRayServletFilter that creates segments for incoming HTTP calls.

```
@Configuration
public class XRayConfig {
  private static final Logger logger = LoggerFactory.getLogger(XRayConfig.class);

  @Bean
  public Filter TracingFilter() {
    return new AWSXRayServletFilter("Workshop");
  }

  @Bean
  public Filter SimpleCORSFilter() {
    return new SimpleCORSFilter();
  }

  static {
  	 System.out.println("\n##### Webconfig.java static AWSXRay ######################\n");
    AWSXRayRecorderBuilder builder = AWSXRayRecorderBuilder.standard().withPlugin(new EC2Plugin());

    URL ruleFile = XRayConfig.class.getResource("/sampling-rules.json");
    builder.withSamplingStrategy(new LocalizedSamplingStrategy(ruleFile));

    AWSXRay.setGlobalRecorder(builder.build());
  }
}
```

##### 2. Add segment to CustomConfigListner
Here is a tips for CustomConfigListner. 
CustomConfigListner is called before executing XRayConfig, this means we need to embed codes CustomConfigListner for prevent from errors.

```
public void onApplicationEvent(ApplicationEnvironmentPreparedEvent event) {
	
	AWSXRay.beginSegment("Workshop : Load ParameterStore");
	
	....
	
	AWSXRay.endSegment();
}		
		
```

##### 3. Configure X-Ray for SQL Queries
Instrument SQL database queries by adding the X-Ray SDK for Java JDBC interceptor to your data source configuration.

- PostgreSQL – com.amazonaws.xray.sql.postgres.TracingInterceptor
- MySQL – com.amazonaws.xray.sql.mysql.TracingInterceptor

So, change codes in CustomConfigListner

```
props.put("spring.mysql.datasource.driver-class-name", "com.mysql.jdbc.Driver");
// for X-Ray
props.put("spring.mysql.datasource.jdbc-interceptors", "com.amazonaws.xray.sql.mysql.TracingInterceptor");   
environment.getPropertySources().addFirst(new PropertiesPropertySource("myProps", props));
```

##### Add code in Client build
To instrument individual clients, remove the aws-sdk-instrumentor submodule from your build and add an XRayClient as a TracingHandler on your AWS SDK client using the service's client builder. 

```
AmazonRekognition rekognitionClient = AmazonRekognitionClientBuilder
	.standard()
	.withRequestHandlers(new TracingHandler(AWSXRay.getGlobalRecorder()))  // X-Ray
	.withRegion(region)
	.build();

AmazonTranslate translate = AmazonTranslateClientBuilder
	.standard()
	.withRegion(region)
	.withRequestHandlers(new TracingHandler(AWSXRay.getGlobalRecorder())) // X-Ray
	.build();	  	         
	  	         
```
 
##### 5. Channge UnitTest code
To run unit tests, we need to add X-Ray Segment to generate segment to trace, for example.

```
public class MySqlTest {
	
	@Autowired
	UserRepository repository;
  
	@Test
	public void test () {
		
		AWSXRay.beginSegment("MySQLTest test"); 
		
		repository.deleteAll();
		
		...
		
    AWSXRay.endSegment();
	}
```

<hr>

## Module-08 Create a docker and CI/CD for first Deployment on AWS

##### 1. Create a CodeStart project

	1. Select a project template (Java, Webapplication with Spring in EC2)
![Select a Template](./images/module-08/01.png)
	
	2. Select CodeCommit as a code repository 
![Select CodeCommit](./images/module-08/02.png)	
	
	3. Choose your key pair
	4. Choose Cloud9 to edit your code 
![Select CodeCommit](./images/module-08/03.png)		

	5. Select a instance type and launch the project
	6. After creating the project, launch a Cloud9 IDE
	
![Select CodeCommit](./images/module-08/04.png)		

##### 2. Open Cloud9 and configure the dev environtment
	1. Open a Cloud9 IDE and check it's first application in folder
![Cloud9](./images/module-08/05.png)	

	2. check java --version
	3. upgrade java version to 1.8
	

```
sudo yum list available java\*      # check avaiable java version
sudo yum install java-1.8.0 java-1.8.0-openjdk-devel        # install 1.8 java and javac
sudo yum remove java-1.7.0-openjdk  # remove 1.7
java -version											# check java version
```
	4. update JAVA_HOME in the environment variable in .bashrc


```
vi ~/.bashrc
### add follwing content
export JAVA_HOME=/usr/
```
	
	4. Install Maven

```
$ cd /usr/local
$ sudo wget http://www-eu.apache.org/dist/maven/maven-3/3.5.3/binaries/apache-maven-3.5.3-bin.tar.gz
$ sudo tar xzf apache-maven-3.5.3-bin.tar.gz
$ sudo ln -s apache-maven-3.5.3  maven

$ sudo vi /etc/profile.d/maven.sh

# add following content.
export M2_HOME=/usr/local/maven
export PATH=${M2_HOME}/bin:${PATH}

# load the environment variables in current shell using following command.
source /etc/profile.d/maven.sh

# check the loaded environment variables  
echo $PATH             
```

##### 3. first fetching source codes from CodeComnit
	1. Perform tasks following the instruction in CodeCommit for the first application
	2. Run following command

	mvn -f pom.xml compile
	mvn -f pom.xml package
	
	3. If you want to run this application, then copy the built application to the Tomcat webapp directory that you configured in your local machine or ec2 instance on AWS
	
##### 4. download this first application on your local Eclipse IDE
	1. open CodeCommit and copy Clone URL
	2. fetch source codes.
	3. if you don't have a CodeCommit username and password, please refer this documentation :
	https://docs.aws.amazon.com/codecommit/latest/userguide/setting-up-gc.html
	4. import this project to your Eclipse IDE
	5. Run follwing commands

```
mvn -f pom.xml compile
mvn -f pom.xml package
```
		
	6. if you got a following errors,
	
	7. add following content in pom.xml
	<properties>
		...
		<maven.compiler.source>1.8</maven.compiler.source>
		<maven.compiler.target>1.8</maven.compiler.target>
	</properties>
	8. check the result of compilation
	
![Eclipse Java Compiler](./images/module-08/06.png)	



##### 5. Create a new pom file for your application.
Firstly, we will use module-04 source code for the created CI/CD.

	1. create a new POM file name as "pom-workshop.xml" 
	2. merge the pom file in module-04 into workshop-java project you fetched from CodeCommit
	3. delete all source codes in "workshop-java" project and copy all source codes from module-04 to "workshop-java" project
	4. run follwing commands again and check the output in target folder

```
mvn -f pom.xml compile
mvn -f
```
	5. if you get a compilation errors in your project, please check the Java compiler version and change compiler version in your applicaiton
	
	
<hr>


## Module-08 DevSecsOps
Secure pushing to Github to prevent from pushing codes with access/secret key
https://github.com/awslabs/git-secrets

<hr>

## Module-09 Custom Metrics and CloudWath Logs for data analytics



### Spring Cloud
https://cloud.spring.io/spring-cloud-aws/
Now Spring Cloud support S3, SNS, SQS, ElastiCache,CloudFormation and RDS

## git examples

git tag -l v1.1.*

git tag v1.0.2

git push origin v1.0.3

git clone

git checkout tags/<tag_name> 

git checkout tags/<tag_name> -b <branch_name>

delete tags

git push --delete origin <tag_name>

git tag --delete <tag_name>


## blog
1. Configuration - parameter store connection
2, Repository - Spring data