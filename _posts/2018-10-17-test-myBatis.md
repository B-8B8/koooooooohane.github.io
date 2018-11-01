---
title: 练习myBatis框架(记录)
key: 2018-10-17
tags: mybatis
---

## 1.创建数据库
```
CREATE DATABASE mybatis CHARACTER SET utf8 COLLATE utf8_general_ci;
USE mybatis;
CREATE TABLE depttable(id INT(5) PRIMARY KEY NOT NULL AUTO_INCREMENT ,deptName VARCHAR(50) NOT NULL,`floor` VARCHAR(50) NOT NULL)ENGINE=INNODB DEFAULT CHARSET=utf8 AUTO_INCREMENT=1;
CREATE TABLE usertable(id INT(5) PRIMARY KEY NOT NULL AUTO_INCREMENT ,userName VARCHAR(50) NOT NULL,deptId INT(5))ENGINE=INNODB DEFAULT CHARSET=utf8 AUTO_INCREMENT=1;

INSERT INTO depttable (deptName,`floor`) VALUES ('营销部','1f-1101');
INSERT INTO depttable (deptName,`floor`) VALUES ('设计部','1f-1102');
INSERT INTO depttable (deptName,`floor`) VALUES ('研发部','2f');

INSERT INTO usertable (userName) VALUES ('王小花');
INSERT INTO usertable (userName) VALUES ('刘磊');
INSERT INTO usertable (userName) VALUES ('刘飞');
INSERT INTO usertable (userName) VALUES ('聂兵');
INSERT INTO usertable (userName) VALUES ('胡毅');
INSERT INTO usertable (userName) VALUES ('张菲菲');
INSERT INTO usertable (userName) VALUES ('李倩');

```

## 2.创建项目和包
### mybatis_exercise(项目)
* src [default]
  * com.ahchentong.entity [package]
    * User.java [实体]
    * Dept.java [实体]
  * com.ahchentong.mapper [package]
    * UserMapper.java [接口]
    * DeptMapper.java [接口]
    * UserMapper.xml [绑定]
    * DeptMapper.xml [绑定]
  * com.ahchentong.test [package]
* config [Source Folder]
  * log4j.xml [日志配置]
  * mybatis-config.xml [mybatis主配置]
  * db.properties [外部资源文件 k-v键值对]

## 3.实操
### 3-1 User.java [实体]

```
package com.ahchentong.entity;

public class User {
	private Integer id;
	private String userName;
	private Integer deptId;
	@Override
	public String toString() {
		return "User [id=" + id + ", userName=" + userName + ", deptId=" + deptId + "]";
	}
	public Integer getId() {
		return id;
	}
	public void setId(Integer id) {
		this.id = id;
	}
	public String getUserName() {
		return userName;
	}
	public void setUserName(String userName) {
		this.userName = userName;
	}
	public Integer getDeptId() {
		return deptId;
	}
	public void setDeptId(Integer deptId) {
		this.deptId = deptId;
	}
	public User(Integer id, String userName, Integer deptId) {
		super();
		this.id = id;
		this.userName = userName;
		this.deptId = deptId;
	}
	public User() {
		super();
		// TODO Auto-generated constructor stub
	}
}

```

### 3-2 Dept.java [实体]

```
package com.ahchentong.entity;

public class Dept {
	private Integer id;
	private String deptName;
	private String floor;
	@Override
	public String toString() {
		return "Dept [id=" + id + ", deptName=" + deptName + ", floor=" + floor + "]";
	}
	public Integer getId() {
		return id;
	}
	public void setId(Integer id) {
		this.id = id;
	}
	public String getDeptName() {
		return deptName;
	}
	public void setDeptName(String deptName) {
		this.deptName = deptName;
	}
	public String getFloor() {
		return floor;
	}
	public void setFloor(String floor) {
		this.floor = floor;
	}
	public Dept(Integer id, String deptName, String floor) {
		super();
		this.id = id;
		this.deptName = deptName;
		this.floor = floor;
	}
	public Dept() {
		super();
		// TODO Auto-generated constructor stub
	}
}

```

### 3-3 导包、导入DTD文件
### 3-4 log4j.xml [日志配置]

```
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE log4j:configuration SYSTEM "log4j.dtd">
<log4j:configuration xmlns:log4j="http://jakarta.apache.org/log4j/">
	<appender name="log.console" class="org.apache.log4j.ConsoleAppender">
		<layout class="org.apache.log4j.PatternLayout">
			<param name="ConversionPattern" value="%d{yyyy-MM-dd HH:mm:ss,SSS} %5p (%C{1}:%M) - %m%n" />
		</layout>
	</appender>
	<logger name="org.apache.ibatis">
		<level value="info" />
		<appender-ref ref="log.console" />
	</logger>
	<logger name="com.ahchentong">
		<level value="debug" />
		<appender-ref ref="log.console" />
	</logger>
</log4j:configuration>

```

### 3-5 db.properties [外部资源文件 k-v键值对]

```
jdbc.user=root
jdbc.password=******
jdbc.driverClass=com.mysql.jdbc.Driver
jdbc.jdbcUrl=jdbc:mysql://127.0.0.1:3306/mybatis

```

### 3-6 mybatis-config.xml [mybatis主配置]

```
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
  PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
  "http://mybatis.org/dtd/mybatis-3-config.dtd">

<configuration>

	<properties resource="db.properties"/>

	<!--
		参考https://koooooooohane.github.io/2018/10/10/mybatis-connection.html#mybatis-configxml-%E7%9A%84%E7%BB%93%E6%9E%84%E9%A1%BA%E5%BA%8F
		mybatis-config.xml 的结构顺序
		没有setting就不要设置，一旦设置就必须遵守顺序
	-->

	<settings>
		<!-- 懒加载 -->
		<setting name="lazyLoadingEnabled" value="true"/>
		<setting name="aggressiveLazyLoading" value="false"/>
		<!-- 懒加载 -->
	</settings>

	<typeAliases>
		<!-- 一、单独的为User进行别名设置 -->
		<!-- <typeAlias type="com.ahchentong.entity.User" alias="User"/> -->

		<!-- 二、批处理别名，扫描整个包下的类 -->
		<package name="com.ahchentong.entity"/>
	</typeAliases>

	<environments default="development">
		<environment id="development">
			<transactionManager type="JDBC" />
			<dataSource type="POOLED">
				<property name="driver" value="${jdbc.driverClass}" />
				<property name="url" value="${jdbc.jdbcUrl}?useUnicode=true&amp;characterEncoding=UTF8" />
				<property name="username" value="${jdbc.user}" />
				<property name="password" value="${jdbc.password}" />
			</dataSource>
		</environment>
	</environments>

	<mappers>
		<!-- xml方式 -->
		<!-- <mapper resource="com/ahchentong/mapper/UserMapper.xml" /> -->
		<!-- Annotations -->
		<!-- <mapper class="com.ahchentong.mapper.UserMapper"/> -->

		<!-- 注册mapper包下的所有mapper接口,此种方法要求mapper接口名称和mapper映射文件名称遵守mabatis规范，且放在同一个目录中 -->
		<package name="com.ahchentong.mapper"/>
	</mappers>

</configuration>

```

### 3-7 创建UserMapper.java [接口]
### 3-8 创建UserMapper.xml [绑定]
### 3-9 创建DeptMapper.java [接口]
### 3-10 创建DeptMapper.xml [绑定]

## 4.测试
### 4-1 一把钥匙、一把锁(单查询)
#### 4-1.1 UserMapper.java

```
package com.ahchentong.mapper;

import com.ahchentong.entity.User;

public interface UserMapper {
	//仅作为演示，不严谨，此为一对一，如一把钥匙对应一把锁。
	User getUserById(Integer id);
}

```

#### 4-1.2 UserMapper.xml

```
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE mapper SYSTEM "http://mybatis.org/dtd/mybatis-3-mapper.dtd" >
<mapper namespace="com.ahchentong.mapper.UserMapper">
	<!-- 仅作为演示，不严谨，此为一对一，如一把钥匙对应一把锁。 -->
	<select id="getUserById" parameterType="Integer" resultType="User">
		select * from usertable where id = #{id}
	</select>
</mapper>

```

#### 4-1.3 DeptMapper.java

```
package com.ahchentong.mapper;

import com.ahchentong.entity.Dept;

public interface DeptMapper {
	//仅作为演示，不严谨，此为一对一，如一把钥匙对应一把锁。
	Dept getDeptById(Integer id);
}

```

#### 4-1.4 DeptMapper.xml

```
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE mapper SYSTEM "http://mybatis.org/dtd/mybatis-3-mapper.dtd" >
<mapper namespace="com.ahchentong.mapper.DeptMapper">
	<!-- 仅作为演示，不严谨，此为一对一，如一把钥匙对应一把锁。 -->
	<select id="getDeptById" parameterType="Integer" resultType="Dept">
		select * from depttable where id = #{id}
	</select>
</mapper>

```

#### 4-1.5 One_key_one_lock.java

```
package com.ahchentong.test;

import java.io.InputStream;

import org.apache.ibatis.io.Resources;
import org.apache.ibatis.session.SqlSession;
import org.apache.ibatis.session.SqlSessionFactory;
import org.apache.ibatis.session.SqlSessionFactoryBuilder;
import org.junit.Before;
import org.junit.Test;

import com.ahchentong.entity.Dept;
import com.ahchentong.entity.User;
import com.ahchentong.mapper.DeptMapper;
import com.ahchentong.mapper.UserMapper;

public class One_key_one_lock {
	SqlSessionFactory sqlSessionFactory = null;
	@Before
	public void init(){
		String resource = "mybatis-config.xml";
		InputStream inputStream = null;
		try {
			inputStream = Resources.getResourceAsStream(resource);
		} catch (Exception e) {
			e.printStackTrace();
		}
		sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);
	}

	@Test
	public void testUser(){
		SqlSession session = sqlSessionFactory.openSession();
		UserMapper mapper = session.getMapper(UserMapper.class);
		User userById = mapper.getUserById(1);
		System.out.println(userById.toString());
	}

	@Test
	public void testDept(){
		SqlSession session = sqlSessionFactory.openSession();
		DeptMapper mapper = session.getMapper(DeptMapper.class);
		Dept deptById = mapper.getDeptById(1);
		System.out.println(deptById.toString());
	}
}

```

### 4-2 通过User的id找到自己以及部门 [1.连接查询]
#### 4-2.1 UserMapper.java

> 注意：通过User的id找到自己以及部门，需要先将Dept加入到User的参数

```
package com.ahchentong.entity;

public class User {
	private Integer id;
	private String userName;
	private Integer deptId;
	//需要先行加入Dept
	private Dept dept;
	@Override
	public String toString() {
		return "User [id=" + id + ", userName=" + userName + ", deptId=" + deptId + ", dept=" + dept + "]";
	}
	public Integer getId() {
		return id;
	}
	public void setId(Integer id) {
		this.id = id;
	}
	public String getUserName() {
		return userName;
	}
	public void setUserName(String userName) {
		this.userName = userName;
	}
	public Integer getDeptId() {
		return deptId;
	}
	public void setDeptId(Integer deptId) {
		this.deptId = deptId;
	}
	public Dept getDept() {
		return dept;
	}
	public void setDept(Dept dept) {
		this.dept = dept;
	}
	public User(Integer id, String userName, Integer deptId, Dept dept) {
		super();
		this.id = id;
		this.userName = userName;
		this.deptId = deptId;
		this.dept = dept;
	}
	public User() {
		super();
		// TODO Auto-generated constructor stub
	}
}

```

```
package com.ahchentong.mapper;

import com.ahchentong.entity.User;

public interface UserMapper {
	//通过用户找到自己和所在的职位
	User getAllByUserId(Integer id);
}

```

#### 4-2.2 UserMapper.xml [association] [与分段查询不同之处在于配置文件]

```
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE mapper SYSTEM "http://mybatis.org/dtd/mybatis-3-mapper.dtd" >
<mapper namespace="com.ahchentong.mapper.UserMapper">
	<resultMap type="User" id="reMap">
		<id property="id" column="id"/>
		<result property="userName" column="userName"/>
		<result property="deptId" column="deptId"/>

		<association property="dept" javaType="com.ahchentong.entity.Dept">
			<id property="id" column="id"/>
			<result property="deptName" column="deptName"/>
			<result property="floor" column="floor"/>
		</association>
	</resultMap>
	<select id="getAllByUserId" parameterType="Integer" resultMap="reMap">
		SELECT * FROM usertable,depttable WHERE usertable.deptId = depttable.id AND usertable.id = #{id}
	</select>
</mapper>

```

#### 4-2.3 DeptMapper.java

```
package com.ahchentong.mapper;

import com.ahchentong.entity.Dept;

public interface DeptMapper {
	Dept getDeptById(Integer id);
}

```

#### 4-2.4 DeptMapper.xml

```
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE mapper SYSTEM "http://mybatis.org/dtd/mybatis-3-mapper.dtd" >
<mapper namespace="com.ahchentong.mapper.DeptMapper">
	<select id="getDeptById" parameterType="Integer" resultType="Dept">
		select * from depttable where id = #{id}
	</select>
</mapper>

```

#### 4-2.5 DeptAndUserByUserId.java

```
package com.ahchentong.test;

import java.io.InputStream;

import org.apache.ibatis.io.Resources;
import org.apache.ibatis.session.SqlSession;
import org.apache.ibatis.session.SqlSessionFactory;
import org.apache.ibatis.session.SqlSessionFactoryBuilder;
import org.junit.Before;
import org.junit.Test;

import com.ahchentong.entity.User;
import com.ahchentong.mapper.UserMapper;

public class DeptAndUserByUserId{
	SqlSessionFactory sqlSessionFactory = null;
	@Before
	public void init(){
		String resource = "mybatis-config.xml";
		InputStream inputStream = null;
		try {
			inputStream = Resources.getResourceAsStream(resource);
		} catch (Exception e) {
			e.printStackTrace();
		}
		sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);
	}

	@Test
	public void getDeptAndUserByUserId(){
		SqlSession session = sqlSessionFactory.openSession();
		UserMapper mapper = session.getMapper(UserMapper.class);
		User allByUserId = mapper.getAllByUserId(1);
		System.out.println(allByUserId.toString());
	}
}

```

### 4-3 通过User的id找到自己以及部门 [2.分段查询]
#### 4-3.1 UserMapper.java [同 4-2]
#### 4-3.2 UserMapper.xml

```
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE mapper SYSTEM "http://mybatis.org/dtd/mybatis-3-mapper.dtd" >
<mapper namespace="com.ahchentong.mapper.UserMapper">
	<resultMap type="User" id="reMap">
		<id property="id" column="id"/>
		<result property="userName" column="userName"/>
		<result property="deptId" column="deptId"/>

		<association property="dept" column="deptId" select="com.ahchentong.mapper.DeptMapper.getDeptById" />
	</resultMap>
	<select id="getAllByUserId" parameterType="Integer" resultMap="reMap">
		SELECT * FROM usertable WHERE id = #{id}
	</select>
</mapper>

```

#### 4-3.3 DeptMapper.java [同 4-2] [getDeptById方法必须要存在]
#### 4-3.4 DeptMapper.xml [同 4-2] [getDeptById并且要绑定]
#### 4-3.5 DeptAndUserByUserId.java[同 4-2]


### 4-3
#### 4-3.1 UserMapper.java
#### 4-3.2 UserMapper.xml
#### 4-3.3 DeptMapper.java
#### 4-3.4 DeptMapper.xml
#### 4-3.5
