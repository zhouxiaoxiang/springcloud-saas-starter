# springcloud-saas-starter

  增加一个starter即可，此处只是方案说明，由于编写这套软件时所在公司的文化限制，代码不能公开。

## 软件环境

- 支持基于spring cloud微服务多租户
- 支持 **Jdk 1.8+, SpringBoot 2.x.x**

## 使用方法

1. 所有微服务的pom.xml中增加

```xml
        <dependency>
            <groupId>eric.cloud</groupId>
            <artifactId>springcloud-saas-starter</artifactId>
            <version>0.0.1-SNAPSHOT</version>
        </dependency>
```

2. 使用 **@TENANT** 支持多租户

|       注解        |         结果         |
| :---------------: | :-----------------:  |
|    没有@TENANT    |     默认数据源       |
|    @TENANT       |    使用租户数据源     |

```java
@TENANT 
@Service
public class UserServiceImpl implements UserService {
  @Override
  public List selectByCondition() {
      return null;
  }
}
```

3. nginx.conf

```nginx
server {
        listen       8080;
        server_name ~^(?<name>.+)\.zhouxiaoxing\.top;
        charset utf-8;
    
        location / {
          proxy_pass http://localhost:8081;
          proxy_redirect off;
          proxy_set_header Host $host;
          proxy_set_header X-Real-IP $remote_addr;
          proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
          proxy_set_header tenantId $name;
          client_max_body_size 1024m;
          client_body_buffer_size        128k;
          proxy_buffer_size              128k;
          proxy_buffers                  16 128k;
          proxy_busy_buffers_size        512k;
          proxy_temp_file_write_size     512k;
        }
}
```

4. 用户访问

  通过zhouxiaoxiang.top进入管理控制台
  通过tenant1.zhouxiaoxiang.top进入租户1
  通过tenant2.zhouxiaoxiang.top进入租户2

## 管理数据源

### 查看租户数据源

```
GET /datasources
```

#### Responses

|HTTP Code|Description|Schema|
|---|---|---|
|**200**|OK|[CommonResponse](#commonresponse)|

<a name="commonresponse"></a>
### CommonResponse

|Name|Description|Schema|
|---|---|---|
|**code**  <br>*required*|返回码|string|
|**message**  <br>*required*|返回消息|string|
|**res**  <br>*optional*||object|
|**success**  <br>*optional*||boolean|

![](images/all.png)

### 增加租户数据源

```
POST /datasources/add
```

#### Parameters

|Type|Name|Description|Schema|
|---|---|---|---|
|**Body**|**dto**  <br>*required*|dto|[DataSourceDTO](#datasourcedto)|

<a name="datasourcedto"></a>
### DataSourceDTO

|Name|Description|Schema|
|---|---|---|
|**driverClassName**  <br>*optional*|JDBC驱动  <br>**Example** : `""`|string|
|**poolName**  <br>*optional*|数据源的名称  <br>**Example** : `"tenant1"`|string|
|**url**  <br>*optional*|JDBC地址  <br>**Example** : `""`|string|
|**username**  <br>*optional*|JDBC用户名|string|
|**password**  <br>*optional*|JDBC密码|string|

#### Responses

|HTTP Code|Description|Schema|
|---|---|---|
|**200**|OK|[CommonResponse](#commonresponse)|

### 删除租户数据源

```
DELETE /datasources
```

#### Parameters

|Type|Name|Description|Schema|
|---|---|---|---|
|**Query**|**name**  <br>*optional*|name|string|

#### Responses

|HTTP Code|Description|Schema|
|---|---|---|
|**200**|OK|[CommonResponse](#commonresponse)|

![](images/delete.png)

```
