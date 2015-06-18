# Spring @Autowired 注释

**@Autowired** 注释对在哪里和如何完成自动连接提供了更多的细微的控制。

@Autowired 注释可以在 setter 方法中被用于自动连接 bean，就像 @Autowired 注释，容器，一个属性或者任意命名的可能带有多个参数的方法。

##  Setter 方法中的 @Autowired

你可以在 XML 文件中的 setter 方法中使用 **@Autowired** 注释来除去 <property> 元素。当 Spring遇到一个在 setter 方法中使用的  @Autowired 注释，它会在方法中视图执行 **byType** 自动连接。

## 示例

让我们使 Eclipse IDE 处于工作状态，然后按照如下步骤创建一个 Spring 应用程序：

<table class="table table-bordered">
<tr><th class="fivepct">步骤</th><th>描述</th></tr>
<tr><td>1</td><td>创建一个名为 <i>SpringExample</i> 的项目，并且在所创建项目的 <b>src</b> 文件夹下创建一个名为 <i>com.tutorialspoint</i> 的包。</td></tr>
<tr><td>2</td><td>使用 <i>Add External JARs</i> 选项添加所需的 Spring 库文件，就如在 <i>Spring Hello World Example</i>  章节中解释的那样。</td></tr>
<tr><td>3</td><td>在 <i>com.tutorialspoint</i> 包下创建 Java 类 <i>TextEditor</i>, <i>SpellChecker</i> 和 <i>MainApp</i>。</td></tr>
<tr><td>4</td><td>在 <b>src</b> 文件夹下创建 Beans 配置文件  <i>Beans.xml</i>。</td></tr>
<tr><td>5</td><td>最后一步是创建所有 Java 文件和 Bean 配置文件的内容，并且按如下解释的那样运行应用程序。</td></tr>
</table>

这里是 **TextEditor.java** 文件的内容：

```
package com.tutorialspoint;
import org.springframework.beans.factory.annotation.Autowired;
public class TextEditor {
   private SpellChecker spellChecker;
   @Autowired
   public void setSpellChecker( SpellChecker spellChecker ){
      this.spellChecker = spellChecker;
   }
   public SpellChecker getSpellChecker( ) {
      return spellChecker;
   }
   public void spellCheck() {
      spellChecker.checkSpelling();
   }
}
```

下面是另一个依赖的类文件 **SpellChecker.java** 的内容：

```
package com.tutorialspoint;
public class SpellChecker {
   public SpellChecker(){
      System.out.println("Inside SpellChecker constructor." );
   }
   public void checkSpelling(){
      System.out.println("Inside checkSpelling." );
   }  
}
```

下面是 **MainApp.java** 文件的内容：

```
package com.tutorialspoint;
import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;
public class MainApp {
   public static void main(String[] args) {
      ApplicationContext context = new ClassPathXmlApplicationContext("Beans.xml");
      TextEditor te = (TextEditor) context.getBean("textEditor");
      te.spellCheck();
   }
}
```

下面是配置文件 **Beans.xml**：

<pre class="prettyprint notranslate">
&lt;?xml version="1.0" encoding="UTF-8"?&gt;

&lt;beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:context="http://www.springframework.org/schema/context"
    xsi:schemaLocation="http://www.springframework.org/schema/beans
    http://www.springframework.org/schema/beans/spring-beans-3.0.xsd
    http://www.springframework.org/schema/context
    http://www.springframework.org/schema/context/spring-context-3.0.xsd"&gt;

   &lt;context:annotation-config/&gt;

   &lt;!-- Definition for textEditor bean without constructor-arg  --&gt;
   &lt;bean id="textEditor" class="com.tutorialspoint.TextEditor"&gt;
   &lt;/bean&gt;

   &lt;!-- Definition for spellChecker bean --&gt;
   &lt;bean id="spellChecker" class="com.tutorialspoint.SpellChecker"&gt;
   &lt;/bean&gt;

&lt;/beans&gt;
</pre>

一旦你已经完成的创建了源文件和 bean 配置文件，让我们运行一下应用程序。如果你的应用程序一切都正常的话，这将会输出以下消息：

```
Inside SpellChecker constructor.
Inside checkSpelling.
```

## 属性中的 @Autowired

你可以在属性中使用 **@Autowired** 注释来除去 setter 方法。当时使用 <property> 为自动连接属性传递的时候，Spring 会将这些传递过来的值或者引用自动分配给那些属性。所以利用在属性中 @Autowired 的用法，你的 ** TextEditor.java** 文件将变成如下所示：

```
package com.tutorialspoint;
import org.springframework.beans.factory.annotation.Autowired;
public class TextEditor {
   @Autowired
   private SpellChecker spellChecker;
   public TextEditor() {
      System.out.println("Inside TextEditor constructor." );
   }  
   public SpellChecker getSpellChecker( ){
      return spellChecker;
   }  
   public void spellCheck(){
      spellChecker.checkSpelling();
   }
}
```

下面是配置文件 **Beans.xml**：

<pre class="prettyprint notranslate">
&lt;?xml version="1.0" encoding="UTF-8"?&gt;

&lt;beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:context="http://www.springframework.org/schema/context"
    xsi:schemaLocation="http://www.springframework.org/schema/beans
    http://www.springframework.org/schema/beans/spring-beans-3.0.xsd
    http://www.springframework.org/schema/context
    http://www.springframework.org/schema/context/spring-context-3.0.xsd"&gt;

   &lt;context:annotation-config/&gt;

   &lt;!-- Definition for textEditor bean --&gt;
   &lt;bean id="textEditor" class="com.tutorialspoint.TextEditor"&gt;
   &lt;/bean&gt;

   &lt;!-- Definition for spellChecker bean --&gt;
   &lt;bean id="spellChecker" class="com.tutorialspoint.SpellChecker"&gt;
   &lt;/bean&gt;

&lt;/beans&gt;
</pre>

一旦你在源文件和 bean 配置文件中完成了上面两处改变，让我们运行一下应用程序。如果你的应用程序一切都正常的话，这将会输出以下消息：

```
Inside TextEditor constructor.
Inside SpellChecker constructor.
Inside checkSpelling.
```

## 构造函数中的 @Autowired 

你也可以在构造函数中使用 @Autowired。一个构造函数 @Autowired 说明当创建 bean 时，即使在 XML 文件中没有使用 <constructor-arg>  元素配置 bean ，构造函数也会被自动连接。让我们检查一下下面的示例。

这里是 **TextEditor.java** 文件的内容：

```
package com.tutorialspoint;
import org.springframework.beans.factory.annotation.Autowired;
public class TextEditor {
   private SpellChecker spellChecker;
   @Autowired
   public TextEditor(SpellChecker spellChecker){
      System.out.println("Inside TextEditor constructor." );
      this.spellChecker = spellChecker;
   }
   public void spellCheck(){
      spellChecker.checkSpelling();
   }
}
```

下面是配置文件 **Beans.xml**：

<pre class="prettyprint notranslate">
&lt;?xml version="1.0" encoding="UTF-8"?&gt;

&lt;beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:context="http://www.springframework.org/schema/context"
    xsi:schemaLocation="http://www.springframework.org/schema/beans
    http://www.springframework.org/schema/beans/spring-beans-3.0.xsd
    http://www.springframework.org/schema/context
    http://www.springframework.org/schema/context/spring-context-3.0.xsd"&gt;

   &lt;context:annotation-config/&gt;

   &lt;!-- Definition for textEditor bean without constructor-arg  --&gt;
   &lt;bean id="textEditor" class="com.tutorialspoint.TextEditor"&gt;
   &lt;/bean&gt;

   &lt;!-- Definition for spellChecker bean --&gt;
   &lt;bean id="spellChecker" class="com.tutorialspoint.SpellChecker"&gt;
   &lt;/bean&gt;

&lt;/beans&gt;
</pre>

一旦你在源文件和 bean 配置文件中完成了上面两处改变，让我们运行一下应用程序。如果你的应用程序一切都正常的话，这将会输出以下消息：

```
Inside TextEditor constructor.
Inside SpellChecker constructor.
Inside checkSpelling.
```

## @Autowired 的（required=false）选项

默认情况下，@Autowired 注释意味着依赖是必须的，它类似于 @Required 注释，然而，你可以使用 @Autowired 的 **（required=false）** 选项关闭默认行为。

即使你不为 age 属性传递任何参数，下面的示例也会成功运行，但是对于 name 属性则需要一个参数。你可以自己尝试一下这个示例，因为除了只有 **Student.java** 文件被修改以外，它和 @Required 注释示例是相似的。

```
package com.tutorialspoint;
import org.springframework.beans.factory.annotation.Autowired;
public class Student {
   private Integer age;
   private String name;
   @Autowired(required=false)
   public void setAge(Integer age) {
      this.age = age;
   }  
   public Integer getAge() {
      return age;
   }
   @Autowired
   public void setName(String name) {
      this.name = name;
   }   
   public String getName() {
      return name;
   }
}
```