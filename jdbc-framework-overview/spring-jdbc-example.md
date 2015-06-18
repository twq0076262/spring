# Spring JDBC 示例

想要理解带有 jdbc 模板类的 Spring JDBC 框架的相关概念，让我们编写一个简单的示例，来实现下述 Student 表的所有 CRUD 操作。

``` 
CREATE TABLE Student(
   ID   INT NOT NULL AUTO_INCREMENT,
   NAME VARCHAR(20) NOT NULL,
   AGE  INT NOT NULL,
   PRIMARY KEY (ID)
);
```

在继续之前，让我们适当地使用 Eclipse IDE 并按照如下所示的步骤创建一个 Spring 应用程序：

<table class="table table-bordered">
<tr><th class="fivepct">步骤</th><th>描述</th></tr>
<tr><td>1</td><td>创建一个名为 <i>SpringExample</i> 的项目，并在创建的项目中的 <b>src</b> 文件夹下创建包 <i>com.tutorialspoint</i>。</td></tr>
<tr><td>2</td><td>使用 <i>Add External JARs</i> 选项添加必需的 Spring 库，解释见 <i>Spring Hello World Example</i> 章节。</td></tr>
<tr><td>3</td><td>在项目中添加 Spring JDBC 指定的最新的库 <b>mysql-connector-java.jar</b>，<b>org.springframework.jdbc.jar</b> 和 <b>org.springframework.transaction.jar</b>。如果这些库不存在，你可以下载它们。</td></tr>
<tr><td>4</td><td>创建 DAO 接口 <i>StudentDAO</i> 并列出所有必需的方法。尽管这一步不是必需的而且你可以直接编写 <i>StudentJDBCTemplate</i> 类，但是作为一个好的实践，我们最好还是做这一步。</td></tr>
<tr><td>5</td><td>在 <i>com.tutorialspoint</i> 包下创建其他的必需的 Java 类 <i>Student</i>，<i>StudentMapper</i>，<i>StudentJDBCTemplate</i> 和 <i>MainApp</i> 。</td></tr>
<tr><td>6</td><td>确保你已经在 TEST 数据库中创建了 <b>Student</b> 表。并确保你的 MySQL 服务器运行正常，且你可以使用给出的用户名和密码读/写访问数据库。</td></tr>
<tr><td>7</td><td>在 <b>src</b> 文件夹下创建 Beans 配置文件 <i>Beans.xml</i>。</td></tr>
<tr><td>8</td><td>最后一步是创建所有的 Java 文件和 Bean 配置文件的内容并按照如下所示的方法运行应用程序。</td></tr>
</table>

以下是数据访问对象接口文件 **StudentDAO.java** 的内容：

``` 
package com.tutorialspoint;
import java.util.List;
import javax.sql.DataSource;
public interface StudentDAO {
   /** 
    * This is the method to be used to initialize
    * database resources ie. connection.
    */
   public void setDataSource(DataSource ds);
   /** 
    * This is the method to be used to create
    * a record in the Student table.
    */
   public void create(String name, Integer age);
   /** 
    * This is the method to be used to list down
    * a record from the Student table corresponding
    * to a passed student id.
    */
   public Student getStudent(Integer id);
   /** 
    * This is the method to be used to list down
    * all the records from the Student table.
    */
   public List<Student> listStudents();
   /** 
    * This is the method to be used to delete
    * a record from the Student table corresponding
    * to a passed student id.
    */
   public void delete(Integer id);
   /** 
    * This is the method to be used to update
    * a record into the Student table.
    */
   public void update(Integer id, Integer age);
}
```

下面是 **Student.java** 文件的内容：

``` 
package com.tutorialspoint;
public class Student {
   private Integer age;
   private String name;
   private Integer id;
   public void setAge(Integer age) {
      this.age = age;
   }
   public Integer getAge() {
      return age;
   }
   public void setName(String name) {
      this.name = name;
   }
   public String getName() {
      return name;
   }
   public void setId(Integer id) {
      this.id = id;
   }
   public Integer getId() {
      return id;
   }
}
```

以下是 **StudentMapper.java** 文件的内容：

``` 
package com.tutorialspoint;
import java.sql.ResultSet;
import java.sql.SQLException;
import org.springframework.jdbc.core.RowMapper;
public class StudentMapper implements RowMapper<Student> {
   public Student mapRow(ResultSet rs, int rowNum) throws SQLException {
      Student student = new Student();
      student.setId(rs.getInt("id"));
      student.setName(rs.getString("name"));
      student.setAge(rs.getInt("age"));
      return student;
   }
}
```

下面是为定义的 DAO 接口 StudentDAO 的实现类文件 **StudentJDBCTemplate.java**：

``` 
package com.tutorialspoint;
import java.util.List;
import javax.sql.DataSource;
import org.springframework.jdbc.core.JdbcTemplate;
public class StudentJDBCTemplate implements StudentDAO {
   private DataSource dataSource;
   private JdbcTemplate jdbcTemplateObject; 
   public void setDataSource(DataSource dataSource) {
      this.dataSource = dataSource;
      this.jdbcTemplateObject = new JdbcTemplate(dataSource);
   }
   public void create(String name, Integer age) {
      String SQL = "insert into Student (name, age) values (?, ?)";     
      jdbcTemplateObject.update( SQL, name, age);
      System.out.println("Created Record Name = " + name + " Age = " + age);
      return;
   }
   public Student getStudent(Integer id) {
      String SQL = "select * from Student where id = ?";
      Student student = jdbcTemplateObject.queryForObject(SQL, 
                        new Object[]{id}, new StudentMapper());
      return student;
   }
   public List<Student> listStudents() {
      String SQL = "select * from Student";
      List <Student> students = jdbcTemplateObject.query(SQL, 
                                new StudentMapper());
      return students;
   }
   public void delete(Integer id){
      String SQL = "delete from Student where id = ?";
      jdbcTemplateObject.update(SQL, id);
      System.out.println("Deleted Record with ID = " + id );
      return;
   }
   public void update(Integer id, Integer age){
      String SQL = "update Student set age = ? where id = ?";
      jdbcTemplateObject.update(SQL, age, id);
      System.out.println("Updated Record with ID = " + id );
      return;
   }
}
```

以下是 **MainApp.java** 文件的内容：

``` 
package com.tutorialspoint;
import java.util.List;
import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;
import com.tutorialspoint.StudentJDBCTemplate;
public class MainApp {
   public static void main(String[] args) {
      ApplicationContext context = 
             new ClassPathXmlApplicationContext("Beans.xml");
      StudentJDBCTemplate studentJDBCTemplate = 
      (StudentJDBCTemplate)context.getBean("studentJDBCTemplate");    
      System.out.println("------Records Creation--------" );
      studentJDBCTemplate.create("Zara", 11);
      studentJDBCTemplate.create("Nuha", 2);
      studentJDBCTemplate.create("Ayan", 15);
      System.out.println("------Listing Multiple Records--------" );
      List<Student> students = studentJDBCTemplate.listStudents();
      for (Student record : students) {
         System.out.print("ID : " + record.getId() );
         System.out.print(", Name : " + record.getName() );
         System.out.println(", Age : " + record.getAge());
      }
      System.out.println("----Updating Record with ID = 2 -----" );
      studentJDBCTemplate.update(2, 20);
      System.out.println("----Listing Record with ID = 2 -----" );
      Student student = studentJDBCTemplate.getStudent(2);
      System.out.print("ID : " + student.getId() );
      System.out.print(", Name : " + student.getName() );
      System.out.println(", Age : " + student.getAge());	  
   }
}
```

下述是配置文件 **Beans.xml** 的内容：

<pre class="prettyprint notranslate">
&lt;?xml version="1.0" encoding="UTF-8"?&gt;
&lt;beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" 
    xsi:schemaLocation="http://www.springframework.org/schema/beans
    http://www.springframework.org/schema/beans/spring-beans-3.0.xsd "&gt;

   &lt;!-- Initialization for data source --&gt;
   &lt;bean id="dataSource" 
      class="org.springframework.jdbc.datasource.DriverManagerDataSource"&gt;
      &lt;property name="driverClassName" value="com.mysql.jdbc.Driver"/&gt;
      &lt;property name="url" value="jdbc:mysql://localhost:3306/TEST"/&gt;
      &lt;property name="username" value="root"/&gt;
      &lt;property name="password" value="password"/&gt;
   &lt;/bean&gt;

   &lt;!-- Definition for studentJDBCTemplate bean --&gt;
   &lt;bean id="studentJDBCTemplate" 
      class="com.tutorialspoint.StudentJDBCTemplate"&gt;
      &lt;property name="dataSource"  ref="dataSource" /&gt;    
   &lt;/bean&gt;
      
&lt;/beans&gt;
</pre>

当你完成创建源和 bean 配置文件后，运行应用程序。如果你的应用程序一切运行顺利的话，将会输出如下所示的消息：

<pre class="result notranslate">
------Records Creation--------
Created Record Name = Zara Age = 11
Created Record Name = Nuha Age = 2
Created Record Name = Ayan Age = 15
------Listing Multiple Records--------
ID : 1, Name : Zara, Age : 11
ID : 2, Name : Nuha, Age : 2
ID : 3, Name : Ayan, Age : 15
----Updating Record with ID = 2 -----
Updated Record with ID = 2
----Listing Record with ID = 2 -----
ID : 2, Name : Nuha, Age : 20
</pre>

你可以尝试自己删除在我的例子中我没有用到的操作，但是现在你有一个基于 Spring JDBC 框架的工作应用程序，你可以根据你的项目需求来扩展这个框架，添加复杂的功能。还有其他方法来访问你使用 **NamedParameterJdbcTemplate** 和 **SimpleJdbcTemplate** 类的数据库，所以如果你有兴趣学习这些类的话，那么你可以查看 Spring 框架的参考手册。