### [Index](https://github.com/PaaS-TA/Guide-eng/blob/master/README.md) > [AP User Guide](../README.md) > Java Development

### 1. Outline

#### 1.1. Document Outline

##### 1.1.1. Purpose
This Document (Java Application Development Guide) presents the service pack of Open PaaS projects (Mysql, Cubrid, MongoDB, RabbitMQ, Radis, and GlusterFS) to work with Java applications and deploy applications.

##### 1.1.2. Range
The range of this document is limited to the Java application development and service pack linkage of Open PaaS projects.

##### 1.1.3. References
-   [http://java.sun.com](http://java.sun.com)
-   [http://spring.io](http://spring.io)


### 2. JAVA Application Development Guide

#### 2.1. Outline
Various service packs registered in Open PaaS are bound to applications written in Java language, and access information for each service is acquired from environmental information (VCAP_SERVICES) bound to the application, and Java applications can be created in a Windows environment.
 The Java Sample Application provides a UI for REST-based services. Services used include Mysql, Cubrid, Mongodb, Redis, RabbitMq, and ClusterFS, which are available in local and OpenPaas environments. However, ClusterFs do not provide connection information in the Local environment.

#### 2.2. Development Environment Configuration
The development environment is constructed in the following environment for Java application development.
-   OS: Windows 7 64bit
-   Java: 1.8
-   Framework: Spring-Boot
-   IDE: Eclipse
-   Dependency: Maven

##### 2.2.1. JDK Installation
1.  Download JDK(1.8)

| <span id="__DdeLink__2953_294360055" class="anchor"></span>http://www.oracle.com/technetwork/java/javase/downloads/index-jsp-138363.html |
|-----------------------------------------------------------------------------------|

1.   Download JDK1.8

<img src="./images/java/image1.png" width="612" height="151" />

2. Install JDK

  -  jdk-8u65-windows-x64.exe Double-click to execute the installation.

<img src="./images/java/image2.png" width="612" height="300" />

  -  Proceed by clicking the next button.

<img src="./images/java/image3.png" width="400" height="250" />

  -  Installation is executed.

<img src="./images/java/image4.png" width="400" height="300" />
  -   Click the Close button to quit the installation.

3. JDK  Environment Setting

  -   Right-click on My Computer and click Properties.

<img src="./images/java/image5.png" width="400" height="150" />

  -   Click the Advanced System Settings > Advanced Tapes > Environment Variables button
  -   System Variable Section > Click path > Add the bin of installed jdk directory to path.
<img src="./images/java/image6.png" width="400" height="150" />

4.  Install eclipse

  - Download Eclipse IDE for Java EE Developers from the Eclipse official site.

| <span id="__DdeLink__2953_294360055" class="anchor"></span>http://www.eclipse.org/downloads/ |
|-----------------------------------------------------------------------------------|

  - Select eclipse-jee-mars-1-win32-x86_64.zip and unzip.

<img src="./images/java/image7.png" width="400" height="150" />

  - Click eclipse.exe.  
<img src="./images/java/image9.png" width="400" height="150" />

  - The eclipse initial screen appears.  
<img src="./images/java/image10.png" width="400" height="150" />

  - Click Project Explore. at the left side and click import.  
<img src="./images/java/image11.png" width="400" height="150" />

  - Click Existing Maven Project.  
<img src="./images/java/image12.png" width="400" height="150" />

  - Click the Browse button and select the file with java-sample project. Click the Finish button.
<img src="./images/java/image13.png" width="400" height="150" />

  - Verify the added java sample Application.  
<img src="./images/java/image14.png" width="400" height="150" />


#### 2.3. Development
  - OpenPaas Java Sample Application has the following structure. Using the Spring-Boot Library
It can be operated without a separate Tomcat setting. Data management for sample applications uses either MySQL, CubridDB, or MongoDB, so when making an API request, it is determined with the DBType value in the request body.

<img src="./images/java/image15.png" width="500" height="500" />



  - **OpenPaas Java Sample Application Package Structure**

|**Package/File**   |**Description**                 |
|----------|-------------------------|
|Java/sample/biz                  |It is a service domain which is a business domain.  |
|Java/sample/Config/data          |This is a package that establishes persistence area connection settings such as Mysql, mongodb, redis, clusterFs, cubid, and RabbitMq. Recognize and access the Local/Cloud environment. ClusterFs settings are not supported in the Local environment (Cloud Only)|
|Java/sample /Config/web          |Set the WEB settings information related to MVC, Interceptor, and MultiPart in Sample Application.              |
|Java/sample /controller          |The controller domain that receives the requested information from the UI.            |
|Java/sample/dao                  |The domain where data in the Persistence section is entered/modified/deleted/checked.               |
|Java/sample /model               |VO domain.     |
|Java/sample /Application.java    |Starts the Sample Application using the Spring Boot.   |
|Resource/tables                  |Contains DB Table DDL and basic data DDL for use by Sample Application.       |
|Resource/ application-cloud.properties  |The property that the Sample Application will use in the Cloud environment. |
|Resource/ application.properties        |The property that the Sample Application will use in the Local environment.  |
|Resource/logback.xml                    |logging setting that uses Logback.   |
|Manifest.yml                            |Files used when an application is first configured in a cloud environment, including environmental information, service information (such as mysql, rabbitmq), application name, number of instances, and instance memory settings.     |
|pom.xml                                 |Defines the Maven dependency of Sample Application.     |
|webapp                                 |UI related resources are located     |


##### 2.3.1. Application Environment Setting
  - This document shows how Java Applications can run in a Cloud environment. Does not provide installation information such as Mysql, Cubrid, Redis, etc.

1)   manifest.yml
  - Cloud  
<img src="./images/java/image1-1.png" width="350" height="300" />

2) resource/application-cloud.properties (application.properties is for Local environment)
  - The service name that the application will use is set as the properties that will be applied in the Cloud environment.
Because cubrid and GlusterFs do not provide information on Spring-cloud-connector, use  VCAP SERVICE environment information of the Application to do service connection.

```
db.mysql.servicename: mysql-service-instance
mongodb.service.name: mongo-service-instance
redis.service.name:   redis-service-instance
rabbitmq.service.name: rabbitmq-service-instance
```

3) sample/config/data/CloudConfigData.java
  - Environment Setting of the Application  

  ```
  @Profile("cloud") ==> Annotation that recognizes the Cloud environment (Environment information and mapping of manifest.yml)
@Configuration    ==> Spring Config
@ServiceScan      ==> Cloud Config Service Scan Annotation
public class CloudDataConfig extends AbstractCloudConfig {

	@Value("${db.mysql.servicename}")
	private String mysqlServiceName; ==> application-cloud.properties of mysql
                                                Read service instance name.
	private String cubridJdbcUrl;

	@Value("${mongodb.service.name}")  
	private String mongoServiceName; ==> application-cloud.properties of mongodb
                                                Read service instance name.
	@Value("${redis.service.name}")
	private String redisServiceName; ==> application-cloud.properties of redis
                                                 Read service instance name.
	@Value("${rabbitmq.service.name}")
	private String rabbitServiceName; ==> application-cloud.properties of redis
                                                 Read service instance name.


	@Bean(name = "dsMysql")
	@Primary
	DataSource mysqlDataSource() {

		CloudFactory cloudFactory = new CloudFactory();
		Cloud cloud = cloudFactory.getCloud();
		ServiceInfo serviceInfo = cloud.getServiceInfo(mysqlServiceName);
		String serviceId = serviceInfo.getId();
		return cloud.getServiceConnector(serviceId, DataSource.class, null);

	}

	@Bean(name = "jdbcMysql")  ==> MySql service connection setting
	@Autowired
	public JdbcTemplate mysqlJdbcTemplate(@Qualifier("dsMysql") DataSource dsSlave) {
		return new JdbcTemplate(dsSlave);
	}

	@Bean(name = "dsCubrid")
	public DataSource cubridDataSource() {
		try {

			String vcap_services = System.getenv("VCAP_SERVICES");
			JSONObject jsonObj = JSONObject.fromObject(vcap_services);
			JSONArray userPro = jsonObj.getJSONArray("CubridDB");
			jsonObj = JSONObject.fromObject(userPro.get(0));
			jsonObj = jsonObj.getJSONObject("credentials");
			cubridJdbcUrl = jsonObj.getString("jdbcurl");

			return new SimpleDriverDataSource(cubrid.jdbc.driver.CUBRIDDriver.class.newInstance(), cubridJdbcUrl);
		} catch (Exception e) {
			throw new RuntimeException(e);
		}
	}

	@Bean(name = "jdbcCubrid")  ==> Cubrid service connection setting
	@Autowired
	public JdbcTemplate cubridJdbcTemplate(@Qualifier("dsCubrid") DataSource dsSlave) {
		return new JdbcTemplate(dsSlave);
	}

	/**
	 * MongoDBFactory
	 */
	@Autowired
	MongoDbFactory mongoDbFactory;


	@Bean(name = "mongoTemplate")   ==> MongoDB service connection setting
	public MongoTemplate mongoTemplate() throws UnknownHostException {

		CloudFactory cloudFactory = new CloudFactory();
		Cloud cloud = cloudFactory.getCloud();
		MongoServiceInfo serviceInfo = (MongoServiceInfo) cloud.getServiceInfo(mongoServiceName);

		// MongoDB authentication process
		mongoDbFactory.getDb().authenticate(serviceInfo.getUserName(), serviceInfo.getPassword().toCharArray());

		MongoTemplate mongoTemplate = new MongoTemplate(mongoDbFactory);

		return mongoTemplate;
	}

	@Bean  ==> Redis service connection setting
	public JedisPool redisTemplate() {

		CloudFactory cloudFactory = new CloudFactory();
		Cloud cloud = cloudFactory.getCloud();
		RedisServiceInfo serviceInfo = (RedisServiceInfo) cloud.getServiceInfo(redisServiceName);

		JedisPoolConfig poolConfig = new JedisPoolConfig();
		poolConfig.setMaxTotal(128);
		return new JedisPool(poolConfig, serviceInfo.getHost(), serviceInfo.getPort(), 5, serviceInfo.getPassword());
	}

	@Bean
	@Primary
	public CachingConnectionFactory rabbitmqConnectionFactory() throws IOException {

		CloudFactory cloudFactory = new CloudFactory();
		Cloud cloud = cloudFactory.getCloud();

		AmqpServiceInfo serviceInfo = (AmqpServiceInfo) cloud.getServiceInfo(rabbitServiceName);

		ConnectionFactory connectionFactory = new ConnectionFactory();
		connectionFactory.setHost(serviceInfo.getHost());
		connectionFactory.setPort(serviceInfo.getPort());
		connectionFactory.setUsername(serviceInfo.getUserName());
		connectionFactory.setPassword(serviceInfo.getPassword());
		connectionFactory.setVirtualHost(serviceInfo.getVirtualHost());

		try {
			// SslProtocol use setting
			connectionFactory.useSslProtocol("TLS");

		} catch (KeyManagementException e) {
			e.printStackTrace();
		} catch (NoSuchAlgorithmException e) {
			e.printStackTrace();
		}

		CachingConnectionFactory cachingConnectionFactory = new CachingConnectionFactory(connectionFactory);

		return cachingConnectionFactory;
	}

	@Bean  ==> RabbitMQ service connection setting
	public RabbitTemplate amqpTemplate(
			@Qualifier("rabbitmqConnectionFactory") CachingConnectionFactory connectionFactory) {

		RabbitTemplate rabbitTemplate = new RabbitTemplate(connectionFactory);

		rabbitTemplate.setQueue("sampleQueue");
		rabbitTemplate.setRoutingKey("sampleQueue");
		rabbitTemplate.setConnectionFactory(connectionFactory);
		return rabbitTemplate;
	}

	@Bean
	public RabbitAdmin rabbitAdmin(@Qualifier("rabbitmqConnectionFactory") CachingConnectionFactory connectionFactory) {

		RabbitAdmin admin = new RabbitAdmin(connectionFactory);
		return admin;
	}

	@Autowired
	Gson gson;

	@Bean  ==> GlusterFs service connection setting
	public AccountConfig accountConfig(){

		String vcap_services = System.getenv("VCAP_SERVICES");
        JsonObject jsonObj = gson.fromJson(vcap_services, JsonElement.class).getAsJsonObject();

        JsonArray userPro = jsonObj.getAsJsonArray("glusterfs");
        jsonObj = userPro.get(0).getAsJsonObject().getAsJsonObject("credentials");
//        String provider = jsonObj.get("provider").getAsString();
        String tenantName = jsonObj.get("tenantname").getAsString();
        String username = jsonObj.get("username").getAsString();
        String password = jsonObj.get("password").getAsString();
        String authUrl = jsonObj.get("auth_url").getAsString();

		AccountConfig config = new AccountConfig();
		config.setUsername(username);
		config.setTenantName(tenantName);
		config.setPassword(password);
		config.setAuthUrl(authUrl + "/tokens");
		config.setAuthenticationMethod(AuthenticationMethod.KEYSTONE);
		return config;
	}

}
  ```

4) pom.xml
   - Maven Dependency being used in the Sample Application 


   ```
      <parent>
     		<groupId>org.springframework.boot</groupId>
     		<artifactId>spring-boot-starter-parent</artifactId>
     		<version>1.2.3.RELEASE</version>
     		<relativePath />
     	</parent>

     <dependency>
     			<groupId>org.springframework.cloud</groupId>
     			<artifactId>spring-cloud-starter-config</artifactId>
     		</dependency>

     		<dependency>
     			<groupId>org.springframework.boot</groupId>
     			<artifactId>spring-boot-starter-web</artifactId>
     		</dependency>
     		<dependency>
     			<groupId>org.springframework.boot</groupId>
     			<artifactId>spring-boot-starter-data-jpa</artifactId>
     		</dependency>

     		<dependency>
     			<groupId>org.springframework.boot</groupId>
     			<artifactId>spring-boot-configuration-processor</artifactId>
     			<optional>true</optional>
     		</dependency>
     ==>Spring Boot dependency


    <!-- Spring Cloud Connector Start -->
 		<dependency>
 			<groupId>org.springframework.cloud</groupId>
 			<artifactId>spring-cloud-spring-service-connector</artifactId>
 		</dependency>

 		<dependency>
 			<groupId>org.springframework.cloud</groupId>
 			<artifactId>spring-cloud-core</artifactId>
 		</dependency>

 		<dependency>
 			<groupId>org.springframework.cloud</groupId>
 			<artifactId>spring-cloud-cloudfoundry-connector</artifactId>
 		</dependency>
 		<!-- Spring Cloud Connector End -->

         ==> Spring Cloud Connector dependency


 		<!-- MongoDB Dependency Start -->
 		<dependency>
 			<groupId>org.mongodb</groupId>
 			<artifactId>mongo-java-driver</artifactId>
 			<version>2.13.3</version>
 		</dependency>

 		<dependency>
 			<groupId>org.springframework.boot</groupId>
 			<artifactId>spring-boot-starter-data-mongodb</artifactId>
 			<exclusions>
 				<exclusion>
 					<groupId>org.mongodb</groupId>
 					<artifactId>mongo-java-driver</artifactId>
 				</exclusion>
 			</exclusions>
 		</dependency>
 		<!-- MongoDB Dependency End -->

 	<!-- MongoDB Dependency Start -->
 		<dependency>
 			<groupId>org.mongodb</groupId>
 			<artifactId>mongo-java-driver</artifactId>
 			<version>2.13.3</version>
 		</dependency>

 		<dependency>
 			<groupId>org.springframework.boot</groupId>
 			<artifactId>spring-boot-starter-data-mongodb</artifactId>
 			<exclusions>
 				<exclusion>
 					<groupId>org.mongodb</groupId>
 					<artifactId>mongo-java-driver</artifactId>
 				</exclusion>
 			</exclusions>
 		</dependency>
 		<!-- MongoDB Dependency End -->

 		<!-- MySql Connector Start -->
 		<dependency>
 			<groupId>mysql</groupId>
 			<artifactId>mysql-connector-java</artifactId>
 		</dependency>
 		<!-- MySql Connector End -->

 		<!-- Redis Start -->
 		<dependency>
 			<groupId>org.springframework.boot</groupId>
 			<artifactId>spring-boot-starter-redis</artifactId>
 		</dependency>

 		<dependency>
 			<groupId>org.apache.commons</groupId>
 			<artifactId>commons-pool2</artifactId>
 		</dependency>
 		<!-- Redis End -->

 		<!-- RabbitMQ Start -->
 		<dependency>
 			<groupId>org.springframework.boot</groupId>
 			<artifactId>spring-boot-starter-amqp</artifactId>
 		</dependency>

 		<!-- RabbitMQ End -->

 		<!-- Java client for OpenStack Storage Swift(GlusterFS) setting -->
 		<!-- GlusterFS Start -->
 		<dependency>
 			<groupId>org.javaswift</groupId>
 			<artifactId>joss</artifactId>
 			<version>0.9.8</version>
 		</dependency>
 		<!-- GlusterFS End -->
   ```
#### 2.3. Development

  - The Java Sample Application is configured with Spring Boot and does not require a separate Tomcat setting.
Right-click the Application.java button in the java package Root --> Run As --> Run Java Application to run the Sample Application.

<img src="./images/java/image17.png" width="350" height="400" />


#### 2.4. Deployment
  - Deploy Java sample applications using the cf cli command
  - The java-sample application runs maven installation and war must exist.
  - Proceed under the assumption that cf cli is installed and cf login is already done.
 ```
 ### Java Sample Application Deployment
 $ cd “Java Sample Application” Directory

 $cd  /home/csupshin/git/OpenPaaSSample/java-sample-app
 $ cf push
 Using manifest file /home/csupshin/git/OpenPaaSSample/java-sample-app/manifest.yml

Updating app java-sample in org OCP / space dev as admin...
OK

Using route java-sample.52.71.31.153.xip.io
Uploading java-sample...
Uploading app files from: /home/csupshin/git/OpenPaaSSample/java-sample-app/lib/java-sample.war
Uploading 1.6M, 227 files
Done uploading               
OK
Binding service mysql-service-instance to app java-sample in org OCP / space dev as admin...
OK
Binding service cubrid-service-instance to app java-sample in org OCP / space dev as admin...
OK
Binding service redis-service-instance to app java-sample in org OCP / space dev as admin...
OK
Binding service rabbitmq-service-instance to app java-sample in org OCP / space dev as admin...
OK
Binding service glusterfs-service-instance to app java-sample in org OCP / space dev as admin...
OK

Stopping app java-sample in org OCP / space dev as admin...
OK

Starting app java-sample in org OCP / space dev as admin...
Creating container
Successfully created container
Downloading app package...
Downloaded app package (30.5M)
Downloading buildpacks (java_buildpack), build artifacts cache...
Downloading java_buildpack...
Downloading build artifacts cache...
Downloaded java_buildpack
Downloaded build artifacts cache (44.7M)
Downloaded buildpacks
Staging...
-----> Java Buildpack Version: v3.3.1 | https://github.com/cloudfoundry/java-buildpack.git#063836b
-----> Downloading Open Jdk JRE 1.8.0_73 from https://download.run.pivotal.io/openjdk/trusty/x86_64/openjdk-1.8.0_73.tar.gz (found in cache)
       Expanding Open Jdk JRE to .java-buildpack/open_jdk_jre (2.1s)
-----> Downloading Open JDK Like Memory Calculator 2.0.1_RELEASE from https://download.run.pivotal.io/memory-calculator/trusty/x86_64/memory-calculator-2.0.1_RELEASE.tar.gz (found in cache)
       Memory Settings: -Xss995K -Xmx382293K -XX:MaxMetaspaceSize=64M -Xms382293K -XX:MetaspaceSize=64M
-----> Downloading Spring Auto Reconfiguration 1.10.0_RELEASE from https://download.run.pivotal.io/auto-reconfiguration/auto-reconfiguration-1.10.0_RELEASE.jar (found in cache)
       Modifying /WEB-INF/web.xml for Auto Reconfiguration
Exit status 0
Staging complete
Uploading droplet, build artifacts cache...
Uploading droplet...
Uploading build artifacts cache...
Uploaded build artifacts cache (44.7M)
Uploaded droplet (75.4M)
Uploading complete

0 of 1 instances running, 1 starting
0 of 1 instances running, 1 starting
0 of 1 instances running, 1 starting
0 of 1 instances running, 1 starting
0 of 1 instances running, 1 starting
0 of 1 instances running, 1 starting
0 of 1 instances running, 1 starting
1 of 1 instance running

App started


OK

App java-sample was started using this command `CALCULATED_MEMORY=$($PWD/.java-buildpack/open_jdk_jre/bin/java-buildpack-memory-calculator-2.0.1_RELEASE -memorySizes=metaspace:64m.. -memoryWeights=heap:75,metaspace:10,native:10,stack:5 -memoryInitials=heap:100%,metaspace:100% -totMemory=$MEMORY_LIMIT) && SERVER_PORT=$PORT $PWD/.java-buildpack/open_jdk_jre/bin/java -cp $PWD/.:$PWD/.java-buildpack/spring_auto_reconfiguration/spring_auto_reconfiguration-1.10.0_RELEASE.jar -Djava.io.tmpdir=$TMPDIR -XX:OnOutOfMemoryError=$PWD/.java-buildpack/open_jdk_jre/bin/killjava.sh $CALCULATED_MEMORY org.springframework.boot.loader.WarLauncher`

Showing health and status for app java-sample in org OCP / space dev as admin...
OK

requested state: started
instances: 1/1
usage: 512M x 1 instances
urls: java-sample.52.71.31.153.xip.io
last uploaded: Mon Feb 15 06:47:21 UTC 2016
stack: cflinuxfs2
buildpack: java_buildpack

     state     since                    cpu     memory         disk           details   
#0   running   2016-02-15 03:49:33 PM   88.2%   341M of 512M   185.2M of 1G
 ```

#### 2.5. Sample App Deployment Check

1) Use the cf cli command to check the deployment status and URL of the Java sample application.

```
$ cf apps
Getting apps in org OCP / space dev as admin...
OK

name                     requested state   instances   memory   disk   urls   
java-sample              started           1/1         512M     1G     java-sample.52.71.31.153.xip.io   
```

#### 2.6. Sample App Login
1) The login screen appears when java-sample.52.71.31.153.xip.io is accessed in the browser.
Enter admin/admin to log in.

<img src="./images/java/image18.png" width="350" height="300" />

2) When the login is complete, the following organizational screen appears. Click the Home icon at the bottom of the screen to display the detailed organization screen of the organization. Click the organization name to view the list of the organization's under the organization.

<img src="./images/java/image19.png" width="350" height="150" />


### [Index](https://github.com/PaaS-TA/Guide-eng/blob/master/README.md) > [AP User Guide](../README.md) > Java Development
