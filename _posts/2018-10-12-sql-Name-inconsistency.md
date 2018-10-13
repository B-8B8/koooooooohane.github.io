---
title: myBatis框架之如果数据库字段名和实体类不一致怎么解决
key: 2018-10-12
tags: mybatis mySql 框架
---
## 前言
> 1. <a href="/2018/10/10/mybatis-connection.html" target="\_blank">记录第二次使用myBatis框架一(使用annotations)</a>   
2. <a href="/2018/10/10/mybatis-connection2.html" target="\_blank">记录第二次使用myBatis框架二(使用mybatis提供的API，用作测试)</a>   
3. <a href="/2018/10/10/mybatis-connection3.html" target="\_blank">记录第二次使用myBatis框架三(xml方式，代码和Sql语句分离，推荐)</a>   
4. <a href="/2018/10/12/mybatis-mapper.html" target="\_blank">myBatis框架的typeAliases别名处理器和mapper映射器</a>   
5. <a href="/2018/10/12/mybatis-select.html" target="\_blank">myBatis框架Conditions多个关键字查询、like模糊查询、返回Map</a>   
如果在本章遇到问题，请查看以上的几个文章以查看修改之前的代码    
最终代码打包地址 (含MyBatis.mmap)>   
链接：<a href="https://pan.baidu.com/s/19IDQLd97TP493ivxVz5KyQ" target="\_blank">https://pan.baidu.com/s/19IDQLd97TP493ivxVz5KyQ</a>
提取码：ajo6



## 1.创建数据库
```
CREATE TABLE `dog` (
 `id` INT(10) NOT NULL AUTO_INCREMENT,
 `dog_Name` VARCHAR(20) DEFAULT NULL,
 `dog_Weight` INT(10) DEFAULT NULL,
 PRIMARY KEY (`id`)
);

INSERT INTO dog(dog_Name,dog_Weight) VALUES('ww1',10);
INSERT INTO dog(dog_Name,dog_Weight) VALUES('ww2',15);

SELECT * FROM dog;

```
## 2.创建实体类 `Dog.java`
```
package com.ahchentong.entity;

public class Dog {
	private Integer id;
	private String dogName;
	private Integer dogWeight;
	public Dog() {
		super();
		// TODO Auto-generated constructor stub
	}
	public Dog(Integer id, String dogName, Integer dogWeight) {
		super();
		this.id = id;
		this.dogName = dogName;
		this.dogWeight = dogWeight;
	}
	public Integer getId() {
		return id;
	}
	public void setId(Integer id) {
		this.id = id;
	}
	public String getDogName() {
		return dogName;
	}
	public void setDogName(String dogName) {
		this.dogName = dogName;
	}
	public Integer getDogWeight() {
		return dogWeight;
	}
	public void setDogWeight(Integer dogWeight) {
		this.dogWeight = dogWeight;
	}
	@Override
	public String toString() {
		return "Dog [id=" + id + ", dogName=" + dogName + ", dogWeight=" + dogWeight + "]";
	}

}

```
## 3.创建 `DogMapper.java` 接口
```
package com.ahchentong.mapper;

import java.util.List;

import com.ahchentong.entity.Dog;

public interface DogMapper {
	public List<Dog> getDogByDogName(String dogName);
}

```
## 4.创建 `DogMapper.xml`(重点)
```
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE mapper SYSTEM "http://mybatis.org/dtd/mybatis-3-mapper.dtd" >
<mapper namespace="com.ahchentong.mapper.DogMapper">
	<!-- <select id="getDogByDogName" parameterType="String" resultType="Dog">
		测试的时候，可以故意将查询的字段和实体类中的不同，看查询结果会发现结果为null
		select id,dog_Name,dog_Weight from dog where dog_Name like #{dogName}

		方法1，设置别名
		select id,dog_Name as dogName,dog_Weight as dogWeight from dog where dog_Name like #{dogName}

	</select> -->

	<!-- 方法2,结果映射 -->
	<!-- 靠resultMap来解决，类名和字段名不一致的情况 -->
	<!-- id:查询结果集的唯一标识
		 column：数据库字段
		 property：POJO类的属性字段
		 result：映射结果，表示将数据库的值对应映射给POJO的类属性上
	-->
	<resultMap type="Dog" id="dogResultMap">
		<id column="id" property="id"/>
		<result column="dog_Name" property="dogName"/>
		<result column="dog_Weight" property="dogWeight"/>
	</resultMap>
	<select id="getDogByDogName" parameterType="String" resultMap="dogResultMap">
		select id,dog_Name,dog_Weight from dog where dog_Name like #{dogName}
	</select>
</mapper>

```
## 5.测试 `TestDogMapper.java`
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

import com.ahchentong.entity.Dog;
import com.ahchentong.mapper.DogMapper;

public class TestDogMapper {
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
	public void testGetDogByDogName(){
		SqlSession session = sqlSessionFactory.openSession();
		DogMapper mapper = session.getMapper(DogMapper.class);

		List<Dog> dogByDogName = mapper.getDogByDogName("%w%");
		for (Dog dog : dogByDogName) {
			System.out.println(dog.toString());
		}
	}
}

```
