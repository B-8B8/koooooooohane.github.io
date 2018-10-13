---
title: myBatis框架的typeAliases别名处理器和mapper映射器
key: 2018-10-12
tags: mybatis mySql 框架
---

## 设置 `mybatis-config.xml`
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
	<!-- <settings></settings> -->

	<typeAliases>
		<!-- 一、单独的为Cat进行别名设置 -->
		<!-- <typeAlias type="com.ahchentong.entity.Cat" alias="Cat"/> -->

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
		<!-- <mapper resource="com/ahchentong/mapper/CatMapper.xml" /> -->
		<!-- Annotations -->
		<!-- <mapper class="com.ahchentong.mapper.CatMapper"/> -->

		<!-- 注册mapper包下的所有mapper接口,此种方法要求mapper接口名称和mapper映射文件名称遵守mabatis规范，且放在同一个目录中 -->
		<package name="com.ahchentong.mapper"/>
	</mappers>

</configuration>

```

## 创建 `TestCatMapper.java`
```
package com.ahchentong.test;

import java.io.InputStream;
import java.util.Date;
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
	/**
	 * 这种测试方式是跟Annotation基本相同的
	 * 只是类名不一样以及它的内容有点不同
	 * 使用Annotation注解时无需配置文件
	 * 而在这里需要将配置文件和类同时使用
	 */
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
		SqlSession session = null;
		try {
			session = sqlSessionFactory.openSession();
			CatMapper mapper = session.getMapper(CatMapper.class);
			//----- 以上代码均与Annotation差不多
			mapper.add(new Cat(null, "小喵", "公"));
			//----- 以下代码均与Annotation差不多
			session.commit();
		} catch (Exception e) {
			e.printStackTrace();
		} finally {
			if(session!=null){session.close();}			
		}
	}

	@Test
	public void testDel(){
		SqlSession session = null;
		try {
			session = sqlSessionFactory.openSession();
			CatMapper mapper = session.getMapper(CatMapper.class);
			//----- 以上代码均与Annotation差不多
			mapper.del("112");
			//----- 以下代码均与Annotation差不多
			session.commit();
		} catch (Exception e) {
			e.printStackTrace();
		} finally {
			if(session!=null){session.close();}			
		}
	}

	@Test
	public void testUpdate(){
		SqlSession session = null;
		try {
			session = sqlSessionFactory.openSession();
			CatMapper mapper = session.getMapper(CatMapper.class);
			//----- 以上代码均与Annotation差不多
			mapper.update(new Cat(113, "喵喵", "母"));
			//----- 以下代码均与Annotation差不多
			session.commit();
		} catch (Exception e) {
			e.printStackTrace();
		} finally {
			if(session!=null){session.close();}			
		}
	}

	@Test
	public void testSearch(){
		SqlSession session = null;
		try {
			session = sqlSessionFactory.openSession();
			CatMapper mapper = session.getMapper(CatMapper.class);
			//----- 以上代码均与Annotation差不多
			Cat search = mapper.search("113");
			System.out.println(search.toString());
			//----- 以下代码均与Annotation差不多
			session.commit();
		} catch (Exception e) {
			e.printStackTrace();
		} finally {
			if(session!=null){session.close();}			
		}
	}

	@Test
	public void testSearchList(){
		SqlSession session = null;
		try {
			session = sqlSessionFactory.openSession();
			CatMapper mapper = session.getMapper(CatMapper.class);
			//----- 以上代码均与Annotation差不多
			List<Cat> searchList = mapper.searchList();
			for (Cat cat : searchList) {
				System.out.println(cat.toString());
			}
			//----- 以下代码均与Annotation差不多
			session.commit();
		} catch (Exception e) {
			e.printStackTrace();
		} finally {
			if(session!=null){session.close();}			
		}
	}
}

```
