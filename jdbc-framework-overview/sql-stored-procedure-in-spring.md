# Spring 中 SQL 的存储过程

**SimpleJdbcCall** 类可以被用于调用一个包含 IN 和 OUT 参数的存储过程。你可以在处理任何一个 RDBMS 时使用这个方法，就像 Apache Derby， DB2， MySQL， Microsoft SQL Server， Oracle，和 Sybase。

为了了解这个方法，我们使用我们的 Student 表，它可以在 MySQL TEST 数据库中使用下面的 DDL 进行创建：

```
CREATE TABLE Student(
   ID   INT NOT NULL AUTO_INCREMENT,
   NAME VARCHAR(20) NOT NULL,
   AGE  INT NOT NULL,
   PRIMARY KEY (ID)
);
```

下一步，考虑接下来的 MySQL 存储过程，该过程使用 学生 Id 并且使用 OUT 参数返回相应的学生的姓名和年龄。所以让我们在你的 TEST 数据库中使用 MySQL 命令提示符创建这个存储过程：

```
DELIMITER $$
DROP PROCEDURE IF EXISTS `TEST`.`getRecord` $$
CREATE PROCEDURE `TEST`.`getRecord` (
IN in_id INTEGER,
OUT out_name VARCHAR(20),
OUT out_age  INTEGER)
BEGIN
   SELECT name, age
   INTO out_name, out_age
   FROM Student where id = in_id;
END $$
DELIMITER ;
```

现在，让我们编写我们的 Spring JDBC 应用程序，它可以实现对我们的 Student 数据库表的创建和读取操作。让我们使 Eclipse IDE 处于工作状态，然后按照如下步骤创建一个 Spring 应用程序：

<table class="table table-bordered">
<tr><th class="fivepct">步骤</th><th>描述</th></tr>
<tr><td>1</td><td>创建一个名为 <i>SpringExample</i> 的项目，并且在所创建项目的 <b>src</b> 文件夹下创建一个名为 <i>com.tutorialspoint</i> 的包。</td></tr>
<tr><td>2</td><td>使用 <i>Add External JARs</i> 选项添加所需的 Spring 库文件，就如在 <i>Spring Hello World Example</i>  章节中解释的那样。</td></tr>
<tr><td>3</td><td>在项目中添加 Spring JDBC 指定的最新的库文件 <b>mysql-connector-java.jar</b>， <b>org.springframework.jdbc.jar</b> 和  <b>org.springframework.transaction.jar</b>。如果你还没有这些所需要的库文件，你可以下载它们。</td></tr>
<tr><td>4</td><td>创建 DAO 接口 <i>StudentDAO</i> 并且列出所有需要的方法。 即使他不是必需的，你可以直接编写 <i>StudentJDBCTemplate</i> 类，但是作为一个良好的实践，让我们编写它。</td></tr>
<tr><td>5</td><td>在 <i>com.tutorialspoint</i> 包下创建其他所需要的 Java 类 <i>Student</i>， <i>StudentMapper</i>， <i>StudentJDBCTemplate</i> 和 <i>MainApp</i>。</td></tr>
<tr><td>6</td><td>确保你已经在 TEST 数据库中创建了 <b>Student</b> 表。同样确保你的 MySQL 服务器是正常工作的，并且保证你可以使用给定的用户名和密码对数据库有读取/写入的权限。</td></tr>
<tr><td>7</td><td>在 <b>src</b> 文件夹下创建 Beans 配置文件  <i>Beans.xml</i>。</td></tr>
<tr><td>8</td><td>最后一步是创建所有 Java 文件和 Bean 配置文件的内容，并且按如下解释的那样运行应用程序。</td></tr>
</table>

下面是数据访问对象接口文件 **StudentDAO.java** 的内容：

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

下面是 **StudentMapper.java** 文件的内容：

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

下面是实现类文件 **StudentJDBCTemplate.java**，定义了 DAO 接口 StudentDAO：

```
package com.tutorialspoint;
import java.util.Map;
import javax.sql.DataSource;
import org.springframework.jdbc.core.JdbcTemplate;
import org.springframework.jdbc.core.namedparam.MapSqlParameterSource;
import org.springframework.jdbc.core.namedparam.SqlParameterSource;
import org.springframework.jdbc.core.simple.SimpleJdbcCall;
public class StudentJDBCTemplate implements StudentDAO {
   private DataSource dataSource;
   private SimpleJdbcCall jdbcCall;
   public void setDataSource(DataSource dataSource) {
      this.dataSource = dataSource;
      this.jdbcCall =  new SimpleJdbcCall(dataSource).
                       withProcedureName("getRecord");
   }
   public void create(String name, Integer age) {
      JdbcTemplate jdbcTemplateObject = new JdbcTemplate(dataSource);
      String SQL = "insert into Student (name, age) values (?, ?)";
      jdbcTemplateObject.update( SQL, name, age);
      System.out.println("Created Record Name = " + name + " Age = " + age);
      return;
   }
   public Student getStudent(Integer id) {
      SqlParameterSource in = new MapSqlParameterSource().
                              addValue("in_id", id);
      Map<String, Object> out = jdbcCall.execute(in);
      Student student = new Student();
      student.setId(id);
      student.setName((String) out.get("out_name"));
      student.setAge((Integer) out.get("out_age"));
      return student;
   }
   public List<Student> listStudents() {
      String SQL = "select * from Student";    
      List <Student> students = jdbcTemplateObject.query(SQL, 
                                      new StudentMapper());
      return students;
   }
}
```

关于上述项目的几句话：你编写的课调用执行的代码涉及创建一个包含 IN 参数的 *SqlParameterSource*。名称的匹配是很重要的，该名称可以使用在存储过程汇总声明的参数名称来提供输入值。*execute* 方法利用 IN 参数返回一个包含在存储过程中由名称指定的任何外部参数键的映射。现在让我们移动主应用程序文件 **MainApp.java**，如下所示：

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
      System.out.println("----Listing Record with ID = 2 -----" );
      Student student = studentJDBCTemplate.getStudent(2);
      System.out.print("ID : " + student.getId() );
      System.out.print(", Name : " + student.getName() );
      System.out.println(", Age : " + student.getAge());	  
   }
}
```

下面是配置文件 **Beans.xml**：

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

一旦你已经完成的创建了源文件和 bean 配置文件，让我们运行一下应用程序。如果你的应用程序一切都正常的话，这将会输出以下消息：

```
------Records Creation--------
Created Record Name = Zara Age = 11
Created Record Name = Nuha Age = 2
Created Record Name = Ayan Age = 15
------Listing Multiple Records--------
ID : 1, Name : Zara, Age : 11
ID : 2, Name : Nuha, Age : 2
ID : 3, Name : Ayan, Age : 15
----Listing Record with ID = 2 -----
ID : 2, Name : Nuha, Age : 2
```