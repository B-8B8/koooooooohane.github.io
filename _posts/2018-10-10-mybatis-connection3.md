---
title: 记录第二次使用myBatis框架三(xml方式，代码和Sql语句分离，推荐)
key: 20181010
tags: mybatis mySql 框架
---
## 第一步：导包
## 第二步：设置 `log4j.xml`
## 第三步：新建数据库
## 第四步：创建实体类 `Cat.java`
> 以上四步在 <a href="/2018/10/10/mybatis-connection.html" target="\_blank">记录第二次使用myBatis框架(使用annotations)</a> 中有记录，需要查看请移步，在此不再赘述。   
ps. 顺序可能不一致，请留意。

## 第五步：创建 `CatMapper.java` 接口
## 第六步：配置 `CatMapper.xml`
## 第七步：配置 `mybatis-config.xml`
> 以上三步在 <a href="/2018/10/10/mybatis-connection2.html" target="\_blank">记录第二次使用myBatis框架二(使用mybatis提供的API，用作测试)</a> 中有记录，需要查看请移步，在此不再赘述。  

## 第八步：测试 `TestCatMapper.java`
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
