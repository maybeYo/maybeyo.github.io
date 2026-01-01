---
title: activemq
date: 2021-07-28 17:34:30
tags: 
  - Linux 
  - activemq
categories: 
  - Linux
---

activemq配置文件

<!--more-->

​在使用ActiveMQ的时候，一般会对其分配用户和角色来做基本的权限验证，本博文选择自带的JAAS Plugin来完成。本文中ActiveMQ版本选择5.16.0

​在LINUX上下载解压完ActiveMQ之后进入到conf目录下，其中有四个文件需要特别关注：

- login.config  - 登录配置
- users.properties - 用户密码配置
- groups.properties - 用户组配置
- activemq.xml - ActiveMQ实例配置

## login.config

```txt
activemq {
    org.apache.activemq.jaas.PropertiesLoginModule required
        org.apache.activemq.jaas.properties.user="users.properties"
        org.apache.activemq.jaas.properties.group="groups.properties"
	reload=true;
};
```

---

​在没有设置`java.security.auth.login.config`属性时，会默认读取这个文件作为配置；可以看到该文件中，先是定义了一个节点名`activemq`，第二行表示使用的是`PropertiesLoginModule`来获取认证信息（其他的方式请参考官网），顾名思义就是从配置文件中读取，所以下面就配置了使用`users.properties`文件作为用户密码配置，使用`groups.properties`文件作为用户组配置。默认在当前文件同一目录中查找这两个文件。`reload=true`，表示可以在运行过程中更改`users.properties`，`groups.properties`文件内容，并动态刷新；**不要忘了最后的** `";"`。

---

## users.properties

```shell
## username = password
admin=admin
wuser=wuser
ruser=ruser
```

在该文件中定义用户，格式：用户名=密码；

## groups.properties

```shell
## rolename = username1,username2
admins=admin
writes=wuser
reads=ruser
```

在该文件中定义角色，格式：角色名=用户1，用户2...

## activemq.xml

```txt
    <persistenceAdapter>
        <kahaDB directory="${activemq.data}/kahadb"/>
    </persistenceAdapter>
 
	<plugins>
 
	<!--  use JAAS to authenticate using the login.config file on the classpath to configure JAAS -->
		<jaasAuthenticationPlugin configuration="activemq" />
	<!--  lets configure a destination based authorization mechanism -->
		<authorizationPlugin>
		   <map>
			<authorizationMap>
				<authorizationEntries>
					<authorizationEntry topic=">" read="reads,writes" write="writes" admin="writes,admins" />
					<authorizationEntry queue=">" read="reads,writes" write="writes" admin="writes,admins" />
					<authorizationEntry topic="ActiveMQ.Advisory.>" read="reads,writes,admins" write="reads,writes,admins" admin="reads,writes,admins"/>
					<authorizationEntry queue="ActiveMQ.Advisory.>" read="reads,writes,admins" write="reads,writes,admins" admin="reads,writes,admins"/>
				</authorizationEntries>
			</authorizationMap>
		   </map>
		</authorizationPlugin>
	</plugins>
```

---

​主要是`plugins`节点，将其放置在persistenceAdapter节点下面，用于配置权限映射。

​其中`jaasAuthenticationPlugin`的`configuration`属性必须要在`login.config`文件中存在，上面介绍`login.config`文件时，定义了一个`activemq`的节点，所以此处同样需要设置为`activemq`。

​下面的authorizationEntry节点中

---

## queue/topic

​代表权限是作用在queue还是topic上，其值是一个通配符表达式，通配符表达式如下，用于匹配queue/topic的名字。

| .   | 分隔符;分隔名字,*,> |
| --- | ------------------- |
| *   | 匹配任何字符        |
| >   | 任何字符直到末尾    |

例如:

​仅单个">" 和单个"*"都表示匹配全部

​"PREFIX.>"或"PREFIX.*"都表示以PREFIX开头的中间以"."分隔；可以匹配： PREFIX , PREFIX.1 , PREFIX.1.2 ；无法匹配： PREFIX1

​"PREFIX.*.SUFFIX"表示以PREFIX开头，以SUFFIX结尾；可以匹配：PREFIX.1.SUFFIX；无法匹配：PREFIX.1.2.SUFFIX，PREFIX.1.SUFFIX.2

​注意，如果要使用">"，那么一定要将其放在表达式的最后。具体信息可以访问[官网](https://activemq.apache.org/wildcards)。

## read/write/admin

| 属性  | 描述                 |
| ----- | -------------------- |
| read  | 查看和消费目的地数据 |
| write | 往目的地发送数据     |
| admin | 创建目的地           |

read/write/admin 其值是角色名，多个使用","分隔，角色名在groups.properties文件中配置。**注意：如果目的地还没创建出来的话，拥有write权限的角色是无法往该目的地发送数据的。**

---

​以上，就是ActiveMQ的权限验证配置。如果是配置ActiveMQ WEB端的权限，则需要在jetty.xml 和 jetty-realm.xml中进行配置

## 1. jetty.xml 配置 ActiveMQ 的web Console 控制台端口:

在`jetty.xml`文件中:

```shell
<bean id="jettyPort" class="org.apache.activemq.web.WebConsolePort" init-method="start">
             <!-- the default port number for the web console -->
        <property name="host" value="0.0.0.0"/>
        <property name="port" value="8161"/></bean>
```

## 2. jetty-realm.properties ActiveMQ 的 web Console 控制台用户名密码配置:

当登录 http://localhost:8161 进入管理界面时,输入用户名密码配置在文件`jetty-realm.properties`中,添加一个用户为"aries"密码为"123"的管理员如下:

```shell
# Defines users that can access the web (console, demo, etc.)
# username: password [,rolename ...]
admin: admin, admin
user: user, user
aries: 123, admin
```

---

第一列为用户名,第二列是密码,第三列表示角色。

---

## 3.设置MQ的持久化方式

ActiveMQ 默认使用 `KaHadb` 进行持久化消息存储, 配置在 `ActiveMQ.xml` 文件中:

```shell
<persistenceAdapter>
            <kahaDB directory="${activemq.data}/kahadb"/>
</persistenceAdapter>
```

 现在我们将持久化存储方式修改为 `mysql` ,则修改上面的配置文件如下:

```shell
<persistenceAdapter>

         <jdbcPersistenceAdapter  dataSource="#derby-ds"/>

</persistenceAdapter>
```

同时添加`mysq数据源`的配置:

```shell
<bean id="derby-ds" class="org.apache.commons.dbcp.BasicDataSource" destroy-method="close">
　　<property name="driverClassName" value="com.mysql.jdbc.Driver"/>
　　<property name="url" value="jdbc:mysql://localhost:3306/activemq?relaxAutoCommit=true"/>
　　<property name="username" value="root"/>
　　<property name="password" value="123"/>
　　<property name="maxActive" value="200"/>
　　<property name="poolPreparedStatements" value="true"/>
</bean>
```

添加mysql驱动等依赖包到ActiveMQ的lib目录下:

- mysql-connector-[Java](http://lib.csdn.net/base/javase)-5.1.30-bin.jar

- commons-dbcp-1.4.jar
- commons-pool-1.6.jar

​在 mysql 中创建  activemq  数据库,然后启动 ActiveMQ，如果数据test库中生成 `activemq_acks`，`activemq_lock`，`activemq_msgs`三张表,则证明mysql持久化存储配置完成。

### 3.配置消息接收发送顺序按照优先级进行:

​在发送消息的时候我们可以设置消息的优先级,来确定消息的接收顺序(对于单个MQ来说,如果是集群就不能确定优先级顺序了),优先级的使用需要在ActiveMQ.xml配置文件中进行开启:

```shell
 <policyEntry queue=">"  prioritizedMessages="true" />  
```

参考连接：[CSDN    https://blog.csdn.net](https://blog.csdn.net/qq_38167579/article/details/115530052)
