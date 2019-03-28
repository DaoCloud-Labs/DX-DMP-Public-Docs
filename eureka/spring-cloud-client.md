# Spring Boot & Spring Cloud 中使用服务发现

### 使用 RestTemplate

```java
@Component
class RestTemplateExample implements CommandLineRunner {

    @Autowired
    private RestTemplate restTemplate;

    @Override
    public void run(String... strings) throws Exception {
        ResponseEntity<List<Bookmark>> exchange =
                this.restTemplate.exchange(
                        // 此处的 bookmark-service 在运行时会转换为真实的 IP:PORT
                        "http://bookmark-service/{userId}/bookmarks",
                        HttpMethod.GET,
                        null,
                        new ParameterizedTypeReference<List<Bookmark>>() {
                        },
                        (Object) "mstine");

        exchange.getBody().forEach(System.out::println);
    }

}
```

### 使用 FeignClient

`FeignClient` 是由 `Spring` 提供的一个 **声明式** 的`HTTP Client`。详细参考 [附录 1](#附录)

```java
// 此处的 bookmark-service 在运行时会转换为真实的 IP:PORT
@FeignClient("bookmark-service")
interface BookmarkClient {

    @RequestMapping(method = RequestMethod.GET, value = "/{userId}/bookmarks")
    List<Bookmark> getBookmarks(@PathVariable("userId") String userId);
}
```

### 使用 DiscoveryClient

亦可以直接使用`Spring`提供的`Eureka Client`，直接获取到注册服务上的服务地址等信息。

```java
@Component
class DiscoveryClientExample implements CommandLineRunner {

    @Autowired
    private DiscoveryClient discoveryClient;

    @Override
    public void run(String... strings) throws Exception {
        discoveryClient.getInstances("bookmark-service").forEach((ServiceInstance s) -> {
            // 此处的 ServiceInstance 即真实的实例对象
            // 服务实例的端口号
            int port = s.getPort();
            // 服务实例的地址 (Hostname/IP) 决定于注册上来的地址
            String host = s.getHost();
            System.out.println(ToStringBuilder.reflectionToString(s));
        });
    }
}
```

## 附录

1. [Declarative REST Client: Feign](https://cloud.spring.io/spring-cloud-netflix/multi/multi_spring-cloud-feign.html)
