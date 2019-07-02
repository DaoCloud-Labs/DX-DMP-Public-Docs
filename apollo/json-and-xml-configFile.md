# json/xml 配置文件操作文档

在引入了apollo-client的情况下在需要的地方加入如下代码获取xml以及json文件

1.获取xml类型的配置文件

```
//获取datasources.xml文件
xmlConfigFile = ConfigService.getConfigFile("datasources",ConfigFileFormat.XML);
//添加监听器
xmlConfigFile.addChangeListener(new ConfigFileChangeListener() {
  @Override
  public void onChange(ConfigFileChangeEvent changeEvent) {
    logger.info(changeEvent.toString());
  }
});
xmlConfigFile.getContent(); //获取xml的配置内容
...业务处理
```

2.获取json类型的配置文件
```
//获取datasources.json文件
jsonConfigFile = ConfigService.getConfigFile("datasources",ConfigFileFormat.JSON);
//添加监听器
jsonConfigFile.addChangeListener(new ConfigFileChangeListener() {
  @Override
  public void onChange(ConfigFileChangeEvent changeEvent) {
    logger.info(changeEvent.toString());
  }
});
jsonConfigFile.getContent(); //获取json的配置内容
....业务处理
```
