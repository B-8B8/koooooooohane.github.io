---
title: 记录第二次使用myBatis框架二(使用mybatis提供的API，用作测试)
key: 20181010
tags: mybatis mySql 框架
---

> 使用mybatis提供的API，这样是用来测试的，一般不使用这种方式。

## 第一步：导包
## 第二步：设置 `log4j.xml`
## 第三步：新建数据库
## 第四步：创建实体类 `Cat.java`
> 以上四步在 <a href="/2018/10/10/mybatis-connection.html" target="\_blank">记录第二次使用myBatis框架(使用annotations)</a> 中有记录，需要查看请移步，在此不再赘述。   
ps. 顺序可能不一致，请留意。

## 第五步：创建 `CatMapper.java` 接口
```
package com.ahchentong.mapper;

import java.util.List;

import org.apache.ibatis.annotations.Delete;
import org.apache.ibatis.annotations.Insert;
import org.apache.ibatis.annotations.Select;
import org.apache.ibatis.annotations.Update;

import com.ahchentong.entity.Cat;

public interface CatMapper {
	public int add(Cat cat);
	public int del(String id);
	public void update(Cat cat);
	public Cat search(String id);
	public List<Cat> searchList();
}

```
## 第六步：配置 `CatMapper.xml`
> 这里是需要注意的，使用mybatis框架提供的API方法测试时也需要创建 `CatMapper.xml` 并且在 `mybatis-config.xml` 中绑定。

> 注意xml语法中的标签
* 增 `insert`
* 删 `delete`
* 改 `update`
* 查 `select`
  * 查多个也是使用 `select` 返回值只用设置 `com.ahchentong.entity.Cat` 无需设置 `List` ，在接收的时候变量类型设置 `List<Cat>` 即可
* `parameterType` 形参，传递的值
* `resultType` 返回值

```
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE mapper SYSTEM "http://mybatis.org/dtd/mybatis-3-mapper.dtd" >
<mapper namespace="com.ahchentong.mapper.CatMapper">
  <insert id="add" parameterType="com.ahchentong.entity.Cat">
  	insert into cat(id,name,sex)values(#{id},#{name},#{sex})
  </insert>
  <delete id="del" parameterType="String">
  	delete from cat where id=#{id}
  </delete>
  <update id="update" parameterType="com.ahchentong.entity.Cat">
  	update cat set name=#{name},sex=#{sex} where id=#{id}
  </update>
  <select id="search" parameterType="String" resultType="com.ahchentong.entity.Cat">
  	select id,name,sex from cat where id=#{id}
  </select>
  <select id="searchList" resultType="com.ahchentong.entity.Cat">
  	select id,name,sex from cat
  </select>
</mapper>

```
## 第七步：配置 `mybatis-config.xml`
> 这里需要在 `mappers` 标签中插入 `CatMapper.xml` 的路径，例如 `<mapper resource="com/ahchentong/mapper/CatMapper.xml" />`

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
				<property name="driver" value="数据库驱动" />
				<property name="url" value="$数据库地址?useUnicode=true&amp;characterEncoding=UTF8" />
				<property name="username" value="用户名" />
				<property name="password" value="密码" />
			</dataSource>
		</environment>
	</environments>

	<mappers>
		<mapper resource="com/ahchentong/mapper/CatMapper.xml" />
	</mappers>

</configuration>

```
## 第八步：测试 `TestCatMapperAPI.java`
```
package com.ahchentong.test;

import java.io.IOException;
import java.io.InputStream;
import java.util.List;

import org.apache.ibatis.io.Resources;
import org.apache.ibatis.session.SqlSession;
import org.apache.ibatis.session.SqlSessionFactory;
import org.apache.ibatis.session.SqlSessionFactoryBuilder;
import org.junit.Before;
import org.junit.Test;

import com.ahchentong.entity.Cat;

public class TestCatMapperAPI {
	/**
	 * 这只是一种测试方法
	 * 使用了myBatis框架提供的接口
	 *
	 * 这样测试的步骤是
	 *
	 * 1.初始化
	 * 2.开流
	 * 3.开session
	 * 4.session.方法(selectOne,selectList...) return
	 * 5.session.commit();
	 * 6.session.close();
	 */
	SqlSessionFactory sqlSessionFactory = null;
	@Before
	public void init(){
		String resource = "mybatis-config.xml";
		InputStream inputStream = null;
		try {
			inputStream = Resources.getResourceAsStream(resource);
		} catch (IOException e) {
			e.printStackTrace();
		}
		sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);
	}

	@Test
	public void testAdd(){
		SqlSession session = sqlSessionFactory.openSession();
		//参数根据需求来定，当前的第一个参数是被测试的接口的全类名.方法名
		//参数二是传实参，视方法而定
		session.insert("com.ahchentong.mapper.CatMapper.add", new Cat(null, "淼淼", "母"));
		//这个session.commit();要加上不然可能不会影响到数据库
		session.commit();
		session.close();
	}

	@Test
	public void testDel(){
		SqlSession session = sqlSessionFactory.openSession();
		session.delete("com.ahchentong.mapper.CatMapper.del", "111");
		session.commit();
		session.close();
	}

	@Test
	public void testUpdate(){
		SqlSession session = sqlSessionFactory.openSession();
		session.update("com.ahchentong.mapper.CatMapper.update", new Cat(112, "喵喵", "公"));
		session.commit();
		session.close();
	}

	@Test
	public void testSearch(){
		SqlSession session = sqlSessionFactory.openSession();
		Cat search = session.selectOne("com.ahchentong.mapper.CatMapper.search", "13");
		System.out.println(search.toString());
		session.commit();
		session.close();
	}

	@Test
	public void testSearchList(){
		SqlSession session = sqlSessionFactory.openSession();
		List<Cat> search = session.selectList("com.ahchentong.mapper.CatMapper.searchList");
		for (Cat cat : search) {
			System.out.println(cat);
		}
		session.commit();
		session.close();
	}
}

```
