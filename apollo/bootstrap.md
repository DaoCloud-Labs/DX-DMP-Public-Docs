# 一 通过配置文件开启配置中心

在Spring Boot初始bootstrap阶段注入配置，需要在applocation.properties或者application.yml开启此功能并将需要提前注入配置的Namespace的标记（相同key的配置以靠前的namespace为准）：

```
# 在bootstrap阶段注入默认application namespace的配置
apollo.bootstrap.enabled = true
# 指定加载的命名空间
apollo.bootstrap.namespaces = application,my-another-namespace
```
注意:使用了配置文件开启就可以不用在启动类上添加@EnableApolloConfig注解

demo地址 [apollodemo2](https://github.com/DaoCloud-Labs/DMP-Demo/tree/master/apollo/apollo-demo%202)

# 二 使用apollo配置中心实现对日志级别的修改以及动态生效

**通过配置使配置中心中的日志配置生效**

从1.2.0版本开始，即从DMP 1.5 版本开始，如果希望把日志相关的配置（
如logging.level.root=info或logback-spring.xml中的参数）
也放在Apollo管理，那么可以额外配置apollo.bootstrap.eagerLoad.enabled=true
来使Apollo的加载顺序放到日志系统加载之前，不过这会导致Apollo的启动过程
无法通过日志的方式输出(因为执行Apollo加载的时候，日志系统压根没有准
备好呢！所以在Apollo代码中使用Slf4j的日志输出便没有任何内容)参考配置示例如下：

配置示例：
```properties
# 将aopllo初始化放在日志系统初始化之前
apollop.bootstrap.eagerLoad.enabled=true
```

demo项目地址 [apollodemo3](https://github.com/DaoCloud-Labs/DMP-Demo/tree/master/apollo/apollo-demo3)

# 三 通过配置中心动态调整日志级别

**客户端使用spring boot自带的LoggingSystem的api来动态设置日志级别**

代码入下：
```java
@Service
public class DynamicLoggersConfig{
	Logger logger= LoggerFactory.getLogger(getClass());
	@ApolloConfig
	private Config config;
	private final static String LoggerTag="logging.level.";
	private final LoggingSystem loggingSystem;
	public DynamicLoggersConfig(LoggingSystem loggingSystem) {
		Assert.notNull(loggingSystem, "LoggingSystem must not be null");
		this.loggingSystem = loggingSystem;
	}
	@ApolloConfigChangeListener
	private void configChangeListter(ConfigChangeEvent changeEvent){
		SetkeyNames=config.getPropertyNames();
		for (String key:keyNames){
			if (StringUtils.containsIgnoreCase(key,LoggerTag)){
				String strLevel=config.getProperty(key,"info");
				LogLevel level = LogLevel.valueOf(strLevel.toUpperCase());
				loggingSystem.setLogLevel(key.replace(LoggerTag,""),level);
				logger.info("{}:{}",key,strLevel);
			}
		}
	}
}
```

配置和spring环境下正常好配置日志级别一样配置即可，如
> logging.level.org.springframework = info  
logging.level.org.hibernate = info

在apollo中修改发布之后等一会日志就能动态生效了

demo项目地址 [apollodemo4](https://github.com/DaoCloud-Labs/DMP-Demo/tree/master/apollo/apollo-demo4)
