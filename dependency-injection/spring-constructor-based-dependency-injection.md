# Spring 基于构造函数的依赖注入

当容器调用带有一组参数的类构造函数时，基于构造函数的 DI 就完成了，其中每个参数代表一个对其他类的依赖。

## 示例：

下面的例子显示了一个类 *TextEditor*，只能用构造函数注入来实现依赖注入。

让我们用 Eclipse IDE 适当地工作，并按照以下步骤创建一个 Spring 应用程序。

<table class="table table-bordered">
<tr><th class="fivepct">步骤</th><th>描述</th></tr>
<tr><td>1</td><td>创建一个名为 <i>SpringExample</i> 的项目，并在创建的项目中的 <b>src</b> 文件夹下创建包 <i>com.tutorialspoint</i> 。</td></tr>
<tr><td>2</td><td>使用 <i>Add External JARs</i> 选项添加必需的 Spring 库，解释见 <i>Spring Hello World Example</i> chapter.</td></tr>
<tr><td>3</td><td>在 <i>com.tutorialspoint</i> 包下创建 Java类 <i>TextEditor</i>，<i>SpellChecker</i> 和 <i>MainApp</i>。 </td></tr>
<tr><td>4</td><td>在 <b>src</b> 文件夹下创建 Beans 的配置文件 <i>Beans.xml</i> 。</td></tr>
<tr><td>5</td><td>最后一步是创建所有 Java 文件和 Bean 配置文件的内容并按照如下所示的方法运行应用程序。</td></tr>
</table>

这是 **TextEditor.java** 文件的内容：

``` 
package com.tutorialspoint;
public class TextEditor {
   private SpellChecker spellChecker;
   public TextEditor(SpellChecker spellChecker) {
      System.out.println("Inside TextEditor constructor." );
      this.spellChecker = spellChecker;
   }
   public void spellCheck() {
      spellChecker.checkSpelling();
   }
}
```

下面是另一个依赖类文件 **SpellChecker.java** 的内容：

``` 
package com.tutorialspoint;
public class SpellChecker {
   public SpellChecker(){
      System.out.println("Inside SpellChecker constructor." );
   }
   public void checkSpelling() {
      System.out.println("Inside checkSpelling." );
   } 
}
```

以下是 **MainApp.java** 文件的内容：

``` 
package com.tutorialspoint;
import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;
public class MainApp {
   public static void main(String[] args) {
      ApplicationContext context = 
             new ClassPathXmlApplicationContext("Beans.xml");
      TextEditor te = (TextEditor) context.getBean("textEditor");
      te.spellCheck();
   }
}
```

下面是配置文件 **Beans.xml** 的内容，它有基于构造函数注入的配置：

<pre class="prettyprint notranslate">
&lt;?xml version="1.0" encoding="UTF-8"?&gt;

&lt;beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://www.springframework.org/schema/beans
    http://www.springframework.org/schema/beans/spring-beans-3.0.xsd"&gt;

   &lt;!-- Definition for textEditor bean --&gt;
   &lt;bean id="textEditor" class="com.tutorialspoint.TextEditor"&gt;
      &lt;constructor-arg ref="spellChecker"/&gt;
   &lt;/bean&gt;

   &lt;!-- Definition for spellChecker bean --&gt;
   &lt;bean id="spellChecker" class="com.tutorialspoint.SpellChecker"&gt;
   &lt;/bean&gt;

&lt;/beans&gt;
</pre> 

当你完成了创建源和 bean 配置文件后，让我们开始运行应用程序。如果你的应用程序运行顺利的话，那么将会输出下述所示消息：

<pre class="result notranslate">
Inside SpellChecker constructor.
Inside TextEditor constructor.
Inside checkSpelling.
</pre> 

## 构造函数参数解析:

如果存在不止一个参数时，当把参数传递给构造函数时，可能会存在歧义。要解决这个问题，那么构造函数的参数在 bean 定义中的顺序就是把这些参数提供给适当的构造函数的顺序就可以了。考虑下面的类:

``` 
package x.y;
public class Foo {
   public Foo(Bar bar, Baz baz) {
      // ...
   }
}
```

下述配置文件工作顺利：

<pre class="prettyprint notranslate">
&lt;beans&gt;
   &lt;bean id="foo" class="x.y.Foo"&gt;
      &lt;constructor-arg ref="bar"/&gt;
      &lt;constructor-arg ref="baz"/&gt;
   &lt;/bean&gt;

   &lt;bean id="bar" class="x.y.Bar"/&gt;
   &lt;bean id="baz" class="x.y.Baz"/&gt;
&lt;/beans&gt;
</pre>

让我们再检查一下我们传递给构造函数不同类型的位置。考虑下面的类：

``` 
package x.y;
public class Foo {
   public Foo(int year, String name) {
      // ...
   }
}
```

如果你使用 type 属性显式的指定了构造函数参数的类型，容器也可以使用与简单类型匹配的类型。例如：

<pre class="prettyprint notranslate">
&lt;beans&gt;

   &lt;bean id="exampleBean" class="examples.ExampleBean"&gt;
      &lt;constructor-arg type="int" value="2001"/&gt;
      &lt;constructor-arg type="java.lang.String" value="Zara"/&gt;
   &lt;/bean&gt;

&lt;/beans&gt;
</pre> 

最后并且也是最好的传递构造函数参数的方式，使用 index 属性来显式的指定构造函数参数的索引。下面是基于索引为 0 的例子，如下所示：

<pre class="prettyprint notranslate">
&lt;beans&gt;

   &lt;bean id="exampleBean" class="examples.ExampleBean"&gt;
      &lt;constructor-arg index="0" value="2001"/&gt;
      &lt;constructor-arg index="1" value="Zara"/&gt;
   &lt;/bean&gt;

&lt;/beans&gt;
</pre> 

最后，如果你想要向一个对象传递一个引用，你需要使用 <constructor-arg> 标签的 **ref** 属性，如果你想要直接传递值，那么你应该使用如上所示的 **value** 属性。