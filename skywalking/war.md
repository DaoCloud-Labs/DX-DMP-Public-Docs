# War包接入

接入War包的方式和[Jar包接入](jar.md)方式大致相同，只是添加`-javaagent`参数位置不同，需要修改Tomcat中的`CATALINA_OPTS `变量。

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

## War包容器化

### Dockerfile编写,参考[源码](https://github.com/DaoCloud-Labs/StrutsSpringExample/blob/dmp/Dockerfile)

```bash
FROM tomcat:7.0.94-jre7

ENV TZ=Asia/Shanghai \
    DIST_NAME=query-service \
    AGENT_REPO_URL="http://nexus.mschina.io/nexus/service/local/repositories/labs/content/io/daocloud/mircoservice/skywalking/agent/2.0.1/agent-2.0.1.gz"

ADD $AGENT_REPO_URL /

RUN set -ex; \
    tar -zxf /agent-2.0.1.gz; \
    rm -rf agent-2.0.1.gz;

RUN ln -sf /usr/share/zoneinfo/$TZ /etc/localtime \
    && echo $TZ > /etc/timezone

ADD catalina.sh /usr/local/tomcat/bin/

ADD target/struts-spring-demo.war /usr/local/tomcat/webapps/

WORKDIR /usr/local/tomcat/webapps/

CMD ["catalina.sh","run"]

```

