## War 包接入

接入 War 包的方式和 Jar 包接入方式大致相同，只是添加 -javaagent 参数位置不同，需要修改 Tomcat 中的`CATALINA_OPTS`变量。

##### 修改Tomcat中CATALINA_OPTS变量
在Linux Tomcat 7, Tomcat 8环境下： 修改tomcat/bin/catalina.sh的第一行：
```bash
CATALINA_OPTS="$CATALINA_OPTS -javaagent:/path/to/vedfolnir-agent.jar"; export CATALINA_OPTS
```
在Windows Tomcat 7, Tomcat 8环境下： 修改tomcat/bin/catalina.bat的第一行：
```bash
set "CATALINA_OPTS=-javaagent:/path/to/vedfolnir-agent.jar"
```

## War 包容器化
```
FROM maven:3.6.1-jdk-7-slim

COPY settings.xml /root/.m2/ 

ADD . /workspace

WORKDIR /workspace

RUN mvn package -U

FROM tomcat:7.0.94-jre7-alpine

RUN apk add curl

ENV AGENT_REPO_URL="https://nexus.daocloud.io/repository/labs-snapshot/io/daocloud/mircoservice/vedfolnir-agent/1.0-SNAPSHOT/vedfolnir-agent-1.0.jar"
RUN curl -X GET $AGENT_REPO_URL > /agent.jar

ADD catalina.sh /usr/local/tomcat/bin/
COPY --from=0 /workspace/target/example-1.0-SNAPSHOT.war /usr/local/tomcat/webapps/

RUN chmod +x /usr/local/tomcat/bin/catalina.sh
CMD ["/usr/local/tomcat/bin/catalina.sh","run"]
```
相关环境变量请参考👉[配置参数说明](agent-settings.md)

