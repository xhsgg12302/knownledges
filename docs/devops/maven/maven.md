## MAVEN分享
> 今天这次分享的主要就是maven构建工具使用的一些经验， 和大家起分享，交流一下。也是为了日后能够更加灵活的使用。内容中也包括我原来对maven存在的误解和疑问的一些答疑。以交流学习，整理归纳为主。

+ ### Tips

	1. 运行mvn compile 会执行 clean 吗? 运行 package 会执行 compile吗?
	2. optional,provided 区别?
	3. 既然maven有自带的依赖仲裁机制，为什么还会出现依赖冲突?
	4. mvn 依赖可以定义的位置及加载顺序，远程仓库定义的位置及加载顺序。mirrorOf实际意义? 

+ ### 介绍（一笔带过）

    ```
    maven 是继 ant 之后apache开源的软件构建工具，由绑定在生命周期上的插件辅助完成构建任务，主要的
    功能包括解决依赖，集成测试，编译项目，定制化资源打包发布等。
    介绍，下载地址，使用方式。
    ```

+ ### settings.xml 介绍

    ```
    <localRepository>
    <pluginGroups>
        配置插件的groupId。为的是单独执行插件的时候，可以简单调用。
        1，全路径执行。2，如何配置省略执行，3，自带的没有配置，为什么可以省略执行 4,prefix
    <proxies>
    <servers> 将pluginGroups中默认的标签解释一下
    <mirrors>
        <mirror>
            <mirrorOf>
    <profile>  -- 属性压制
    ```
+ ### POM.xml介绍

    ```
    <relativePath/>
        查找parent路径优先级； 指定的位置 > 本地仓库 > 远程仓库 
        1,当为空标签得时候，默认从本地仓库查找没有的话再从远程，
        2,不写得时候这个值默认为 ../pom.xml，也就是他上级路径得pom.xml.
    
    <properties>
    <dependencyManagement>
    <dependencies>
        <dependency>
            <scope>
            <type>
            <classifier>
    <build>
        <filters>
            <filter>
        <resource>
            <filtering>true|false
        <plugins>
    ```

+ ### properties 引用的各种形式，${},@@
    
    ```
    https://maven.apache.org/pom.html#properties
    
    resources 过滤或加载顺序
    1,基本使用。
    2，属性优先级
    ```
+ ### maven特性

    ```
    生命周期，
    继承与聚合，，
    https://maven.apache.org/pom.html#a-final-note-on-inheritance-v-aggregation
        继承通过申明<parent>标签来定义。
    依赖机制
    
    ```
+ ### 问题答疑
    
    ```
    1,  运行mvn compile 会执行 clean吗？运行package 会执行 compile吗？
        不会，因为查看文档发现 compile 阶段 跟 clean 阶段不属于同一个声明周期【分组】。
        或者执行命令也可发现在compile命令下，不会调用clean相关插件。
    
    2,  optional ,provided 区别，
        optional 表示可选依赖传递，可以理解为默认排除
        provided 表示依赖必选，但是使用方提供，
        
    3，既然maven有自带的依赖仲裁机制，为什么还会出现依赖冲突？
        
        
    4，mvn 依赖可以在哪些地方定义？最终生效的是那部分？
        1, <dependency> , <repository> 的定义是有顺序的。
        <dependency source> like repository,mirror,<profile>
        pom.dependency  > profile.dependency > parent
        
        2,远程仓库定义
        settings > pom.profile > pom.repository
    ```
    
    ![](../../../.images/maven/project-dependency.png '项目结构')
    
    ![nihao](../../../.images/maven/pull-process.png 'repo')

+ ### 常用命令

    ```
    构建命令[phase]： mvn [ clean, compile, package, install, deploy, site ] 
    查看插件帮助：mvn [plugin:help]
        mvn dependency:tree -Dverbose=true -Dincludes=commons-lang:commons-lang
    构建命令[插件：goals]：mvn [clean:clean, spring-boot:repackage ] 
    直接命令[options]: mvn [-Da=b, -emp, -ep, -X]
    ```

+ ### 插件介绍(插件，resources)
    
    ```
    1, 打jar包-> exe文件，
        -- maven-shade-plugin 配合 launch4j-maven-plugin
        -- spring-boot-maven-plugin 配合 launch4j-maven-plugin
        
    2，远程部署tomcat插件
        <plugins>
            <plugin>
                <groupId>org.apache.tomcat.maven</groupId>
                <artifactId>tomcat7-maven-plugin</artifactId>
                <version>2.2</version>
                <configuration>
                    <url>http://test.wtfu.site/manager/text</url>
                    <server>tomcat7</server>
                    <path>/test##2.1</path>
                    <charset>utf8</charset>
                    <update>true</update>
                </configuration>
            </plugin>
        </plugins>
        
    3，代码生成插件
        <plugin>
				<!-- _12302_2021/9/7_< https://gitee.com/xhsgg12302/haohuo-component.git > -->
				<groupId>com.haohuo.framework</groupId>
				<artifactId>gencode-maven-plugin</artifactId>
				<version>1.0.3-SNAPSHOT</version>
				<executions>
					<!-- _12302_2021/9/7_< 不跟任何生命周期绑定，
					命令行 mvn com.haohuo.framework:gencode-maven-plugin:product
					或者maven任务栏插件，goal,执行 > -->
				</executions>
				<configuration>
				.....
		        </configuration>
		</plugin>
    ```

+ ### maven私服及免费的maven仓库

    ```
    自己搭建
    开源镜像（163, huawei, alibaba, tencent)
        163: http://mirrors.163.com/.help/maven.html
        huawei: https://mirrors.huaweicloud.com/home
        alibaba: https://developer.aliyun.com/mvn/guide
        tencent: https://mirrors.cloud.tencent.com/help/maven.html
    github提供的package
    云效maven 私服 
    旧版：https://repomanage.rdc.aliyun.com/
    新版：https://packages.aliyun.com/maven （有覆盖 release版本的设置）
    ```
+ ### 项目中的log4j[CVE-2021-44228] 漏洞修复思路及复现

    ```
    解决思路：
        一，访问log4j官网，查看最新响应漏洞及解决版本。替换相应的版本即可。（可能需要即时跟踪log4j动    态，最近漏洞更新挺快）
        二，以假乱真，移花接木
            1，根据全局需要的排除得jar定制高版本fake包，上传到私服，
            2，由于maven得仲裁机制压制真正得log4j包。
            3，引入三方开源得桥接包，将输出流重新路由到slf4j->logback.
    
    注意点：log4j分为log4j 1和2版本，而且它们的groupId 也不一样。 
        引入 log4j1 只需要一个 log4j:log4j:1.2.17, 爆出漏洞得不是log4j1
        而log4j2 需要引入连个：org.apache.logging.log4j:log4j-api:2.14.1 【门面接口包】
                               org.apache.logging.log4j:log4j-core:2.14.1 【实现包(出问题的包)】
    
    
        
    扩展：    
        1, logj漏洞原理的简单介绍，及两种方式复现
        2, 实际中的log4j攻击现象（nginx日志）。
    ```
    
+ ### deploy

    ```
    mvn clean deploy -Dmaven.test.skip=true -DaltDeploymentRepository=rdc-snapshots::default::https://packages.aliyun.com/maven/repository/2066950-snapshot-w6DSio/
    ```

## settings.xml
<details><summary>示例</summary>

```xml
<?xml version="1.0" encoding="UTF-8"?>

<!--
Licensed to the Apache Software Foundation (ASF) under one
or more contributor license agreements.  See the NOTICE file
distributed with this work for additional information
regarding copyright ownership.  The ASF licenses this file
to you under the Apache License, Version 2.0 (the
"License"); you may not use this file except in compliance
with the License.  You may obtain a copy of the License at

    http://www.apache.org/licenses/LICENSE-2.0

Unless required by applicable law or agreed to in writing,
software distributed under the License is distributed on an
"AS IS" BASIS, WITHOUT WARRANTIES OR CONDITIONS OF ANY
KIND, either express or implied.  See the License for the
specific language governing permissions and limitations
under the License.
-->

<!--
 | This is the configuration file for Maven. It can be specified at two levels:
 |
 |  1. User Level. This settings.xml file provides configuration for a single user,
 |                 and is normally provided in ${user.home}/.m2/settings.xml.
 |
 |                 NOTE: This location can be overridden with the CLI option:
 |
 |                 -s /path/to/user/settings.xml
 |
 |  2. Global Level. This settings.xml file provides configuration for all Maven
 |                 users on a machine (assuming they're all using the same Maven
 |                 installation). It's normally provided in
 |                 ${maven.conf}/settings.xml.
 |
 |                 NOTE: This location can be overridden with the CLI option:
 |
 |                 -gs /path/to/global/settings.xml
 |
 | The sections in this sample file are intended to give you a running start at
 | getting the most out of your Maven installation. Where appropriate, the default
 | values (values used when the setting is not specified) are provided.
 |
 |-->
<settings xmlns="http://maven.apache.org/SETTINGS/1.0.0"
          xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
          xsi:schemaLocation="http://maven.apache.org/SETTINGS/1.0.0 http://maven.apache.org/xsd/settings-1.0.0.xsd">
  <!-- localRepository
   | The path to the local repository maven will use to store artifacts.
   |
   | Default: ${user.home}/.m2/repository
  <localRepository>/path/to/local/repo</localRepository>
  -->
  <localRepository>/Users/stevenobelia/Documents/maven_repository</localRepository> 
  <!-- interactiveMode
   | This will determine whether maven prompts you when it needs input. If set to false,
   | maven will use a sensible default value, perhaps based on some other setting, for
   | the parameter in question.
   |
   | Default: true
  <interactiveMode>true</interactiveMode>
  -->

  <!-- offline
   | Determines whether maven should attempt to connect to the network when executing a build.
   | This will have an effect on artifact downloads, artifact deployment, and others.
   |
   | Default: false
  <offline>false</offline>
  -->

  <!-- pluginGroups
   | This is a list of additional group identifiers that will be searched when resolving plugins by their prefix, i.e.
   | when invoking a command line like "mvn prefix:goal". Maven will automatically add the group identifiers
   | "org.apache.maven.plugins" and "org.codehaus.mojo" if these are not already contained in the list.
   |-->
  <pluginGroups>
    <!-- pluginGroup
     | Specifies a further group identifier to use for plugin lookup.
    <pluginGroup>com.your.plugins</pluginGroup>
    -->
  </pluginGroups>

  <!-- proxies
   | This is a list of proxies which can be used on this machine to connect to the network.
   | Unless otherwise specified (by system property or command-line switch), the first proxy
   | specification in this list marked as active will be used.
   |-->
  <proxies>
    <!-- proxy
     | Specification for one proxy, to be used in connecting to the network.
     |
    <proxy>
      <id>optional</id>
      <active>true</active>
      <protocol>http</protocol>
      <username>proxyuser</username>
      <password>proxypass</password>
      <host>proxy.host.net</host>
      <port>80</port>
      <nonProxyHosts>local.net|some.host.com</nonProxyHosts>
    </proxy>
    -->
  </proxies>

  <!-- servers
   | This is a list of authentication profiles, keyed by the server-id used within the system.
   | Authentication profiles can be used whenever maven must make a connection to a remote server.
   |-->
  <servers>
    <!-- server
     | Specifies the authentication information to use when connecting to a particular server, identified by
     | a unique name within the system (referred to by the 'id' attribute below).
     |
     | NOTE: You should either specify username/password OR privateKey/passphrase, since these pairings are
     |       used together.
     |
    <server>
      <id>deploymentRepo</id>
      <username>repouser</username>
      <password>repopwd</password>
    </server>
    -->
    <server>
		<id>releases</id>
		<username>admin</username>
		<password>{0L7qrv2rYMEHTV896SqpI58ztyMinhfN7expcu6wFes=}</password>
    </server>

    <server>
		<id>snapshots</id>
		<username>admin</username>
		<password>{0L7qrv2rYMEHTV896SqpI58ztyMinhfN7expcu6wFes=}</password>
    </server>

    <server>
		<id>tomcat7</id>
		<username>xhs</username>
		<password>{ybhHCUAegqsH4EQp2GVT6Zh4w1O3s4ZLZphTwbSe7Wo=}</password>
    </server>

    <server>
      <id>github</id>
      <username>xhs-12302</username>
      <password>{6JnHDzZJqBAHiSA42DPOtf5ZwpOCeM7vUUS+1dKKwyBvkX0Cx2m3qAuNxP05RHhYrA61UKIWvRz+BtdAQZPdig==}</password>
    </server>
    <!-- Another sample, using keys to authenticate.
    <server>
      <id>siteServer</id>
      <privateKey>/path/to/private/key</privateKey>
      <passphrase>optional; leave empty if not used.</passphrase>
    </server>
    -->
  </servers>

  <!-- mirrors
   | This is a list of mirrors to be used in downloading artifacts from remote repositories.
   |
   | It works like this: a POM may declare a repository to use in resolving certain artifacts.
   | However, this repository may have problems with heavy traffic at times, so people have mirrored
   | it to several places.
   |
   | That repository definition will have a unique id, so we can create a mirror reference for that
   | repository, to be used as an alternate download site. The mirror site will be the preferred
   | server for that repository.
   |-->
  <mirrors>
    <!-- mirror
     | Specifies a repository mirror site to use instead of a given repository. The repository that
     | this mirror serves has an ID that matches the mirrorOf element of this mirror. IDs are used
     | for inheritance and direct lookup purposes, and must be unique across the set of mirrors.
     |
    <mirror>
      <id>mirrorId</id>
      <mirrorOf>repositoryId</mirrorOf>
      <name>Human Readable Name for this Mirror.</name>
      <url>http://my.repository.com/repo/path</url>
    </mirror>
     -->
	  <!--<mirror>
		  <id>aliyun</id>
		  <mirrorOf>central</mirrorOf>
		  <name>ali Mirror</name>
		  <url>https://maven.aliyun.com/repository/public</url>
	  </mirror>-->
  </mirrors>

  <!-- profiles
   | This is a list of profiles which can be activated in a variety of ways, and which can modify
   | the build process. Profiles provided in the settings.xml are intended to provide local machine-
   | specific paths and repository locations which allow the build to work in the local environment.
   |
   | For example, if you have an integration testing plugin - like cactus - that needs to know where
   | your Tomcat instance is installed, you can provide a variable here such that the variable is
   | dereferenced during the build process to configure the cactus plugin.
   |
   | As noted above, profiles can be activated in a variety of ways. One way - the activeProfiles
   | section of this document (settings.xml) - will be discussed later. Another way essentially
   | relies on the detection of a system property, either matching a particular value for the property,
   | or merely testing its existence. Profiles can also be activated by JDK version prefix, where a
   | value of '1.4' might activate a profile when the build is executed on a JDK version of '1.4.2_07'.
   | Finally, the list of active profiles can be specified directly from the command line.
   |
   | NOTE: For profiles defined in the settings.xml, you are restricted to specifying only artifact
   |       repositories, plugin repositories, and free-form properties to be used as configuration
   |       variables for plugins in the POM.
   |
   |-->
  <profiles>
    <!-- profile
     | Specifies a set of introductions to the build process, to be activated using one or more of the
     | mechanisms described above. For inheritance purposes, and to activate profiles via <activatedProfiles/>
     | or the command line, profiles have to have an ID that is unique.
     |
     | An encouraged best practice for profile identification is to use a consistent naming convention
     | for profiles, such as 'env-dev', 'env-test', 'env-production', 'user-jdcasey', 'user-brett', etc.
     | This will make it more intuitive to understand what the set of introduced profiles is attempting
     | to accomplish, particularly when you only have a list of profile id's for debug.
     |
     | This profile example uses the JDK version to trigger activation, and provides a JDK-specific repo.
    <profile>
      <id>jdk-1.4</id>

      <activation>
        <jdk>1.4</jdk>
      </activation>

      <repositories>
        <repository>
          <id>jdk14</id>
          <name>Repository for JDK 1.4 builds</name>
          <url>http://www.myhost.com/maven/jdk14</url>
          <layout>default</layout>
          <snapshotPolicy>always</snapshotPolicy>
        </repository>
      </repositories>
    </profile>
    -->

    <!--
     | Here is another profile, activated by the system property 'target-env' with a value of 'dev',
     | which provides a specific path to the Tomcat instance. To use this, your plugin configuration
     | might hypothetically look like:
     |
     | ...
     | <plugin>
     |   <groupId>org.myco.myplugins</groupId>
     |   <artifactId>myplugin</artifactId>
     |
     |   <configuration>
     |     <tomcatLocation>${tomcatPath}</tomcatLocation>
     |   </configuration>
     | </plugin>
     | ...
     |
     | NOTE: If you just wanted to inject this configuration whenever someone set 'target-env' to
     |       anything, you could just leave off the <value/> inside the activation-property.
     |
    <profile>
      <id>env-dev</id>

      <activation>
        <property>
          <name>target-env</name>
          <value>dev</value>
        </property>
      </activation>

      <properties>
        <tomcatPath>/path/to/tomcat/instance</tomcatPath>
      </properties>
    </profile>
    -->
    <profile>
		<id>father</id>
		<activation>
<!--			<activeByDefault>true</activeByDefault>-->
			<jdk>1.8</jdk>
		</activation>

		<repositories>
			<repository>
				<id>12302</id>
				<name>local private nexus</name>
				<url>http://mvn.wtfu.site/nexus/content/groups/public/</url>
				<releases>
					<enabled>true</enabled>
				</releases>
				<snapshots>
					<enabled>true</enabled>
				</snapshots>
			</repository>
		</repositories>
		
		<pluginRepositories>
			<pluginRepository>
				<id>aliyun-plugin</id>
				<name>aliyun-plugin-name</name>
				<url>http://maven.aliyun.com/nexus/content/groups/public/</url>
				<releases>
					<enabled>true</enabled>
				</releases>
				<snapshots>
					<enabled>false</enabled>
				</snapshots>
			</pluginRepository>
		</pluginRepositories>	
	</profile>
	    
  </profiles>

  <!-- activeProfiles
   | List of profiles that are active for all builds.
   |
  <activeProfiles>
    <activeProfile>alwaysActiveProfile</activeProfile>
    <activeProfile>anotherAlwaysActiveProfile</activeProfile>
  </activeProfiles>
  -->
<!--	<activeProfiles>
		<activeProfile>father</activeProfile>
	</activeProfiles>-->
</settings>
```
</details>

## pom.xml
<details><summary>示例</summary>

```xml
<?xml version="1.0" encoding="UTF-8"?>

<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <!--<parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.0.3.RELEASE</version>
        <relativePath/>
    </parent>-->
    
    <groupId>com.haohuo.framework</groupId>
    <artifactId>haohuo-component</artifactId>
    <version>1.0.0-SNAPSHOT</version>
    <packaging>pom</packaging>

    <name>haohuo-component</name>
    <url>https://wtfu.site/</url>

    <modules>
        <module>hh-cpt-executor</module>
        <module>hh-cpt-test</module>
        <module>hh-cpt-fake-log4j</module>
        <module>hh-cpt-fake-jcl</module>
        <module>hh-cpt-log4j-CVE</module>
        <module>hh-cpt-maven-conflict</module>
        <module>hh-cpt-maven-dependency</module>
        <module>hh-cpt-maven-plugin</module>
    </modules>

    <properties>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
        <maven.compiler.source>1.8</maven.compiler.source>
        <maven.compiler.target>1.8</maven.compiler.target>
        <java.version>1.8</java.version>
        <prod.config.url>http://120.26.161.40:8888/</prod.config.url>
        <test.config.url>http://112.124.46.254:8888/</test.config.url>
        <spring.boot.version>2.0.3.RELEASE</spring.boot.version>
        <spring.cloud.version>Finchley.RELEASE</spring.cloud.version>
        <haohuo.sleuth.starter.version>0.0.1.RELEASE</haohuo.sleuth.starter.version>
        <curator.version>2.10.0</curator.version>
    </properties>

    <dependencies>
        <dependency>
            <groupId>redis.clients</groupId>
            <artifactId>jedis</artifactId>
            <version>2.9.0</version>
        </dependency> 
    </dependencies>

    <dependencyManagement>
        <dependencies>
            <dependency>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-dependencies</artifactId>
                <version>2.0.3.RELEASE</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>

            <dependency>
                <groupId>org.springframework</groupId>
                <artifactId>spring-context</artifactId>
                <version>5.0.7.RELEASE</version>
            </dependency>
        </dependencies>
    </dependencyManagement>
    
    <repositories>
        
    </repositories>

    <build>
        <plugins>
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-source-plugin</artifactId>
                <version>2.1.1</version>
                <executions>
                    <execution>
                        <id>attach-sources</id>
                        <goals>
                            <goal>jar-no-fork</goal>
                        </goals>
                    </execution>
                </executions>
            </plugin>
        </plugins>
    </build>

    <!-- 发布到私服插件<mvn deploy> 的配置 -->
    <distributionManagement>
        <!--<repository>
            <id>rdc-releases</id>
            <name>Internal releases</name>
            <url>https://packages.aliyun.com/maven/repository/2066950-release-cbvBqh/</url>
        </repository>
        <snapshotRepository>
            <id>rdc-snapshots</id>
            <name>Internal snapshots</name>
            <url>https://packages.aliyun.com/maven/repository/2066950-snapshot-w6DSio/</url>
        </snapshotRepository>-->

        <repository>
            <id>rdc-releases</id>
            <url>https://repo.rdc.aliyun.com/repository/138059-release-UHEMVy/</url>
        </repository>
        <snapshotRepository>
            <id>rdc-snapshots</id>
            <url>https://repo.rdc.aliyun.com/repository/138059-snapshot-uQQQPY/</url>
        </snapshotRepository>

        <!--<repository>
            <id>releases</id>
            <name>Internal releases</name>
            <url>http://mvn.wtfu.site/nexus/content/repositories/releases</url>
        </repository>
        <snapshotRepository>
            <id>snapshots</id>
            <name>Internal snapshots</name>
            <url>http://mvn.wtfu.site/nexus/content/repositories/snapshots</url>
        </snapshotRepository>-->

    </distributionManagement>
</project>
```
</details>

## 插件篇
### maven-assembly-plugin
```xml
<!-- pom.xml -->
<plugin>
  <groupId>org.apache.maven.plugins</groupId>
  <artifactId>maven-assembly-plugin</artifactId>
  <version>3.4.2</version>
  <executions>
      <execution>
          <id>make-assembly</id>
          <phase>package</phase>
          <goals>
              <goal>single</goal>
          </goals>
      </execution>
  </executions>
  <configuration>
      <!-- see: https://github.com/apache/maven-assembly-plugin/blob/master/src/main/resources/assemblies/jar-with-dependencies.xml -->
      <!--<descriptorRefs>
          <descriptorRef>jar-with-dependencies</descriptorRef>
      </descriptorRefs>-->
      <descriptors>
          <descriptor>src/main/assembly/assembly.xml</descriptor>
      </descriptors>

      <finalName>youzan-crawler</finalName>
      <appendAssemblyId>true</appendAssemblyId>
      <outputDirectory>${project.build.directory}/archive-demo</outputDirectory>

      <!-- see: https://stackoverflow.com/questions/15530453/how-to-stop-the-maven-assembly-plugin-from-deploying-the-artifact-it-creates -->
      <attach>false</attach>

      <!--<archiveBaseDirectory>${pom.build.}/src</archiveBaseDirectory>-->
      <archive>
          <addMavenDescriptor>true</addMavenDescriptor>
          <manifest>
              <mainClass>com.test.youzan.Main2</mainClass>
              <addClasspath>false</addClasspath>

              <!--
                  see: https://docs.oracle.com/javase/tutorial/deployment/jar/downman.html
                  jar文件中的jar的加载必须由自定义的类加载器去完成,所以在fat-jar模式下 jar包内部的 lib/*.jar 不会生效。
              -->
              <classpathPrefix>lib/</classpathPrefix>
          </manifest>
      </archive>
  </configuration>
</plugin>
```
```xml
<!-- assembly/assembly.xml -->
<assembly xmlns="http://maven.apache.org/plugins/maven-assembly-plugin/assembly/1.1.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
          xsi:schemaLocation="http://maven.apache.org/plugins/maven-assembly-plugin/assembly/1.1.0 http://maven.apache.org/xsd/assembly-1.1.0.xsd">
    <id>${pom.version}</id>
    <formats>
        <!-- 指定打包格式。maven-assembly-plugin插件支持的打包格式有zip、tar、tar.gz (or tgz)、tar.bz2 (or tbz2)、jar、dir、war，可以同时指定多个打包格式 -->
        <format>jar</format>
    </formats>
    <!--指定打的包是否包含打包层目录，比如finalName是terminal-dispatch，当值为true，所有文件被放在包内的terminal-dispatch目录下，否则直接放在包的根目录下，-->
    <includeBaseDirectory>false</includeBaseDirectory>
    <fileSets>
        <fileSet>
            <!--将target class下 的打包到conf 因为我们可能用resource占位-->
            <directory>./target/classes</directory>
            <outputDirectory>/</outputDirectory>
            <includes>
                <include>**</include>
            </includes>
        </fileSet>
    </fileSets>
    <dependencySets>
        <dependencySet>
            <outputDirectory>/</outputDirectory>
            <scope>runtime</scope>
            <useProjectArtifact>false</useProjectArtifact>
        </dependencySet>
    </dependencySets>
</assembly>
```

### maven-shade-plugin
```xml
<hello></hello>
```


### spring-boot-maven-plugin
```xml
<plugin>
    <!--
      package org.springframework.boot.maven;
      @Mojo(
          name = "repackage",
          defaultPhase = LifecyclePhase.PACKAGE,
          requiresProject = true,
          threadSafe = true,
          requiresDependencyResolution = ResolutionScope.COMPILE_PLUS_RUNTIME,
          requiresDependencyCollection = ResolutionScope.COMPILE_PLUS_RUNTIME
      )
      public class RepackageMojo extends AbstractDependencyFilterMojo {
     -->
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-maven-plugin</artifactId>
    <version>2.0.3.RELEASE</version>
    <configuration>
        <mainClass>${Main.class}</mainClass>
    </configuration>
    <executions>
        <execution>
            <goals>
                <goal>repackage</goal>
            </goals>
        </execution>
    </executions>
</plugin>
```

### maven-dependency-plugin
```xml
<plugin>
    <groupId>org.apache.maven.plugins</groupId>
    <artifactId>maven-dependency-plugin</artifactId>
    <version>3.1.0</version>
    <executions>
        <execution>
            <id>copy-dependencies</id>
            <phase>package</phase>
            <goals>
                <goal>copy-dependencies</goal>
            </goals>
            <configuration>
                <!-- 把依赖的所有maven jar包拷贝到lib目录中 -->
                <outputDirectory>${project.build.directory}/archive-demo/lib</outputDirectory>
            </configuration>
        </execution>
    </executions>
</plugin>
```
### 
```xml
<profile>
    <id>code-generator</id>
    <activation>
        <activeByDefault>true</activeByDefault>
    </activation>

    <pluginRepositories>
        <pluginRepository>
            <id>gen-repo_id</id>
            <name>gen-repo-name</name>
            <url>https://mvn.wtfu.site/nexus/content/groups/public/</url>
            <snapshots>
                <enabled>true</enabled>
            </snapshots>
            <releases><enabled>false</enabled></releases>
        </pluginRepository>
    </pluginRepositories>

    <build>
        <plugins>
            <plugin>
                <groupId>com.haohuo.framework</groupId>
                <artifactId>gencode-maven-plugin</artifactId>
                <version>1.0.8-SNAPSHOT</version>

                <configuration>
                    <author>gen-code</author>
                    <info>false</info>
                    <swagger2>true</swagger2>
                    <output>${basedir}/src/main/java</output>
                    <username>username</username>
                    <password>{jXe28vG2lYEHY+7IlnlBVT0Mb3WSP+XUcKT1mlJrDHY=}</password>
                    <url><![CDATA[
                    jdbc:mysql://aliyuncs.com:3306/frame-dev?useUnicode=true&characterEncoding=utf8&zeroDateTimeBehavior=convertToNull&useSSL=false&serverTimezone=GMT%2B8&allowPublicKeyRetrieval=true
                            ]]></url>
                    <companySuffix>com.msb</companySuffix>
                    <moduleName>frame.admin</moduleName>
                    <contains>entity, mapper, service, impl, xml</contains>
                    <entityPkgName>domain</entityPkgName>

                    <tables>youzan_trade_record,</tables>

                    <settings>/Users/mac/software/apache-maven-3.3.9/conf/settings.xml</settings>
                    <accessKey>***</accessKey>


                </configuration>
            </plugin>
        </plugins>
    </build>
</profile>
```

## OTHER