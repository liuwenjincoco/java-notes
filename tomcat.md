调试tomcat 

------

```xml
<dependency>
	<groupId>org.apache.tomcat</groupId>
	<artifactId>tomcat7</artifactId>
	<version>7.0.81-local</version>
</dependency>
<!-- 内嵌tomcat -->
<dependency>
	<groupId>org.apache.tomcat.embed</groupId>
	<artifactId>tomcat-embed-core</artifactId>
	<version>7.0.81</version>
</dependency>
<dependency>
	<groupId>org.apache.tomcat.embed</groupId>
	<artifactId>tomcat-embed-logging-juli</artifactId>
	<version>7.0.81</version>
</dependency>
<dependency>
	<groupId>org.apache.tomcat.embed</groupId>
	<artifactId>tomcat-embed-jasper</artifactId>
	<version>7.0.81</version>
</dependency> 

1. 下载对应tomcat版本的源码，tomcat官网有下载的，由于tomcat是ant编译的，将其转成maven编译的结构编译jar包，source包 

2.在web工程下面加入tomcat jar包依赖，此处也可以使用上面注释的三个包。 

```



```xml 
<!-- 以下是tomcat ant编译转maven的配置 --> 

<?xml version="1.0" encoding="UTF-8"?>
<project
	xmlns="http://maven.apache.org/POM/4.0.0"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" 

	xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
	<modelVersion>4.0.0</modelVersion>
	<groupId>org.apache.tomcat</groupId>
	<artifactId>tomcat7</artifactId>
	<name>tomcat7</name>
	<version>7.0.81-local</version>
	<build>
		<plugins>
			<plugin>
				<groupId>org.apache.maven.plugins</groupId>
				<artifactId>maven-compiler-plugin</artifactId>
				<version>3.5.1</version>
				<configuration>
					<encoding>UTF-8</encoding>
					<source>1.7</source>
					<target>1.7</target>
				</configuration>
			</plugin>
			<plugin>
				<groupId>org.apache.maven.plugins</groupId>
				<artifactId>maven-jar-plugin</artifactId>
				<version>2.6</version>
			</plugin>
			<plugin>
				<inherited>true</inherited>
				<groupId>org.apache.maven.plugins</groupId>
				<artifactId>maven-source-plugin</artifactId>
				<executions>
					<execution>
						<id>attach-sources</id>
						<goals>
							<goal>jar</goal>
						</goals>
					</execution>
				</executions>
			</plugin>
		</plugins>
		<finalName>tomcat7</finalName>
		<sourceDirectory>java</sourceDirectory>
		<!--<testSourceDirectory>test</testSourceDirectory> test 下的有些文件报错，因此将test文件夹去掉了-->
		<resources>
			<resource>
				<directory>java</directory>
			</resource>
		</resources>
		<testResources>
			<testResource>
				<directory>test</directory>
			</testResource>
		</testResources>
	</build>
	<dependencies>
		<dependency>
			<groupId>junit</groupId>
			<artifactId>junit</artifactId>
			<version>4.11</version>
			<scope>test</scope>
		</dependency>
		<dependency>
			<groupId>ant</groupId>
			<artifactId>ant</artifactId>
			<version>1.6.5</version>
		</dependency>
		<dependency>
			<groupId>org.easymock</groupId>
			<artifactId>easymock</artifactId>
			<version>3.4</version>
		</dependency>
		<dependency>
			<groupId>wsdl4j</groupId>
			<artifactId>wsdl4j</artifactId>
			<version>1.6.2</version>
		</dependency>
		<dependency>
			<groupId>javax.xml</groupId>
			<artifactId>jaxrpc</artifactId>
			<version>1.1</version>
		</dependency>
		<dependency>
			<groupId>org.eclipse.jdt.core.compiler</groupId>
			<artifactId>ecj</artifactId>
			<version>4.5</version>
		</dependency>
	</dependencies>
</project> 

```



###远程debug端口开启（2选1即可） 

* 修改startup.sh脚本 

```bash
declare -x CATALINA_OPTS="-Xrunjdwp:transport=dt_socket,server=y,suspend=n,address=8788" 

脚本倒数第二行添加此行即可 
```

* 修改catlina.sh脚本 

``` bash
在JAVA_OPTS参数中添加 

-Xrunjdwp:transport=dt_socket,server=y,suspend=n,address=8788 

-agentlib:jdwp=transport=dt_socket,address=50618,suspend=n,server=y 

```