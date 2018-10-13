---
title: myBatis框架Conditions多个关键字查询、like模糊查询、返回Map
key: 2018-10-12
tags: mybatis mySql 框架
---

## 创建 `CatMapper2.java` 接口
```
package com.ahchentong.mapper;

import java.util.List;
import java.util.Map;

import com.ahchentong.entity.Cat;

public interface CatMapper2 {
	public Map<String,Object> getCatByCatNameMap(String name);

	public List<Cat> getCatByCatName(String name);

	public Cat getCatConditions(Cat cat);

}

```
## 创建 `CatMapper2.xml`
```
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE mapper SYSTEM "http://mybatis.org/dtd/mybatis-3-mapper.dtd" >
<mapper namespace="com.ahchentong.mapper.CatMapper2">
	<select id="getCatConditions" parameterType="Cat" resultType="Cat">
		select * from cat where id=#{id} and sex=#{sex}
	</select>

	<select id="getCatByCatName" parameterType="Cat" resultType="Cat">
		<!-- 2.这里只能是value，不管你传来的是什么 -->
		select * from cat where name like '%${value}%'
		<!-- 1.select * from cat where name like #{name} -->
	</select>

	<!-- 这里只是说一下是可以返回HashMap -->
	<select id="getCatByCatNameMap" parameterType="Cat" resultType="hashmap">
		select * from cat where name=#{name}
	</select>
</mapper>

```
## 开始测试 `TestCatMapper2.java`
```
package com.ahchentong.test;

import java.io.InputStream;
import java.util.List;
import java.util.Map;

import org.apache.ibatis.io.Resources;
import org.apache.ibatis.session.SqlSession;
import org.apache.ibatis.session.SqlSessionFactory;
import org.apache.ibatis.session.SqlSessionFactoryBuilder;
import org.junit.Before;
import org.junit.Test;

import com.ahchentong.entity.Cat;
import com.ahchentong.mapper.CatMapper2;

public class TestCatMapper2 {

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

	/**
	 * 根据多个条件查询
	 * 在sql语句中给出的是两个条件的查询，如果在测试类中只给一个条件，是不能查到数据的
	 * 打印出来的是null值
	 */
	@Test
	public void testGetCatConditions(){
		SqlSession session = sqlSessionFactory.openSession();
		CatMapper2 mapper = session.getMapper(CatMapper2.class);

		Cat catConditions = mapper.getCatConditions(new Cat(113, "喵喵", "母"));
		System.out.println(catConditions);
	}

	/**
	 * 模糊查询
	 */
	@Test
	public void testGetCatByCatName(){
		SqlSession session = sqlSessionFactory.openSession();
		CatMapper2 mapper = session.getMapper(CatMapper2.class);

		/*1. List<Cat> catByCatName = mapper.getCatByCatName("%喵%");*/
		/*2. 推荐使用1*/
		List<Cat> catByCatName = mapper.getCatByCatName("喵");
		for (Cat cat : catByCatName) {
			System.out.println(cat.toString());
		}
	}

	/**
	 * 返回Map
	 */
	@Test
	public void testGetCatByCatNameMap(){
		SqlSession session = sqlSessionFactory.openSession();
		CatMapper2 mapper = session.getMapper(CatMapper2.class);

		Map<String, Object> catByCatNameMap = mapper.getCatByCatNameMap("喵喵");
		System.out.println(catByCatNameMap.toString());
	}
}

```
