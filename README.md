# Swings
Practice

##Project is created using Spring boot
This code is written for my reference of configuring log4j2 but i want to make it a open source as this code could be used by others who are struggling to use log4j2 in there spring boot application. Here i am using log4j2.xml to configure the logging and in the pom.xml we have disabled the default application log provided by the spring boot framework.

##The application already has all the components you could clone it and use it on your local machine but the below steps are to understand how its built.

##To understand or to do it your self, here i am starting from basics if someone who has no idea on spring boot has to start then they could follow this or you could skip this part
* First to start with spring boot application you could visit [Spring Intializr](https://start.spring.io/) page.
![start.spring.io](start.spring.io.png)

* In project select maven project, and in language select java, then in spring boot version you could select any version of spring i choose 2.7.1 and in project meta data give the group name this would be the intial project pakage name and it should be in the format "com.somename" and packaging you could go with jar or war depeding on your project requirement and also select the right version of java.
* That's it click on generate it will create a project for you download it and import it in your IDE.
* After downloading open the application and open pom.xml it has some built in dependencies so under the <dependencies> just add
```
<!-- Exclude Spring Boot's Default Logging -->
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

<!-- Add Log4j2 Dependency -->
<dependency>
	<groupId>org.springframework.boot</groupId>
	<artifactId>spring-boot-starter-log4j2</artifactId>
</dependency>
```
* Then after adding that you need to add log4j2.xml file under the path "src/main/resources", this will not create a log file its used for printing console outputs and the code is provided below.

```
<?xml version="1.0" encoding="UTF-8"?>
<Configuration status="WARN" monitorInterval="30">
    <Properties>
        <Property name="LOG_PATTERN">
            %d{yyyy-MM-dd HH:mm:ss.SSS} %5p ${hostName} --- [%15.15t] %-40.40c{1.} : %m%n%ex
        </Property>
    </Properties>
    <Appenders>
        <Console name="ConsoleAppender" target="SYSTEM_OUT" follow="true">
            <PatternLayout pattern="${LOG_PATTERN}"/>
        </Console>
    </Appenders>
    <Loggers>
        <Logger name="com.example.log4j2demo" level="debug" additivity="false">
            <AppenderRef ref="ConsoleAppender" />
        </Logger>

        <Root level="info">
            <AppenderRef ref="ConsoleAppender" />
        </Root>
    </Loggers>
</Configuration>
```
* If you want the logs to be written on to a file then in <Appenders> add <RollingFile> replace the "path_to_file" with your system path and the SizeBasedTriggeringPolicy is for setting the log file size and DefaultRolloverStrategy is for number of files to be generated after it reaches the 20MB.

```
<RollingFile name="LogToFile" filename="path_to_file/application.log" filepattern="path_to_file/application-%d{yyyy-MM-dd}-%i.log">
<PatternLayout pattern="${LOG_PATTERN}" />
<Policy>
  <SizeBasedTriggeringPolicy max="20MB" />
 </Policy>
 <DefaultRolloverStrategy max="5" /.
 </RollingFile>
 
 * So this is the last part here we just need to use the configuration that we did go to src/main/java/packagename/starter_file.java which is the only class that you have in your project 
 
 ```
package package_name;

import org.springframework.boot.ApplicationArguments;
import org.springframework.boot.ApplicationRunner;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.apache.logging.log4j.LogManager;
import org.apache.logging.log4j.Logger;

@SpringBootApplication
public class LoggingApplication implements ApplicationRunner {

    private static final Logger logger = LogManager.getLogger(LoggingApplication.class);
    
	public static void main(String[] args) {
		SpringApplication.run(LoggingApplication.class, args);
	}

	@Override
	public void run(ApplicationArguments args) throws Exception {
		logger.debug("Debugging log");
        logger.info("Info log");
        logger.warn("Hey, This is a warning!");
        logger.error("Oops! We have an Error. OK");
        logger.fatal("Damn! Fatal error. Please fix me.");
	}

}

```
* In that application just implement the application runnable interface and that will ask for a run method to be overrided so just add that and then add then initialize the logger with the current class name.class when the java code is compiled it creates a class file so it works on that class file and you will start getting the logs as output except the debug, because it's set to false in the xml file.
* To run the application if your using eclipse then rightclick on this java file and select Run As -> Java Application.

**Note: The advantage of using spring boot is that there is no external Tomcat server is required as it has it built in**
