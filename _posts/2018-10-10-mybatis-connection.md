---
title: 记录第二次使用myBatis框架一(使用annotations)
key: 20181010
tags: mybatis mySql 框架
---
## 导包 将jar包导入lib
> 地址 链接: <a href="https://pan.baidu.com/s/1DHyF5wDUE8npMK1VgnSN-A" target="\_blank">https://pan.baidu.com/s/1DHyF5wDUE8npMK1VgnSN-A</a> 提取码: xygu

## 设置 `log4j.xml`
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

## 新建数据库
```
CREATE DATABASE catDataBase CHARACTER SET utf8 COLLATE utf8_general_ci;
USE catDataBase;
CREATE TABLE cat(id INT(5) PRIMARY KEY NOT NULL AUTO_INCREMENT ,`name` VARCHAR(50) NOT NULL,sex VARCHAR(50) NOT NULL)ENGINE=INNODB  DEFAULT CHARSET=utf8 AUTO_INCREMENT=1;
INSERT INTO cat (`name`,sex) VALUES ('Tom','公');
INSERT INTO cat (`name`,sex) VALUES ('Sana','母');
INSERT INTO cat (`name`,sex) VALUES ('Goqi','母');

```

> ##### mybatis-config.xml 的结构顺序
* configuration
* properties
* settings
* typeAliases
* typeHandlers
* objectFactory
* plugins
* environments
    * environment
      * transactionManager
      * dataSource
* databaseIdProvider
* mappers

## 配置 `mybatis-config.xml`

> DTD头文件地址同上面的包   
引入方式(Eclipse)   
1. windows选项卡
2. Preferences选项
3. 搜索xml
4. 选择 XML Catalog 选项
5. 点击 Add 按钮
  * 点击右边 File System 按钮
  * 找到你需要添加的DTD文件并点击 打开 按钮
    * 例如：C:\Users\koooo\Desktop\mybatis相关\mybatis-3-mapper.dtd
  * key type 选择框选择 URI
  * 在 key 编辑框内输入 http://mybatis.org/dtd/mybatis-3-mapper.dtd
6. 点击 OK 按钮
  * 再次点击 OK 按钮退出

> new xml file的时候
* Next
* 输入 mybatis-config.xml (官方建议名称)
* 不要点击完成 继续Next
* 选择 Create XML file from a DTD file (第一个)
* Next
* 选择 select XML Catalog entry (第二个)
* 在Catalog列表框选择我们刚刚设置的那个
* Next
* Finish

##### 第一种要先添加外部数据库资源文件 `db.properties`
> src或同级目录创建键值对文件db.properties

```
jdbc.user=***
jdbc.password=***
jdbc.driverClass=com.mysql.jdbc.Driver
jdbc.jdbcUrl=jdbc:mysql://127.0.0.1:3306/catDataBase?useUnicode=true&amp;characterEncoding=UTF8

```

```
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
  PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
  "http://mybatis.org/dtd/mybatis-3-config.dtd">

<configuration>
    <!--引入外部资源-->
	<properties resource="db.properties"/>

	<environments default="development">
		<environment id="development">
			<transactionManager type="JDBC" />
			<dataSource type="POOLED">
				<property name="driver" value="${jdbc.driverClass}" />
				<property name="url" value="${jdbc.jdbcUrl}" />
				<property name="username" value="${jdbc.user}" />
				<property name="password" value="${jdbc.password}" />
			</dataSource>
		</environment>
	</environments>

	<mappers>
		<mapper class="com.ahchentong.mapper.CatMapper" />
	</mappers>

</configuration>

```

> 或

```
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
  PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
  "http://mybatis.org/dtd/mybatis-3-config.dtd">

<configuration>

	<environments default="development">
		<environment id="development">
			<transactionManager type="JDBC" />
			<dataSource type="POOLED">
				<property name="driver" value="com.mysql.jdbc.Driver" />
				<property name="url"
					value="jdbc:mysql://localhost:3306/catDataBase?useUnicode=true&amp;characterEncoding=UTF8" />
				<property name="username" value="***" />
				<property name="password" value="***" />
			</dataSource>
		</environment>
	</environments>

	<mappers>
		<!-- <mapper resource="com/ahchentong/mapper/CatMapper.xml" /> -->
		<mapper class="com.ahchentong.mapper.CatMapper" />
	</mappers>

</configuration>

```

## 创建实体类 `Cat.java`
```
package com.ahchentong.entity;

public class Cat {
	private Integer id;
	private String name;
	private String sex;
	public Cat() {
		super();
		// TODO Auto-generated constructor stub
	}
	public Cat(Integer id, String name, String sex) {
		super();
		this.id = id;
		this.name = name;
		this.sex = sex;
	}
	public Integer getId() {
		return id;
	}
	public void setId(Integer id) {
		this.id = id;
	}
	public String getName() {
		return name;
	}
	public void setName(String name) {
		this.name = name;
	}
	public String getSex() {
		return sex;
	}
	public void setSex(String sex) {
		this.sex = sex;
	}
	@Override
	public String toString() {
		return "Cat [id=" + id + ", name=" + name + ", sex=" + sex + "]";
	}
}

```

## 创建Mapper `CatMapper.java` 接口
```
package com.ahchentong.mapper;

import java.util.List;

import org.apache.ibatis.annotations.Delete;
import org.apache.ibatis.annotations.Insert;
import org.apache.ibatis.annotations.Select;
import org.apache.ibatis.annotations.Update;

import com.ahchentong.entity.Cat;
  /**
  * 需要使用注释的方式需要将mybatis-config.xml内的
  * <mapper resource="xx/xx/mapper/OtherMapper.xml" />
  * 改为
  * <mapper class="本类全类名" />
  * 注意：此种方式无需写配置文件
  */
public interface CatMapper {
	@Insert("insert into cat(id,name,sex)values(#{id},#{name},#{sex})")
	public int add(Cat cat);
	@Delete("delete from cat where id=#{id}")
	public int del(String id);
	@Update("update cat set name=#{name},sex=#{sex} where id=#{id}")
	public void update(Cat cat);
	@Select("select id,name,sex from cat where id=#{id}")
	public Cat search(String id);
	@Select("select id,name,sex from cat")
	public List<Cat> searchList();
}

```

## 开始测试 创建 `TestCatMapper.java`
```
package com.ahchentong.test;

import java.io.InputStream;
import java.util.List;

import org.apache.ibatis.io.Resources;
import org.apache.ibatis.session.SqlSession;
import org.apache.ibatis.session.SqlSessionFactory;
import org.apache.ibatis.session.SqlSessionFactoryBuilder;
import org.junit.Before;
import org.junit.Test;

import com.ahchentong.entity.Cat;
import com.ahchentong.mapper.CatMapper;

public class TestCatMapper {

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
	public void testAdd(){
		SqlSession session = sqlSessionFactory.openSession();
		CatMapper mapper = session.getMapper(CatMapper.class);

		Cat cat = new Cat(null, "毛毛", "母");
		mapper.add(cat);

		session.commit();
		session.close();
	}

	@Test
	public void testDel(){
		SqlSession session = sqlSessionFactory.openSession();
		CatMapper mapper = session.getMapper(CatMapper.class);

		mapper.del("11");

		session.commit();
		session.close();
	}

	@Test
	public void testUpdate(){
		SqlSession session = sqlSessionFactory.openSession();
		CatMapper mapper = session.getMapper(CatMapper.class);

		Cat cat = new Cat(12, "哈贰", "公");
		mapper.update(cat);

		session.commit();
		session.close();
	}

	@Test
	public void testSearch(){
		SqlSession session = sqlSessionFactory.openSession();
		CatMapper mapper = session.getMapper(CatMapper.class);

		Cat search = mapper.search("12");
		System.out.println(search.toString());

		session.commit();
		session.close();
	}

	@Test
	public void testSearchList(){
		SqlSession session = sqlSessionFactory.openSession();
		CatMapper mapper = session.getMapper(CatMapper.class);

		List<Cat> searchList = mapper.searchList();
		for (Cat cat : searchList) {
			System.out.println(cat.toString());
		}

		session.commit();
		session.close();
	}
}

```
