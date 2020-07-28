# 通过注解方式开启
Apollo支持注解的方式来接入配置中心。这也是最为推荐的一种做法。
此处所使用的DaoShop中`daoshop-admin`服务源码在👉[Github](https://github.com/DaoCloud-Labs/daoshop-admin)

要使用Apollo的注解，你需要在maven或者gradle中引入相关依赖，比如：

## 1 引入依赖
- Maven方式，在`pom.xml`文件中添加如下依赖：

```
        <!--import apollo-client-->
        <dependency>
            <groupId>com.ctrip.framework.apollo</groupId>
            <artifactId>apollo-client</artifactId>
            <version>2.4.0.DMP.RELEASE</version>
        </dependency>
        
        <repository>
            <id>maven-public</id>
            <name>maven-public</name>
            <url>https://nexus.daocloud.io/repository/maven-public/</url>
        </repository>
```

- Gradle方式，在`build.gradle`文件中添加如下依赖：

```
repositories {
    maven {
        url "https://nexus.daocloud.io/repository/maven-public/"
    }
}
dependencies {
    compile group: 'com.ctrip.framework.apollo', name: 'apollo-client', version: '2.4.0.DMP.RELEASE'
}
```

## 2 注解配置
如采用Spring Boot方式编写的Java应用程序，在程序启动类上面添加如下注解：

```java
@Configuration
@EnableApolloConfig({"application", "my-another-namespace", "application.yml"})
public class AnotherAppConfig {
	//······
}
```

**需要注意的是：** `@EnableApolloConfig`注解中的`application`和`my-another-namespace`是你在配置中心中创建的Namespace(命名空间)，按照实际情况填入。
除了properties格式的namespace不需要加后缀，其他都要加后缀。比如.yml, .yaml, .xml 等。

## 3 启动传参
从配置中心拉取配置时，你需要告诉你的服务或者`apollo-client`去哪个配置组里面拉取配置，以及拉取配置的地址是什么。

最简单的方式是在运行程序时通过Vm Options传入参数:

```bash
app.id = ${环境code}.${在配置中心创建的AppId}
apollo.configService = http://192.168.2.96:8080 （这里是Apollo-ConfigService的地址。）
```
当然，你也可以在运行Jar包时传入参数覆盖参数值：

```bash
java -Dapp.id=dmp -Dapollo.configService=http://192.168.2.96:8080 -jar your-app.jar
或者通过环境变量覆盖配置：
APOLLO_CONFIGSERVICE=http://192.168.2.96:8080 APOLLO_APP_ID=test240.dmp java -jar your-app.jar
```
