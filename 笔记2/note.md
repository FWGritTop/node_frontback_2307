# 简单应用笔记

## Springboot
---
### 修改端口
application.properties下
``` js
server.port=8088
```

### springboot版本错误
![Alt text](image-5.png)
因为springboot3.0需要Java17以上的版本
降低springboot版本，改成2.7.12

### 热部署
![Alt text](image-6.png)
![Alt text](image-7.png)
```js
//pom.xml下
<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-devtools</artifactId>
			<optional>true</optional>
		</dependency>
```
```js
//application.properties下
spring.devtools.remote.restart.enabled=true
spring.devtools.restart.additional-paths="src/main/java"
```

## Vue
---
### 官网
[官网链接](https://v2.cn.vuejs.org/v2/guide/)
### 安装
用两行命令 __注意在管理员权限下安装__
安装vue
`npm install vue -g`

安装vue的脚手架使可以通过npm快速创建一个vue项目

`npm install -g @vue/cli`

查看是否安装成功

`vue --version`

###  创建项目
当前目录下cmd输入命令 __注意尽量选择管理员模式有时候会安装依赖__
`vue create 项目名`
选择第三个
![Alt text](image-8.png)
初学者把eslint去掉这个是语句有更强的语法要求，空格取消
![Alt text](image-9.png)
![Alt text](image-12.png)
是否要保存配置
![Alt text](image-10.png)![Alt text](image-11.png)
创建完成
![Alt text](image-13.png)
我们可以cd进文件夹
用`npm run serve`启动项目

## Axios
---
### 官网
[官网链接](https://www.axios-http.cn/)
### 安装
语句`npm install axios -g`
### vue全局配置
main.js中
```js
import axios from 'axios'
axios.defaults.baseURL ="http://localhost:8088"
Vue.prototype.$http =axios
```
### 还需要在当前目录下安装anxios依赖
`npm install --save axios `

### 跨域问题的解决
在后端spring controller里
类前加入`@CrossOrigin`
![Alt text](image-14.png)

### 使用
![Alt text](image-15.png)
![Alt text](image-16.png)
可以用v-bind与事件进行绑定

再通过调用$http来使用，代码：
```js
this.$http.put("/hello").then(res=>{
          this.info=res.data
          console.log('res',res)})
```

## VueRouter
---
### 官网
[官网链接](https://v3.router.vuejs.org/zh/installation.html)

### 安装
vue2:`npm install vue-router@3`
vue3:`npm install vue-router@4`

### main.js配置
![Alt text](image-17.png)
```js
import router from "./router"
```

### index.js
router目录下新建index.js
```js
import Vue from 'vue'
import VueRouter from 'vue-router'
Vue.use(VueRouter)
const router =new VueRouter({
    routes:[
        {path:"/",redirect:"/路径1"},
        {path:"/路径1",component:组件1},//配置对应router切换的组件
        {name:path:"/路径2",component:组件2,//params传参时必须要设置name
            children:[
                {path:"子路径名",component:子组件}]//注意路径不用加斜杠
            },
    ]
})

export default router
```

### 传参方式
params
```js
this.$router.push({
  name:'second',//注意name的设置是必要的
  params: {
    id:'123',
     name: '123'
  }
})
```
query
```js
this.$router.push({
    path:'second',
    query: {
        queryId:'123',
        queryName: '123'
    }
})
```
以上是函数式调用，声明式写在template里的类似

用vue-bind：绑定属性后进行例如：
```js
<router-link :to="{
	name:'list',
	params:{
		info:123,id:this.selected.toString()
	}}">点击这里</router-link>
```

## Mybatis
---
### 依赖
pom.xml依赖
```js
        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
            <version>5.1.49</version>
        </dependency>

        <dependency>
            <groupId>org.mybatis</groupId>
            <artifactId>mybatis</artifactId>
            <version>3.5.9</version>
        </dependency>
```

### 从 XML 中构建 SqlSessionFactory
resource 目录下建立xml配置文件
```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
  PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
  "https://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>
  <environments default="development">
    <environment id="development">
      <transactionManager type="JDBC"/>
      <dataSource type="POOLED">
        <property name="driver" value="${driver}"/>
        <property name="url" value="${url}"/>
        <property name="username" value="${username}"/>
        <property name="password" value="${password}"/>
      </dataSource>
    </environment>
  </environments>
  <mappers>
    <mapper resource="org/mybatis/example/BlogMapper.xml"/>
  </mappers>
</configuration>
```
driver填写驱动`com.mysql.jdbc.Driver`
url填写连接链接`jdbc:mysql://localhost:3306/nefu?useSSL=false`
username填写数据库账户
password填写数据库密码

mappers设置xml mapper.xml
对应的mapper.xml下面的路径
### 映射 SQL 语句
mapper.xml中写sql语句
```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
  PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
  "https://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="org.mybatis.example.BlogMapper">
  <select id="selectBlog" resultType="Blog">
    select * from Blog where id = #{id}
  </select>
</mapper>
```
mapper namespace 大概是类似于clas类名
select id 大概是对应类下面的方法名
resulType 对应的返回类型，可以写 map

### 使用
```java
	SqlSessionFactory sqlSessionFactory = null;
        SqlSession session = null;

        String resource = "mybatisconfig.xml";
        InputStream inputStream = Resources.getResourceAsStream(resource);
        sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);

        session = sqlSessionFactory.openSession();

        List<Object> objects = session.selectList("test.selectUser");

        for (Object object : objects) {
            System.out.println(object);
        }
```
大致思路
从mybatisoconfig.xml中获取配置
`SqlSessionFactoryBuilder().build`建立连接
`sqlSessionFactory.openSession()`建立对话
session调用`session.selectList("test.selectUser")`
`test`是mapper namespace的参数
`selectUser`是select id的参数

### 实例
src/main/resources/mybatisconfig.xml
```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "https://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>
    <environments default="development">
        <environment id="development">
            <transactionManager type="JDBC"/>
            <dataSource type="POOLED">
                <property name="driver" value="com.mysql.jdbc.Driver"/>
                <property name="url" value="jdbc:mysql://localhost:3306/nefu?useSSL=false"/>
                <property name="username" value="root"/>
                <property name="password" value="123456"/>
            </dataSource>
        </environment>
    </environments>
    <mappers>
        <mapper resource="mapper/nefuMapper.xml"/>
    </mappers>
</configuration>
```
src/main/resources/mapper/nefuMapper.xml
```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "https://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="test">
    <select id="selectUser" resultType="map">
        select * from user
    </select>
</mapper>
```
主函数
```java
package org.example;
import org.apache.ibatis.io.Resources;
import org.apache.ibatis.session.SqlSession;
import org.apache.ibatis.session.SqlSessionFactory;
import org.apache.ibatis.session.SqlSessionFactoryBuilder;
import java.io.IOException;
import java.io.InputStream;
import java.util.List;
public class mybatis {
    public static void main(String[] args) throws IOException {
        SqlSessionFactory sqlSessionFactory = null;
        SqlSession session = null;
        String resource = "mybatisconfig.xml";
        InputStream inputStream = Resources.getResourceAsStream(resource);
        sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);
        session = sqlSessionFactory.openSession();
        List<Object> objects = session.selectList("test.selectUser");
        for (Object object : objects) {
            System.out.println(object);
        }
    }
}
```

### 与实体对象的绑定
#### 实体对象的创建
新建一个java类
`alt+insert`或者是右键`生成`
![Alt text](image-18.png)
![Alt text](image-19.png)
#### 修改mapper.xml
如果实体类型的每一个名字都和数据库里的对应
直接把resultType修改成class类型即可
![Alt text](image-20.png)
如果实体类型的名字不对应，则需要resultmap来使他一一对应
![Alt text](image-21.png)

### 增删改查
#### 查一个
java代码
```java
int id=1;
user u = session.selectOne("test.selectUserByID",id);
```
xml代码
```xml
<select id="selectUserByID" resultMap="UserMap" parameterType="int">
        select * from  user where id = #{id}
    </select>
```
#### 插入
java代码
```java
user u = new user();
u.setUsername("wangwu");
u.setPassword("123");
u.setCreatetime(new Date());
u.setUpdateBy("wangwu");
int count = session.update("test.insert",u);
System.out.println("count = " + count);
session.commit();
```
xml代码
```xml
<insert id="insert" parameterType="entity.user" >
        INSERT INTO `nefu`.`user` (
        `username`,
        `password`,
        `createtime`,
        `update_by`
        )
        VALUES
        (
        #{username},
        #{password},
        #{createtime},
        #{updateBy}
        )
    </insert>
```
#### 修改
java代码
```java
int id=3;
user u = session.selectOne("test.selectUserByID",id);
u.setPassword("123333");
int count = session.update("test.updateById",u);
System.out.println("count = " + count);
session.commit();
```
xml代码
```xml
<update id="updateById" parameterType="entity.user">
        UPDATE
        `nefu`.`user`
        SET
        `username` =  #{username},
        `password` =  #{password},
        `createtime` =  #{createtime},
        `update_by` =  #{updateBy}
        WHERE `id` =  #{id}
    </update>
```
#### 删除
java代码
```java
int id=3;
        int count = session.delete("test.deletebyID",id);
        System.out.println("count = " + count);
        session.commit();
```
xml代码
```xml
<delete id="deletebyID" parameterType="int">
        DELETE FROM `user` WHERE `user`.`id` = #{id}
    </delete>
```

### 用接口来实现增删改查 仅需要写xml和接口类
#### xml文件
这里注意`mapper namespace`的参数是接口类的引用
```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "https://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="sqlctrl.UserMapper">
    <select id="selectUser" resultType="entity.user">
        select * from user
    </select>
</mapper>
```
#### 接口类的实现
函数名对应select的id如果是其他的操作则是其他的id
```java
public interface UserMapper {
    public List<user> selectUser();
}
```

#### 最终调用
重点是`session.getMapper(UserMapper.class)`完成一个mybatis内部的映射关系
```java
UserMapper userMapper = sqlSession.getMapper(UserMapper.class);
```

### sql工具类
通常直接写一个java类来获取session
来简化每次连接获取session会话
```java
package sqlctrl;
import java.io.InputStream;
import org.apache.ibatis.io.Resources;
import org.apache.ibatis.session.SqlSession;
import org.apache.ibatis.session.SqlSessionFactory;
import org.apache.ibatis.session.SqlSessionFactoryBuilder;
public class SqlSessionUtil {
    public static SqlSession getSqlSession() throws Exception{
        String resource = "mybatisconfig.xml";
        //使用类加载器加载mybatis的配置文件（它也加载关联的映射文件）
        InputStream is = Resources.getResourceAsStream(resource);
        //构建sqlSession的工厂
        SqlSessionFactory sessionFactory = new SqlSessionFactoryBuilder().build(is);
        //创建能执行映射文件中sql的sqlSession
        SqlSession sqlSession = sessionFactory.openSession();
        return sqlSession;
    }
}
```
使用
```java
package sqlctrl;
import entity.user;
import org.apache.ibatis.session.SqlSession;
import java.util.List;
public class testmybatis {
    public static void main(String[] args) throws Exception {
        SqlSession sqlSession = SqlSessionUtil.getSqlSession();
        UserMapper userMapper = sqlSession.getMapper(UserMapper.class);
        List<user> users=userMapper.selectUser();
        sqlSession.close();
    }
}
```


### Mybatis的mapper文件中$和#的区别
[Mybatis的mapper文件中$和#的区别](https://www.cnblogs.com/f-zhao/p/6171984.html)


## 数据库
---
### 创建
创建数据库

创建表
```sql
CREATE TABLE `nefu`.`user`(  
  `id` BIGINT NOT NULL AUTO_INCREMENT,
  `username` VARCHAR(500) COMMENT '用户名列',
  `password` VARCHAR(500),
  `createtime` DATETIME,
  PRIMARY KEY (`id`)
) ENGINE=INNODB CHARSET=utf8mb4;
```

## JDBC
---
### MySql依赖
```js
        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
            <version>5.1.49</version>
        </dependency>
```
### 注册驱动
```js
Class.forName("com.mysql.jdbc.Driver");
Class.forName("com.mysql.cj.jdbc.Driver");
//注意异常处理
```
本质区别：

com.mysql.jdbc.Driver 是 mysql-connector-java 5中的， 
com.mysql.cj.jdbc.Driver 是 mysql-connector-java 6以及以上中的

### 数据库连接
```js
Connection con = DriverManager.getConnection("jdbc:mysql://localhost:3306/数据库名称", "root", "xxx");
```

### 获取数据库对象
```js
Statement stat = con.createStatement();
```

### 执行sql语句
```js
String sql = "xxxx";
ResultSet re=stat.executeQuery(sql);
```

### 关闭连接
`con.close();`

### 结果集获取
 ResultSet：详解
 封装了结果集的对象：内部有一个可移动的光标，默认情况，指向第一条记录集的上一条记录：  
 
 next（）;光标下移动一次：返回的boolean的值;判断是否有结果可以被遍历：  
 previous（）;光标上移动一次： 
 last（）移动到最后一行： 
 afterLast（）;移动到最后一行之后：
 beforeFirst（）移动到第一行的之前：
 first（） 
 
 getObject
 getInt
 getString
 getFloat
 getDouble
 getDate
 getXxx

### 实例
```js
	Class.forName("com.mysql.jdbc.Driver");
        Connection con = DriverManager.getConnection("jdbc:mysql://localhost:3306/nefu?useSSL=false", "root", "123456");
        Statement stat = con.createStatement();
        String sql = "select * from user";
        ResultSet re=stat.executeQuery(sql);
        while(re.next()){
            System.out.println(re.getInt("id")+re.getString("password")+re.getString("password")+re.getTimestamp("createtime"));
        }
        con.close();
```

## Servlet
---
### 依赖
```js
<dependency>
      <groupId>javax.servlet</groupId>
      <artifactId>javax.servlet-api</artifactId>
      <version>3.1.0</version>
    </dependency>
    <dependency>
      <groupId>javax.servlet.jsp</groupId>
      <artifactId>javax.servlet.jsp-api</artifactId>
      <version>2.3.3</version>
    </dependency>

```
