# 使用帮助

> 两行代码即可发送邮件

![starts](https://img.shields.io/github/stars/hongshuboy/springmail-simple-mail.svg?style=social)
![size](https://img.shields.io/github/repo-size/hongshuboy/springmail-simple-mail.svg)
![version](https://img.shields.io/github/release/hongshuboy/springmail-simple-mail.svg)
![releasedate](https://img.shields.io/github/release-date/hongshuboy/springmail-simple-mail.svg)

#### 特点优势

> - 极简邮件发送：两行代码发送邮件
> - 高效：保证使用简单的同时，重视效率，在你不需要关注源码的情况下，自动选择最好的方式
> - Spring：基于Spring-Mail，完全支持Spring，又可以脱离Spring使用
> - 高送达率：默认双发送器，失败重发，尽可能保证送达到目的地

# 版本更新

| 版本号 | 关键词   | 更新时间      | 主要更新内容                                                 |
| ------ | -------- | :------------ | ------------------------------------------------------------ |
| 1.0.0  | RELEASE  | 2019年2月22日 | RELEASE版发布，功能测试稳定                                  |
| 1.0.1  | 单例模式 | 2019年3月16日 | 目录`1.1`，默认使用`单例模式`，在使用方式不变的情况下，提高了响应速度 |
| 1.0.2  | 抄送     | 2019年5月18日 | 抄送自动切换，使用方式不变                                   |

## 1.快速开始（5分钟上手使用）

> - 在这里，你可以快速完成邮件的发送，只需要一点点必须的设置
> - 熟悉项目结构：这是一个maven工程，如果你不会使用maven，可以使用`1.Jar`包的方式

## 一个示例：

只需要这四行代码，即可完整地发送一封邮件。

```java
@Test
public void testSendSimple() throws MailAddressException {
    MimeMail mimeMail = MimeMail.Builder.initMailSender("smtp.163.com", "smtp",465, "hongshuboy@163.com","你的客户端授权码", false);
    List<String> to = new ArrayList<String>();// 收件人集合
    to.add("hongshuboy@qq.com");//在这写接收者邮箱地址
    mimeMail.sendMail(to, "你有新的消息", "请到网站内查看"+new Date());
    System.out.println("发送成功,接收者列表:"+to);
}
```

### 1.1 必需的设置，开启邮箱的POP3/SMTP/IMAP

通过客户端（代码）发送邮件，必须到邮箱中打开这项设置，并且获取到客户端授权码（理解为密码），因为账号的密码是不能用的，需要用它代替密码。

详细设置过程不难，请自行搜索，搜索关键词如：`163邮箱如何开启POP3/SMTP/IMAP服务`。

### 1.2 下载依赖Jar包

- 点此[下载`Jar`包](https://github.com/hongshuboy/springmail-simple-mail/releases)或者将本项目使用Maven打包
- **注意：** 如果你不使用Spring容器，那么你需要 **额外** 将/[dependencies](https://github.com/hongshuboy/springmail-simple-mail/tree/master/dependencies)下的所有Jar包添加到项目中

### 1.3 Add to Build Path

复制刚刚下载好的几个jar包到你的项目（lib目录）下，右键选择build path ->Add to Build Path

### 1.4 开始发送

> - 这是**最简单**的方式
> - 这样只需要在需要的时候用初始化的MimeMail send方法发送邮件即可
> - **注意：**这种方式只能添加一种发送器。相比方法2，这种的稳定性稍差。
> - `1.0.1`版本更新后，此方式默认使用单例模式加载，在使用方式不变的情况下，提高了响应速度

```java
	/**
	 * 	下面是一个简单的例子，测试发送。这是最简单的方式
	 * @throws IOException
	 * @throws MailAddressException 自定义异常，邮件地址不正确
	 */
	@Test
	public void testSendSimple() throws MailAddressException {
		MimeMail mimeMail = MimeMail.Builder.initMailSender("smtp.163.com", "smtp",465, "youremailname@163.com","你的客户端授权码", false);
		List<String> to = new ArrayList<String>();// 收件人集合
		to.add("hongshuboy@qq.com");			//发给谁写在这，可以群发
		mimeMail.sendMail(to, "你有新的消息", "请到网站内查看"+new Date());
	}
```

### 1.5 补充

之后，如果你在系统中再次需要获取MimeMail对象，只需要再次调用`MimeMail.Builder.initMailSender`方法，这样会使用单例模式直接拿到第一次初始化的对象，也就是说**从第二次使用开始，都会直接获取第一次的MimeMail对象，而不会重新创建。之后的调用，参数填错也不要紧。**

​	**第二次调用示例：**

```java
		MimeMail mail2 = MimeMail.Builder.initMailSender("hello", "world", 100, "a", "b",true);//这里参数可以随便填了
		System.out.println(mail == mail2);//这里会输出true,因为直接拿了上一次的对象
```

​	**如果163邮箱报554 DT:SPM异常，意思是识别到你发的是垃圾邮件，其他异常请参考下面的文档**

​	[163邮箱退信的常见问题](http://help.163.com/09/1224/17/5RAJ4LMH00753VB8.html)

# 2. 高效初始化方式

### 2.1 设置发件箱的域名和密码

> 如果你不想使用配置文件，只想使用编码方式快速开始，可以略过这一部分，确保`开启你的邮箱的POP3/SMTP/IMAP`之后，直接看下面的`1.1不使用properties（最简单的方式）`或`1.2使用properties`

**src\main\resources\mail.properties** 

​	1. 参照现有的配置，修改这个文件，填入你的发件箱，建议你163和QQ邮箱都配置上，这样，如果发送失败，程序会自动切换发送器重发，保证成功率，而且切换过程用户没有察觉。

​	2. 如果你只配置了一个发送器，无需修改代码，系统会只用这一个发送器进行发送（但是需要删除一个spring配置，注意下文`A`部分）。

**开启邮箱的POP3/SMTP/IMAP**

​	要使用java mail，请先在邮箱设置中开启POP3/SMTP/IMAP，***配置的密码不是你的登录密码***，以163邮箱为例，同样在设置中选择客户端授权密码，获取一份授权密码，放在配置文件 *（src\main\resources\mail.properties）* 的`mail.password`位置。

***A:如果你只在其中配置了一个邮箱***

​	只想用一个发送器？那请把 `src\main\resources\spring-mailx.xml`文件中不用的发送器删除，注意删除整个`<bean>`标签

> - **强烈建议你两个都配置**，这样系统在其中一个发送失败时会切换发送器重新发送，[点击查看163发送失败的代号对应的原因](http://help.163.com/09/1224/17/5RAJ4LMH00753VB8.html)

## 2.2 简单的配置之后终于可以开始测试了

> 可以使用两种方式快速上手使用

#### 使用Spring容器初始化

> 这种方式也简单易用，速度上也比上种有显著提升

1. 使用git clone项目到本地

2. 修改`src\main\resources`下的mail.properties（配置等号上面的内容）（如果你只需要一个发送器，可以只配置一个，并且必须执行`第三步`）

3. （可选）修改`src\main\resources`下的spring-mailx.xml（如果你在`2`中只配置了一个发送器，这里需要删除多余的那个）

4. （可选）如果你需要发送附件或者在邮件中显示图片，请将附件或者图片放在`src\main\resources`下，并且修改`top\weweb\hawk\mailx\MimeMail.sendMail(List<String> to,String subject,String Text,boolean retry)`对应部分的源码（注释的很清楚）

5. **[**直接导入项目，使用Maven关联这个项目为依赖**]**    或者   **[**使用Maven重新打包项目（**请跳过Maven Test，或者删除`src\test`下的`java`文件，否则报错**）并将Jar导入**]**

6. 在工程的`spring.xml`中`import`中导入`simple mail`内的`spring-mailx.xml`配置文件，这样当前项目就可以使用`MimeMail`发送邮件了，在需要用的地方使用`@Autowired`注入就可以使用了。请看下面的演示。

**步骤6中在上层项目的spring.xml中导Mail组件的Spring容器的方法**

> 请直接复制<import>结点，除非必要不要更改

```xml
	<!-- 导入邮件组件的spring -->
	<import resource="classpath*:spring-mailx.xml"/>
	<!-- 继续你的配置 -->
  	<bean id="xxx" class="xxx.xxxx">
		...
	</bean>
```

> **易错注意 :** 
>
> - 如果使用上层容器，两个容器内不要同时有`<context:property-placeholder location="xx"/>`，将这个结点放到顶层`spring.xml`中

### 如果你觉得对你有帮助，就点个star吧~

> 有问题或者建议欢迎在此页留言或者向我发送邮件，一起改进

![starts](https://img.shields.io/github/stars/hongshuboy/springmail-simple-mail.svg?style=social)

### 问题反馈

[hongshu@weweb.top](mailto:hongshu@weweb.top)

### 捐赠

比特币：1MGLaexfJYyzz3CqSrssSX1fNhy8Mc7xwW

以太坊：0xf5C299A307e7fd1C2106faf52d3AF918c2835A58