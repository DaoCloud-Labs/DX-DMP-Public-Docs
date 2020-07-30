# 无客户端 SDK 服务发现

针对一些非 `JVM` 的语言，又无 `SDK` 的实现。最终可以选择最为简单的 `Http API` 的方式进行服务表的获取。

## 获取所有的注册服务

```bash
curl --request GET \
  --url http://${YOUR_EUREKA_ADDRESS}/eureka/v2/apps \
```

会获得形如以下的 `XML` 格式的报文返回：

```xml
<applications>
  <versions__delta>1</versions__delta>
  <apps__hashcode>UP_48_</apps__hashcode>
  <application>
    <name>BOOKMARK-SERVICE</name>
    <instance>
      <instanceId>192.168.1.148:bookmark-service:8762</instanceId>
      <hostName>192.168.1.148</hostName>
      <app>BOOKMARK-SERVICE</app>
      <ipAddr>192.168.1.148</ipAddr>
      <status>UP</status>
      <overriddenstatus>UNKNOWN</overriddenstatus>
      <port enabled="true">8762</port>
      <securePort enabled="false">443</securePort>
      <countryId>1</countryId>
      <dataCenterInfo class="com.netflix.appinfo.InstanceInfo$DefaultDataCenterInfo">
        <name>MyOwn</name>
      </dataCenterInfo>
      <leaseInfo>
        <renewalIntervalInSecs>30</renewalIntervalInSecs>
        <durationInSecs>90</durationInSecs>
        <registrationTimestamp>1551854657222</registrationTimestamp>
        <lastRenewalTimestamp>1551855922809</lastRenewalTimestamp>
        <evictionTimestamp>0</evictionTimestamp>
        <serviceUpTimestamp>1551854657222</serviceUpTimestamp>
      </leaseInfo>
      <metadata>
        <management.port>8762</management.port>
      </metadata>
      <homePageUrl>http://192.168.1.148:8762/</homePageUrl>
      <statusPageUrl>http://192.168.1.148:8762/info</statusPageUrl>
      <healthCheckUrl>http://192.168.1.148:8762/health</healthCheckUrl>
      <vipAddress>manage-system</vipAddress>
      <secureVipAddress>manage-system</secureVipAddress>
      <isCoordinatingDiscoveryServer>false</isCoordinatingDiscoveryServer>
      <lastUpdatedTimestamp>1551854657222</lastUpdatedTimestamp>
      <lastDirtyTimestamp>1551854657063</lastDirtyTimestamp>
      <actionType>ADDED</actionType>
    </instance>
  </application>
</applications>
```

其中的 `<application>` 标签内是服务，由多个`<instance>`构成，`<instance>`中 `<ipAddr>`与`<port>` 即是服务实例的地址所在。

### 查询单个服务的实例列表

```bash
curl --request GET \
  --url http://${YOUR_EUREKA_ADDRESS}/eureka/v2/apps/{YOUR_APP_ID} \
```

结果同上

### 查询单个服务的单个实例信息

```bash
curl --request GET \
  --url http://${YOUR_EUREKA_ADDRESS}/eureka/v2/apps/{YOUR_APP_ID}/{YOUR_INSTANCE_ID} \
```

结果同上

## 附录

- [Eureka Restful API](https://github.com/Netflix/eureka/wiki/Eureka-REST-operations)
