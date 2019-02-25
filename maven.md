##xxxManagement和XXX的区别 

###plugins和pluginManagement区别 

pluginManagement 仅仅只是插件声明，只有放在plugins下的插件才会被maven加载并且执行。常被用作指定默认的plugin配置，子pom文件依赖的使用的时候，只需要配置groupId和artifactId即可。 

```xml 
例如父pom文件配置片段 

<build> 
    <pluginManagement> 
        <plugins> 
            <plugin> 
                <groupId>com.yihaodian.plugin.maven</groupId> 
                <artifactId>yhd-version-scanner-maven-plugin</artifactId> 
                <version>${yhd-version-scanner-maven-plugin.version}</version> 
                <executions> 
                    <id>dependencyScanner</id> 
                    <goals> 
                    <goal>dependencyScanner</goal> <!-- 依赖扫描Goal --> 
                    </goals> 
                    <configuration> 

                    .... 
                    </configuration> 
                </executions>
            </plugin>
        </plugins> 
    </pluginManagement> 
</build> 

子pom配置片段 

<build> 
	<plugin> 
        <groupId>com.yihaodian.plugin.maven</groupId> 
        <artifactId>yhd-version-scanner-maven-plugin</artifactId> 
    </plugin> 
</build> 

子pom只需要增加这个配置，maven就会加载该插件，并且执行该插件，如果不配置，此插件不会被加载 
```

####dependencyManagement和# dependencies区别 

与pluginMangemen，plugins的区别类似，此处常用的作用：统一管理项目的依赖版本号，确保项目的各个项目的依赖版本一致。 

##profiles 

多项目打包，类似环境参数，定义一组相同的属性（不同环境，属性值不一样），打包的时候，指定不同的环境参数即可。 

### profile的类型和定义的位置 

* 每个Project 

定义在pom.xml文件中 

* 每个用户 

定义在Maven-setting中(%USER_HOME%/.m2/settings.xml) 

* 全局的 

定义在global Maven-settings中(%M2_HOME%/conf/settings.xml) 

### 触发profile 

* 明确指定 

mvn -p profile-1 

* 通过Maven配置 

```xml 

<setting> 

... 

<activeProfiles> 

<activeProfile>profile-1</activeProfile> 

</activeProfiles> 

... 

</setting> 

定义在此处的profile，将在每一个project中使用 

```

* 通过环境变量 

```xml 

<profiles> 

<profile> 

<activation> 

<property> 

<name>environment</name> 

<value>test</value> 

</property> 

</activation> 

... 

</profile> 

</profiles> 

mvn -Denvironment=test激活profile 

```

* 操作系统设置 

```xml 

<profiles> 

<profile> 

<activation> 

<jdk>[1.3,1.6)</jdk> 

</activation> 

... 

</profile> 

</profiles> 

通过JDK的版本激活profile 

<profiles> 

<profile> 

<activation> 

<os> 

<name>Windows XP</name> 

<family>Windows</family> 

<arch>x86</arch> 

<version>5.1.2600</version> 

</os> 

</activation> 

... 

</profile> 

</profiles> 

通过操作系统版本号，触发profile 

```

* 判断文件是否存在 

## profile中可能涉及到的一些优先级问题 

以YHD-VERSION-SCANNER-PLUGIN插件为例进行讲解 

```xml 

1.setting 文件配置片段 

<settings> 

<activeProfiles> 

<activaProfile>maven-scanner</activaProfile> 

<activaProfile>local</activaProgile> 

</activeProfiles> 

... 

<profiles> 

<profile> 

<id>local</id> 

<properties> 

<dependency.scanner.skip>false</dependency.scanner.skip> 

<dependency.scanner.breakBuild>true</dependency.scanner.breakBuild> 

<properties> 

</profile> 

</profiles> 

</settings> 

2. publi-pom配置，父pom文件 

<build> 

<pluginManagement> 

<plugins> 

<plugin> 

<groupId>com.yihaodian.plugin.maven</groupId> 

<artifactId>yhd-version-scanner-maven-plugin</artifactId> 

<version>${yhd-version-scanner-maven-plugin.version}</version> 

<executions> 

<id>dependencyScanner</id> 

<goals> 

<goal>dependencyScanner</goal> <!-- 依赖扫描Goal --> 

</goals> 

<configuration> 

.... 

</configuration> 

</executions> 

</plugins> 

</pluginManagement> 

<profile> 

<id>maven-scanner</id> 

<build> 

<plugins> 

<plugin> 

<groupId>com.yihaodian.plugin.maven</groupId> 

<artifactId>yhd-version-scanner-maven-plugin</artifactId> 

</plugin> 

</plugins> 

</build> 

</profile> 

</build> 

3.子pom文件配置 

<build> 

<plugins> 

<plugin> 

<groupId>com.yihaodian.plugin.maven</groupId> 

<artifactId>yhd-version-scanner-maven-plugin</artifactId> 

<version>${yhd-version-scanner-maven-plugin.version}</version> 

<executions> 

<configuration> 

<skip>true</skip> 

<breakBuild>false</breakBuild> 

</configuration> 

</executions> 

</plugins> 

</plugin> 

</plugins> 

</build> 

此处执行的时候，还是会执行version-scanner这个插件，子类此时无法覆盖profile中的配置，说明profile中的配置优先级应该是最高的，其次才是子pom文件的配置，再去父pom文件寻找。 

当然profile中定义的properties这个值，也是可以被mvn xxx -Dxxx=xxx，这样在执行maven命令的覆盖掉 

```
