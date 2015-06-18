# Spring ApplicationContext 容器 

*Application Context* 是 spring 中较高级的容器。和 BeanFactory 类似，它可以加载配置文件中定义的 bean，将所有的 bean 集中在一起，当有请求的时候分配 bean。 另外，它增加了企业所需要的功能，比如，从属性文件从解析文本信息和将事件传递给所指定的监听器。这个容器在 *org.springframework.context.ApplicationContext interface* 接口中定义。

*ApplicationContext* 包含 *BeanFactory* 所有的功能，一般情况下，相对于 *BeanFactory*，*ApplicationContext* 会被推荐使用。*BeanFactory* 仍然可以在轻量级应用中使用，比如移动设备或者基于 applet 的应用程序。

最常被使用的 ApplicationContext 接口实现：

- **FileSystemXmlApplicationContext**：该容器从 XML 文件中加载已被定义的 bean。在这里，你需要提供给构造器 XML 文件的完整路径

- **ClassPathXmlApplicationContext**：该容器从 XML 文件中加载已被定义的 bean。在这里，你不需要提供 XML 文件的完整路径，只需正确配置 CLASSPATH 环境变量即可，因为，容器会从 CLASSPATH 中搜索 bean 配置文件。

- **WebXmlApplicationContext**：该容器会在一个 web 应用程序的范围内加载在 XML 文件中已被定义的 bean。

我们已经在 *Spring Hello World Example*章节中看到过 ClassPathXmlApplicationContext 容器，并且，在基于 spring 的 web 应用程序这个独立的章节中，我们讨论了很多关于 XmlWebApplicationContext。所以，接下来，让我们看一个关于 FileSystemXmlApplicationContext 的例子。

## 例子: 

假设我们已经安装 Eclipse IDE，按照下面的步骤，我们可以创建一个 Spring 应用程序。

<table class="table table-bordered">
<tr><th class="fivepct">步骤</th><th>描述</th></tr>
<tr><td>1</td><td>创建一个名为 <i>SpringExample</i> 的工程， 在 <b>src</b> 下新建一个名为 <i>com.tutorialspoint</i> 的文件夹<b>src</b></td></tr>
<tr><td>2</td><td> 点击右键，选择 <i>Add External JARs</i> 选项，导入 Spring 的库文件，正如我们在 <i>Spring Hello World Example</i> 章节中提到的导入方式。</td></tr>
<tr><td>3</td><td>在 <i>com.tutorialspoint</i> 文件夹下创建 <i>HelloWorld.java</i> 和 <i>MainApp.java</i> 两个类文件。</td></tr>
<tr><td>4</td><td>文件夹下创建 Bean 的配置文件 <i>Beans.xml</i>。</td></tr>
<tr><td>5</td><td>最后的步骤是编辑所有 JAVA 文件的内容和 Bean 的配置文件,按照以前我们讲的那样去运行应用程序。</td></tr>
</table>

下面是文件 **HelloWorld.java** 的内容：

```
package com.tutorialspoint;
public class HelloWorld {
   private String message;
   public void setMessage(String message){
      this.message  = message;
   }
   public void getMessage(){
      System.out.println("Your Message : " + message);
   }
}
```
  
下面是文件 **MainApp.java** 的内容：

<pre>
package com.tutorialspoint;
import org.springframework.context.ApplicationContext;
import org.springframework.context.support.FileSystemXmlApplicationContext;
public class MainApp {
   public static void main(String[] args) {
      ApplicationContext context = new FileSystemXmlApplicationContext
            ("C:/Users/ZARA/workspace/HelloSpring/src/Beans.xml");
      HelloWorld obj = (HelloWorld) context.getBean("helloWorld");
      obj.getMessage();
   }
}
</pre>

在主程序当中，我们需要注意以下两点：

- 第一步生成工厂对象。加载完指定路径下 bean 配置文件后，利用框架提供的 **FileSystemXmlApplicationContext** API 去生成工厂 bean。**FileSystemXmlApplicationContext** 负责生成和初始化所有的对象，比如，所有在 XML bean 配置文件中的 bean。

- 第二步利用第一步生成的上下文中的 **getBean()** 方法得到所需要的 bean。 这个方法通过配置文件中的 bean ID 来返回一个真正的对象。一旦得到这个对象，就可以利用这个对象来调用任何方法。

下面是配置文件 **Beans.xml** 中的内容：

<pre>
&lt;?xml version="1.0" encoding="UTF-8"?&gt;
&lt;beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://www.springframework.org/schema/beans
    http://www.springframework.org/schema/beans/spring-beans-3.0.xsd"&gt;

   &lt;bean id="helloWorld" class="com.tutorialspoint.HelloWorld"&gt;
       &lt;property name="message" value="Hello World!"/&gt;
   &lt;/bean&gt;

&lt;/beans&gt;
</pre>

如果你已经完成上面的内容，接下来，让我们运行这个应用程序。如果程序没有错误，你将从控制台看到以下信息：

<pre class="result notranslate">
Your Message : Hello World!
</pre>