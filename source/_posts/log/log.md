---
title: 后端自定义log使用手册
date: 2022-11-24 13:28:11
tags: log
top_img:
---

## 一、执行建表语句，生成sys_oper_log表

![image-20221124110317539](images/log/image-20221124110317539.png)

## 二、项目中引入必要依赖

```
<!-- JSON 解析器和生成器 -->
<dependency>
    <groupId>com.alibaba.fastjson2</groupId>
    <artifactId>fastjson2</artifactId>
    <version>${fastjson.version}</version>
</dependency>

<!-- elasticsearch -->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-elasticsearch</artifactId>
    <version>2.3.3.RELEASE</version>
</dependency>
```

## 三、将log文件夹整体复制到下图位置

![image-20221124110535664](images/log/image-20221124110535664.png)

## 四、在需要抓取日志的controller中的方法上添加注解@Log

例：

```java
@ApiOperation(value = "分页查询列表展示信息")
    @PostMapping("/find/list")
    @Log(title = "种植企业列表", businessType = SELECT, operatorType = MANAGE)
    public ResponseResult<PageInfo<Map<String,Object>>> findPageList(@RequestBody Map<String,Object> entity, Integer page, Integer size) {
        return ResponseResult.success(plantService.findPageList(entity,page,size));
    }
```

注解中有五种参数可以供需添加

```
public @interface Log {
    /**
     * 模块
     */
    String title() default "";

    /**
     * 功能(查询，插入，更新等)
     */
    BusinessType businessType() default BusinessType.OTHER;

    /**
     * 操作人类别
     */
    OperatorType operatorType() default OperatorType.OTHER;

    /**
     * 是否保存请求的参数
     */
    boolean isSaveRequestData() default true;

    /**
     * 是否保存响应的参数
     */
    boolean isSaveResponseData() default true;
}
```

## 五、访问此接口即可生成日志

日志会同步存入mysql及Elasticsearch，代码位置如下，如果不想存入es，注释掉下方es代码即可

![image-20221124111949917](images/log/image-20221124111949917.png)



**如果要存入Elasticsearch，则先要启动Elasticsearch，es下载启动方式如下**

### **1.先下载es和kibana的包**

kibana是为Elasticsearch设计的开源分析和可视化平台，在win7无法启动，不下载也行，不耽误在系统中查询

```
1 Elastic 的官方网站：https://www.elastic.co/
2 ElasticSearch7.16.2 ：https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-7.16.2-windows-x86_64.zip
3 kibana7.16.2：https://artifacts.elastic.co/downloads/kibana/kibana-7.16.2-linux-windows-x86_64.zip
```

### **2.修改配置参数  /config/elasticsearch.yml**

```
1 #集群名称
2 cluster.name: es
3 #集群节点名称
4 node.name: node-173
5 #绑定的IP地址
6 network.host: 192.168.190.173
7 #HTTP的端口号
8 http.port: 9200
9 #新节点用于加入集群的主节点列表
10 discovery.seed_hosts: ["192.168.190.173"]
11 #bootstrap集群的初始列表
12 cluster.initial_master_nodes: ["node-173"]
13 path.data=指向一个存储空间比较大的分区
14 path.logs=指向一个存储空间比较大的分区
```

### **3.启动  /bin/elasticsearch.bat**

启动后访问 192.168.190.173:9200,出现以下所示即启动成功

```
{
  "name" : "node-173",
  "cluster_name" : "es",
  "cluster_uuid" : "fMjQC2VwTJmZQzR3e23aEw",
  "version" : {
    "number" : "7.16.2",
    "build_flavor" : "default",
    "build_type" : "zip",
    "build_hash" : "2b937c44140b6559905130a8650c64dbd0879cfb",
    "build_date" : "2021-12-18T19:42:46.604893745Z",
    "build_snapshot" : false,
    "lucene_version" : "8.10.1",
    "minimum_wire_compatibility_version" : "6.8.0",
    "minimum_index_compatibility_version" : "6.0.0-beta1"
  },
  "tagline" : "You Know, for Search"
}
```

### **4.然后在application中增加此配置，es有用户名密码的话可自行添加配置**

![image-20221124111744052](images/log/image-20221124111744052.png)

## 六、目录结构说明



![image-20221124133619713](images/log/image-20221124133619713.png)


```
百度网盘
链接: https://pan.baidu.com/s/1uzU0wPWh8lLAl6YzzPC8Lg 
提取码: gyeu 
```