---
title: Java发送邮件
key: 2018-09-27
tags: Java 发邮件 jar包 验证码
---

## Jar包下载网站
> <a href="http://maven.ibiblio.org/" target="\_blank">maven.ibiblio.org</a>   
此项目用到的Jar包   
链接：<a href="https://pan.baidu.com/s/1AZ_TE3SkErNB8iiE9opSpw" target="\_blank">https://pan.baidu.com/s/1AZ_TE3SkErNB8iiE9opSpw</a>
提取码：y0e0

## 代码实例 Sendmail.java
```
package com.ahchentong.verification;

import java.util.Properties;

import javax.mail.Message;
import javax.mail.Session;
import javax.mail.Transport;
import javax.mail.internet.InternetAddress;
import javax.mail.internet.MimeMessage;

/**
 *
 * <h1>用途：发送邮件
 * <h1>项目：ahChentong-Java-toolkit
 * <h1>模块：com.ahchentong.verification
 * <h1>用法：
 * <p>
 * //1.创建对象：<br>
 * Sendmail sendmail = new Sendmail(hostName, sendEmail, sendEmailPwd, receiveEmail, subject, content);<br>
 * //2.获取状态：<br>
 * String state = sendmail.getState();<br>
 * </p>
 * @author 骆昊宇(骆浩) ahChentong.com
 *
 */

public class Sendmail {
	private String state;

	/**
	 *
	 * <ol>
	 * <li> hostName[参数1]：（邮箱服务器地址）smtp.exmail.qq.com,smtp.163.com...
	 * <li> sendEmail[参数2]：用于发送邮件的账户
	 * <li> sendEmailPwd[参数3]：发件账户的密码
	 * <li> receiveEmail[参数4]：收件人的账户
	 * <li> subject[参数5]：邮件的标题
	 * <li> content[参数6]：邮件的正文
	 * </ol>
	 *
	 */
	public Sendmail(String hostName, String sendEmail, String sendEmailPwd, String receiveEmail, String subject, String content) {
		Transport ts = null;
		try {
			this.state = null;
			Properties prop = new Properties();
			prop.setProperty("mail.host", hostName);
			prop.setProperty("mail.transport.protocol", "smtp");
			prop.setProperty("mail.smtp.auth", "true");
			Session session = Session.getInstance(prop);
			ts = session.getTransport();
			ts.connect(hostName, sendEmail, sendEmailPwd);
			Message message = createSimpleMail(session, sendEmail, receiveEmail, subject, content);
			ts.sendMessage(message, message.getAllRecipients());
			this.state = "Success";
		} catch (Exception e) {
			//e.printStackTrace();
		} finally {
			if(ts != null){
				try {
					ts.close();
				} catch (Exception e) {
					//e.printStackTrace();
				}
			}
		}
	}

	private Message createSimpleMail(Session session, String sendEmail, String receiveEmail, String subject,
			String content) throws Exception {
		MimeMessage message = new MimeMessage(session);
		message.setFrom(new InternetAddress(sendEmail));
		message.setRecipient(Message.RecipientType.TO, new InternetAddress(receiveEmail));
		message.setSubject(subject);
		message.setContent(content, "text/html;charset=UTF-8");
		return message;
	}

	/**
	 *
	 * String state[返回值]：<br>
	 * 发送后调用,成功返回Success,否则返回null
	 *
	 */
	public String getState() {
		return state;
	}

	public void setState(String state) {
		this.state = state;
	}
}

```
## 调用方法 TestMain.java
```
package com.ahchentong.test;

import org.junit.Test;

import com.ahchentong.verification.Sendmail;

public class TestMain {
	@Test
	public void testSendmail(){
		String subject = "【安徽辰通信息科技有限公司】请尽快验证您的账户。";
		int code = 0;
		String word = "生活不止眼前的苟且，还有诗和远方的田野。";
		do{
			for(int i = 0;i < 100;i++){
				code = (int) ((Math.random()*9+1)*100000);
			}
		}while(code == 0);

		String content = "<div style='color:#333;font-weight:bold;'>"
				+ "<span style='padding-left:20px;display:block;border-left: 10px solid #C1194E;'>"
				+ "<h1>验证码为：<span style='border-bottom:3px dotted red;'>" + code + "</span></h1>"
				+ "Ps：验证码有效时间为30秒，为了保证您的使用请尽快验证！<br />"
				+ "From：<a style='text-decoration:none;' href='http://ahchentong.com' target='_blank'>安徽辰通信息科技有限公司</a><br/>"
				+ "word：" + word + "<br/>"
				+ "</span>"
				+ "<hr style='margin:20px auto;border:0;border-bottom:1px dashed #333;' />"
				+ "<img src='http://www.ahchentong.com/views/images/brand.png' width='100%'>"
				+ "</div>";

		Sendmail sendmail = new Sendmail("smtp.exmail.qq.com1"
				, "admin@ahchentong.com"
				, "***"
				, "202***609@qq.com"
				, subject
				, content);

		String state = sendmail.getState();
		System.out.println(state);
	}
}

```
> 参考：<a href="https://www.cnblogs.com/xdp-gacl/p/4216311.html" target="\_blank">JavaWeb学习总结(五十二)——使用JavaMail创建邮件和发送邮件 - 孤傲苍狼</a>

## 拓展 验证码生成
```
package com.ahchentong.verification;

import java.util.Arrays;
/**
 *
 * <h1>用途：生成验证码
 * <h1>项目：ahChentong-Java-toolkit
 * <h1>模块：com.ahchentong.verification
 * <h1>用法：
 * <p>
 * //1.创建对象：<br>
 * GenerateCaptchas generateCaptchas = new GenerateCaptchas();<br>
 * //2.调用方法：<br>
 * String code = generateCaptchas.getCode(type,length);<br>
 * </p>
 * @author 骆昊宇(骆浩) ahChentong.com
 *
 */
public class GenerateCaptchas {
	private final String [] NUMBER = {"0","1","2","3","4","5","6","7","8","9"};
	private final String [] CAPITAL_LETTERS = {"a"
			,"b","c","d","e","f","g","h","i","j","k","l","m","n"
			,"o","p","q","r","s","t","u","v","w","x","y","z"};
	private final String [] LOWERCASE_LETTERS = {"A"
			,"B","C","D","E","F","G","H","I","J","K","L","M","N"
			,"O","P","Q","R","S","T","U","V","W","X","Y","Z"};

	/**
	 *
	 * <ol>
	 * <li> type[参数1]：1/a/A/1a/1A/aA/1aA
	 * <li> length[参数2]：0~∞,建议越短越好不要玩坏哦~
	 * <li> String[返回值] Code
	 * </ol>
	 *
	 */
	public String getCode(String type,int length) {
		String[] blend = null;
		if(type.equals(null)){
			blend = Arrays.copyOf(NUMBER, NUMBER.length);
		} else if("a".equals(type)){
			blend = Arrays.copyOf(CAPITAL_LETTERS, CAPITAL_LETTERS.length);
		} else if("A".equals(type)){
			blend = Arrays.copyOf(LOWERCASE_LETTERS, LOWERCASE_LETTERS.length);
		} else if("aA".equals(type)){
			blend = Arrays.copyOf(CAPITAL_LETTERS, CAPITAL_LETTERS.length + CAPITAL_LETTERS.length);
			System.arraycopy(LOWERCASE_LETTERS, 0, blend, CAPITAL_LETTERS.length, LOWERCASE_LETTERS.length);
		} else if("1a".equals(type)){
			blend = Arrays.copyOf(NUMBER, NUMBER.length + CAPITAL_LETTERS.length);
			System.arraycopy(CAPITAL_LETTERS, 0, blend, NUMBER.length, CAPITAL_LETTERS.length);
		} else if("1A".equals(type)){
			blend = Arrays.copyOf(NUMBER, NUMBER.length + LOWERCASE_LETTERS.length);
			System.arraycopy(LOWERCASE_LETTERS, 0, blend, NUMBER.length, LOWERCASE_LETTERS.length);
		} else if("1aA".equals(type)){
			String[] temp = Arrays.copyOf(NUMBER, NUMBER.length + CAPITAL_LETTERS.length);
			System.arraycopy(CAPITAL_LETTERS, 0, temp, NUMBER.length, CAPITAL_LETTERS.length);

			blend = Arrays.copyOf(temp, temp.length + LOWERCASE_LETTERS.length);
			System.arraycopy(LOWERCASE_LETTERS, 0, blend, temp.length, LOWERCASE_LETTERS.length);
		} else {
			blend = Arrays.copyOf(NUMBER, NUMBER.length);
		}
		String code = "";
		if(length <= 0){
			code = "0";
		}else{
			for(int i=0;i<length;i++){
				code += blend[(int) ((Math.random()*blend.length)*1)];
			}
		}
		return code;
	}
}

```
## 调用示例
```
String code = null;

GenerateCaptchas generateCaptchas = new GenerateCaptchas();
code = generateCaptchas.getCode("1", 14);
System.out.println(code);

code = generateCaptchas.getCode("a", 14);
System.out.println(code);

code = generateCaptchas.getCode("A", 14);
System.out.println(code);

code = generateCaptchas.getCode("1a", 14);
System.out.println(code);

code = generateCaptchas.getCode("1A", 14);
System.out.println(code);

code = generateCaptchas.getCode("aA", 14);
System.out.println(code);

code = generateCaptchas.getCode("1aA", 14);
System.out.println(code);
/*for(int i=0;i<30;i++){
	String code = generateCaptchas.getCode("1", 6);
	System.out.println(code);
}*/

```
