# Spring 页面重定向例子

下面的例子说明了如何编写一个简单的基于 web 的应用程序，它利用**重定向**来传送一个 http 请求到另一个页面中。为了开始使用它，让我们在恰当的位置使用 Eclipse IDE，然后按照下面的步骤使用 Spring 的 Web 框架来开发一个动态的基于表单的 Web 应用程序：
 
<table class="table table-bordered">
<tr><th class="fivepct">步骤</th><th>描述</th></tr>
<tr><td>1</td><td>创建一个名称为 <i>HelloWeb</i> 的<i>动态 Web 项目</i>，并且在已创建的项目的 <i>src</i> 文件夹中创建一个包 <i>com.tutorialspoint</i>。</td></tr>
<tr><td>2</td><td>将上面提到的 Spring 和其他库拖拽到文件夹 <i>WebContent/WEB-INF/lib</i> 中。</td></tr>
<tr><td>3</td><td>在 <i>com.tutorialspoint</i> 包下创建一个 Java 类 <i>WebController</i>。</td></tr>
<tr><td>4</td><td>在 <i>WebContent/WEB-INF</i> 文件夹下创建 Spring 的配置文件 <i>Web.xml</i> 和 <i>HelloWeb-servlet.xml</i>。</td></tr>
<tr><td>5</td><td>在 <i>WebContent/WEB-INF</i> 文件夹下创建名称为 <i>jsp</i> 的子文件夹。在这个子文件夹下创建视图文件 <i>index.jsp</i> 和 <i>final.jsp</i>。</td></tr>
<tr><td>6</td><td>最后一步是创建所有的源代码和配置文件的内容，并导出该应用程序，正如下面解释的一样。</td></tr>
</table>

这里是 **WebController.java** 文件的内容：

```
package com.tutorialspoint;
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestMethod;
@Controller
public class WebController {
   @RequestMapping(value = "/index", method = RequestMethod.GET)
   public String index() {
	   return "index";
   }   
   @RequestMapping(value = "/redirect", method = RequestMethod.GET)
   public String redirect() {     
      return "redirect:finalPage";
   }   
   @RequestMapping(value = "/finalPage", method = RequestMethod.GET)
   public String finalPage() {     
      return "final";
   }
}
```

下面是 Spring Web 配置文件 **web.xml** 的内容

<pre class="prettyprint notranslate">
&lt;web-app id="WebApp_ID" version="2.4"
    xmlns="http://java.sun.com/xml/ns/j2ee" 
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://java.sun.com/xml/ns/j2ee 
    http://java.sun.com/xml/ns/j2ee/web-app_2_4.xsd"&gt;
 
    &lt;display-name&gt;Spring Page Redirection&lt;/display-name&gt;
 
    &lt;servlet&gt;
        &lt;servlet-name&gt;HelloWeb&lt;/servlet-name&gt;
        &lt;servlet-class&gt;
           org.springframework.web.servlet.DispatcherServlet
        &lt;/servlet-class&gt;
        &lt;load-on-startup&gt;1&lt;/load-on-startup&gt;
    &lt;/servlet&gt;
   
    &lt;servlet-mapping&gt;
        &lt;servlet-name&gt;HelloWeb&lt;/servlet-name&gt;
        &lt;url-pattern&gt;/&lt;/url-pattern&gt;
    &lt;/servlet-mapping&gt;
  
&lt;/web-app&gt;
</pre>

下面是另一个 Spring Web 配置文件 **HelloWeb-servlet.xml** 的内容

<pre class="prettyprint notranslate">
&lt;?xml version="1.0" encoding="UTF-8"?&gt;
&lt;beans xmlns="http://www.springframework.org/schema/beans"
   xmlns:context="http://www.springframework.org/schema/context"
   xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
   xsi:schemaLocation="
   http://www.springframework.org/schema/beans     
   http://www.springframework.org/schema/beans/spring-beans-3.0.xsd
   http://www.springframework.org/schema/context 
   http://www.springframework.org/schema/context/spring-context-3.0.xsd"&gt;
 
    &lt;context:component-scan base-package="com.tutorialspoint" /&gt;
     
    &lt;bean id="viewResolver" class="org.springframework.web.servlet.view.InternalResourceViewResolver"&gt;
    &lt;property name="prefix" value="/WEB-INF/jsp/" /&gt;
    &lt;property name="suffix" value=".jsp" /&gt;
    &lt;/bean&gt;
&lt;/beans&gt;
</pre>

下面是 Spring 视图文件 **index.jsp** 文件的内容。这将是一个登陆页面，这个页面将发送一个请求来访问**重定向** service 方法，该方法将把这个请求重定向到另一个 service 方法中，最后将显示 **final.jsp** 页面。

<pre class="prettyprint notranslate">
&lt;%@taglib uri="http://www.springframework.org/tags/form" prefix="form"%&gt;
&lt;html&gt;
&lt;head&gt;
    &lt;title&gt;Spring Page Redirection&lt;/title&gt;
&lt;/head&gt;
&lt;body&gt;
&lt;h2&gt;Spring Page Redirection&lt;/h2&gt;
&lt;p&gt;Click below button to redirect the result to new page&lt;/p&gt;
&lt;form:form method="GET" action="/HelloWeb/redirect"&gt;
&lt;table&gt;
    &lt;tr&gt;
    &lt;td&gt;
    &lt;input type="submit" value="Redirect Page"/&gt;
    &lt;/td&gt;
    &lt;/tr&gt;
&lt;/table&gt;  
&lt;/form:form&gt;
&lt;/body&gt;
&lt;/html&gt;
</pre>

下面是 Spring 视图文件 **final.jsp** 的内容。这是最终的重定向页面。

<pre class="prettyprint notranslate">
&lt;%@taglib uri="http://www.springframework.org/tags/form" prefix="form"%&gt;
&lt;html&gt;
&lt;head&gt;
    &lt;title&gt;Spring Page Redirection&lt;/title&gt;
&lt;/head&gt;
&lt;body&gt;

&lt;h2&gt;Redirected Page&lt;/h2&gt;

&lt;/body&gt;
&lt;/html&gt;
</pre>

最后，下面是包含在你的 web 应用程序中的 Spring 和其他库的列表。你仅仅需要将这些文件拖拽到 **WebContent/WEB-INF/lib** 文件夹中。

- commons-logging-x.y.z.jar

- org.springframework.asm-x.y.z.jar

- org.springframework.beans-x.y.z.jar

- org.springframework.context-x.y.z.jar

- org.springframework.core-x.y.z.jar

- org.springframework.expression-x.y.z.jar

- org.springframework.web.servlet-x.y.z.jar

- org.springframework.web-x.y.z.jar

- spring-web.jar

一旦你完成了创建源代码和配置文件后，导出你的应用程序。右键单击你的应用程序，并且使用 **Export > WAR File** 选项，并且在 Tomcat 的 *webapps* 文件夹中保存你的 **HelloWeb.war** 文件。

现在启动你的 Tomcat 服务器，并且确保你能够使用标准的浏览器访问 webapps 文件夹中的其他 web 页面。现在尝试访问该 URL **http://localhost:8080/HelloWeb/index**。如果你的 Spring Web 应用程序一切都正常，你应该看到下面的结果：

![](../images/1.4.png)

现在单击 “Redirect Page” 按钮来提交表单，并且得到最终的重定向页面。如果你的 Spring Web 应用程序一切都正常，你应该看到下面的结果：

![](../images/1.5.png)