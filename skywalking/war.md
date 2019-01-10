# War包接入

接入War包的方式和[Jar包接入](jar.md)方式大致相同，只是添加`-javaagent`参数位置不同。

### 修改`Tomcat中CATALINA_OPTS变量`
- 在`Linux Tomcat 7, Tomcat 8`环境下：
修改`tomcat/bin/catalina.sh`的第一行：

```bash
CATALINA_OPTS="$CATALINA_OPTS -javaagent:/path/to/skywalking-agent/skywalking-agent.jar"; export CATALINA_OPTS
```

- 在`Windows Tomcat 7, Tomcat 8`环境下：
修改`tomcat/bin/catalina.bat`的第一行：

```bash
set "CATALINA_OPTS=-javaagent:/path/to/skywalking-agent/skywalking-agent.jar"
```