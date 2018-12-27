# 通过注解方式开启
Apollo支持注解的方式来接入配置中心。这也是最为推荐的一种做法。
此处所使用的源码在👉[Github](https://github.com/DaoCloud-Labs/DMP-Demo/blob/master/apollo/apollo-demo/README.md)

要使用Apollo的注解，你需要在maven或者gradle中引入相关依赖，比如：

## 1 引入依赖
- Maven方式，在`pom.xml`文件中添加如下依赖：

```xml
......
<dependency>
  <groupId>com.ctrip.framework.apollo</groupId>
  <artifactId>apollo-client</artifactId>
  <version>1.5</version>
</dependency>
......
 <!--从DaoCloud的Nexus拉取依赖-->
<repositories>
  <repository>
    <id>snapshots</id>
    <url>http://nexus.mschina.io/nexus/content/repositories/labs-snapshot/</url>
  </repository>
  <repository>
    <id>releases</id>
    <url>http://nexus.mschina.io/nexus/content/repositories/labs/</url>
  </repository>
</repositories>
......
```

- Gradle方式，在`build.gradle`文件中添加如下依赖：

```json
······
repositories {
    maven {
        url "http://nexus.mschina.io/nexus/content/groups/public/"
    }
}
······
dependencies {
    compile group: 'com.ctrip.framework.apollo', name: 'apollo-client', version: '1.5'
}
······
```

## 2 注解配置
如采用Spring Boot方式编写的Java应用程序，在程序启动类上面添加如下注解：

```java
@Configuration
@EnableApolloConfig({"application", "my-another-namespace"})
public class AnotherAppConfig {
	//······
}
```

**需要注意的是：**`@EnableApolloConfig`注解中的`application`和`my-another-namespace`是你在配置中心中创建的Namespace(命名空间)，按照实际情况填入。

## 3 启动传参
从配置中心拉取配置时，你需要告诉你的服务或者`apollo-client`去哪个配置组里面拉取配置，以及拉取配置的地址是什么。

最简单的方式是在运行程序时通过Vm Options传入参数:

```bash
app.id = ${在配置中心创建的AppId}
env = dmp
apollo.meta = http://192.168.2.96:8080 （这里是Apollo-Configservice的地址。）
```
当然，你也可以在运行Jar包时传入参数覆盖参数值：

```bash
java -Dapp.id=dmp -Denv=dmp -Dapollo.meta=http://192.168.2.96:8080 -jar your-app.jar
```
