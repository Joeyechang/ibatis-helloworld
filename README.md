# ibatis-helloworld
ibatis测试例子
原文地址：http://blog.csdn.net/jubincn/article/details/7438436


原文地址：http://loianegroner.com/2011/02/getting-started-with-ibatis-mybatis-xml-configuration/

这份教程将使用一个简单的Java项目来带您体验iBatis（MyBatis），并展示简单的插入、修改查询和删除操作。
准备工作
在此教程中，将用到下面这些工具：
IDE：Eclipse
Database：MySQL
Libs/jars：MyBatis，MySQL和JUnit

工程在Eclipse中的布局应该类似这样：


样例数据库
使用下面的代码生成项目所需的数据库和样例数据：
```
[sql] view plain copy print?
DROP TABLE IF EXISTS `blog`.`contact`;  
CREATE TABLE  `blog`.`contact` (  
  `CONTACT_ID` int(11) NOT NULL AUTO_INCREMENT,  
  `CONTACT_EMAIL` varchar(255) NOT NULL,  
  `CONTACT_NAME` varchar(255) NOT NULL,  
  `CONTACT_PHONE` varchar(255) NOT NULL,  
  PRIMARY KEY (`CONTACT_ID`)  
)  
ENGINE=InnoDB;  
   
insert into CONTACT (CONTACT_NAME, CONTACT_PHONE, CONTACT_EMAIL) values ('Contact0','(000) 000-0000', 'contact0@loianetest.com');  
insert into CONTACT (CONTACT_NAME, CONTACT_PHONE, CONTACT_EMAIL) values ('Contact1', '(000) 000-0000', 'contact1@loianetest.com');  
insert into CONTACT (CONTACT_NAME, CONTACT_PHONE, CONTACT_EMAIL) values ('Contact2', '(000) 000-0000', 'contact2@loianetest.com');  
insert into CONTACT (CONTACT_NAME, CONTACT_PHONE, CONTACT_EMAIL) values ('Contact3', '(000) 000-0000', 'contact3@loianetest.com');  
insert into CONTACT (CONTACT_NAME, CONTACT_PHONE, CONTACT_EMAIL) values ('Contact4', '(000) 000-0000', 'contact4@loianetest.com');  
insert into CONTACT (CONTACT_NAME, CONTACT_PHONE, CONTACT_EMAIL) values ('Contact5', '(000) 000-0000', 'contact5@loianetest.com');  
insert into CONTACT (CONTACT_NAME, CONTACT_PHONE, CONTACT_EMAIL) values ('Contact6', '(000) 000-0000', 'contact6@loianetest.com');  
insert into CONTACT (CONTACT_NAME, CONTACT_PHONE, CONTACT_EMAIL) values ('Contact7', '(000) 000-0000', 'contact7@loianetest.com');  
insert into CONTACT (CONTACT_NAME, CONTACT_PHONE, CONTACT_EMAIL) values ('Contact8', '(000) 000-0000', 'contact8@loianetest.com');  
insert into CONTACT (CONTACT_NAME, CONTACT_PHONE, CONTACT_EMAIL) values ('Contact9', '(000) 000-0000', 'contact9@loianetest.com');  
insert into CONTACT (CONTACT_NAME, CONTACT_PHONE, CONTACT_EMAIL) values ('Contact10', '(000) 000-0000', 'contact10@loianetest.com');  
insert into CONTACT (CONTACT_NAME, CONTACT_PHONE, CONTACT_EMAIL) values ('Contact11', '(000) 000-0000', 'contact11@loianetest.com');  
insert into CONTACT (CONTACT_NAME, CONTACT_PHONE, CONTACT_EMAIL) values ('Contact12', '(000) 000-0000', 'contact12@loianetest.com');  
insert into CONTACT (CONTACT_NAME, CONTACT_PHONE, CONTACT_EMAIL) values ('Contact13', '(000) 000-0000', 'contact13@loianetest.com');  
insert into CONTACT (CONTACT_NAME, CONTACT_PHONE, CONTACT_EMAIL) values ('Contact14', '(000) 000-0000', 'contact14@loianetest.com');  
insert into CONTACT (CONTACT_NAME, CONTACT_PHONE, CONTACT_EMAIL) values ('Contact15', '(000) 000-0000', 'contact15@loianetest.com');  
insert into CONTACT (CONTACT_NAME, CONTACT_PHONE, CONTACT_EMAIL) values ('Contact16', '(000) 000-0000', 'contact16@loianetest.com');  
insert into CONTACT (CONTACT_NAME, CONTACT_PHONE, CONTACT_EMAIL) values ('Contact17', '(000) 000-0000', 'contact17@loianetest.com');  
insert into CONTACT (CONTACT_NAME, CONTACT_PHONE, CONTACT_EMAIL) values ('Contact18', '(000) 000-0000', 'contact18@loianetest.com');  
insert into CONTACT (CONTACT_NAME, CONTACT_PHONE, CONTACT_EMAIL) values ('Contact19', '(000) 000-0000', 'contact19@loianetest.com');  

```

1 – Contact POJO
首先我们创建一个Contact类，包含id，name，phone和email这几个字段。

```
[java] view plain copy print?
package com.loiane.model;  
   
public class Contact {  
   
    private int id;  
    private String name;  
    private String phone;  
    private String email;  
   
    public Contact(int id, String name, String phone, String email) {  
        super();  
        this.id = id;  
        this.name = name;  
        this.phone = phone;  
        this.email = email;  
    }  
   
    public Contact() {}  
   
    //getters and setters  
}  

```

2 - Contact.xml
Contact.xml是iBatis/MyBatis的SQL Map配置文件。我们将在这里编写SQL语句，并将这些SQL语句与对象的方法配置到一起，这里是ORM魔法的源泉。

```
<?xml version="1.0" encoding="UTF-8"?>  
<!DOCTYPE mapper  
  PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"  
    "http://mybatis.org/dtd/mybatis-3-mapper.dtd">  
   
<mapper namespace="Contact">  
   
    <resultMap id="result" type="Contact">  
        <result property="id" column="CONTACT_ID"/>  
        <result property="name" column="CONTACT_NAME"/>  
        <result property="phone" column="CONTACT_PHONE"/>  
        <result property="email" column="CONTACT_EMAIL"/>  
    </resultMap>  
   
    <select id="getAll" resultMap="result">  
        SELECT * FROM CONTACT  
    </select>  
   
    <select id="getById" parameterType="int" resultMap="result">  
        SELECT * FROM CONTACT WHERE CONTACT_ID = #{id}  
    </select>  
   
    <delete id="deleteById" parameterType="int">  
        DELETE from CONTACT WHERE CONTACT_ID = #{id};  
    </delete>  
   
    <insert id="insert" parameterType="Contact">  
    INSERT INTO CONTACT (CONTACT_EMAIL, CONTACT_NAME, CONTACT_PHONE)  
        VALUES (#{name}, #{phone}, #{email});  
      <selectKey keyProperty="id" resultType="int" order="AFTER">  
        select last_insert_id() as id  
      </selectKey>  
    </insert>  
   
    <update id="update" parameterType="Contact">  
        UPDATE CONTACT  
        SET  
            CONTACT_EMAIL = #{email},  
            CONTACT_NAME = #{name},  
            CONTACT_PHONE = #{phone}  
        WHERE CONTACT_ID = #{id};  
  </update>  
   
</mapper>  
```
  
Contact.xml中包含这些内容：
resultMap：iBatis中最强大也是最复杂的部分，它实现了Java对象到数据库表格的映射。
insert：映射的insert语句
update：映射的update语句
select：映射的select语句
delete：映射的delete语句
Result Map
Result Map是MyBatis/iBatis SQL Map配置文件中最强大也是最复杂的元素。相比JDBC从Result Set中获取数据的方式，Result Map给用户减轻了90%的工作量，在某些方面，它还给用户提供了一些JDBC所没有的功能。实际上，实现一个复杂的语join操作映射可能需要上千行代码。Result Map的设计使用户不必编写代码就可以实现简单的statement映射，即使是复杂的statement，用户也仅需描述一下其中的关联即可。
在这个例子中，表格的列名和Contact类中的字段不一致，这正是我们要将列明和字段名的关联写到配置文件中的原因。如果类名和字段名一致，就不需在Result Map中使用“column=”选项。
TypeAlias是个好东西，它使我们不必总是输入长长的完整类名，我们将在iBatis的主配置文件中使用TypeAlias。
Select语句
本例中的第一个select语句的id是“getAll”，我们将在DAO类中使用这个id来调用SQL语句。我们设置的另一个选项是resultMap，它将结果映射到Contact类，其执行结果是返回一个包含Contact对象的List。
本例中的第二个select语句的id是“getById”。我们设置了一个int型的参数，返回类型为Contact。注意参数的写法：#{id}，这将使iBatis生成一个PreparedStatement的参数。在JDBC中，这将在传给PreparedStatement的SQL语句中用一个“？”表示。
Delete语句
删除语句很简单，我们通过参数id来确认要删除的记录。
Update语句
在update语句中参数的类型为Contact类，这意味着我们将传递一个类型为Contact的参数给DAO中的方法。注意SQL中的参数#{name}, #{phone}, #{email}和#{id}。SQL中所有的参数名必须和Contact的属性名一致，否则iBatis/MyBatis将无法完成映射。
Insert语句
相比其他语句，insert语句拥有更多的属性的子元素，从而提供了多种自动生成主键的方法。首先，如果数据库支持自动生成主键，那么您可以通过设置useGeneratedKeys=”true” 并将keyProperty设为相应的主键即可。MyBatis实现在不支持自动生成列甚至不支持JDBC的数据库中自动生成主键。
在这个例子中，我们将使用selectKey选项来自动生成主键。在这个例子中，selectKey将在insert执行之后执行，通过last_insert_id()方法我们可以获得最近生成的主键值并将其设置到id属性中。
3 - Mapper配置文件
MyBatis的XML配置文件中包含的设置和属性对MyBatis的运行有重要影响，文档的高层结构如下所示：
Configuration
properties
settings
typeAliases
typeHandlers
objectFactory
plugins
environments
environment
transactionManager
dataSource
mappers
提示：配置文件中子元素的顺序必须与上述结构的顺序一致
此项目中SqlMapConfig.xml文件：

```

<?xml version="1.0" encoding="UTF-8"?>  
<!DOCTYPE configuration  
    PUBLIC "-//mybatis.org//DTD Config 3.0//EN"  
    "http://mybatis.org/dtd/mybatis-3-config.dtd">  
   
<configuration>  
   
    <typeAliases>  
        <typeAlias alias="Contact" type="com.loiane.model.Contact"/>  
    </typeAliases>  
   
    <environments default="development">  
        <environment id="development">  
          <transactionManager type="JDBC"/>  
            <dataSource type="POOLED">  
                <property name="driver" value="com.mysql.jdbc.Driver"/>  
                <property name="url" value="jdbc:mysql://localhost:3306/blog"/>  
                <property name="username" value="root"/>  
                <property name="password" value="root"/>  
            </dataSource>  
       </environment>  
    </environments>  
   
    <mappers>  
       <mapper resource="com/loiane/data/Contact.xml"/>  
    </mappers>  
   
</configuration>  

```

下面我们来看看这些配置：
TypeAliases
一个Type Aliases就是一个Java类的别名，它只应用于XML文档自身并且其作用就是为了实现缩写。
还记得我们在Contact.xml中直接使用Contact来表示Contact类吗？TypeAliases帮了我们大忙！
Environments
MyBatis中可以同时配置多个Environment，这将使你可以将SQL映射到多个数据库中。例如你可以同时配置开发、测试和运行时的数据库配置。或者，你有多个拥有同样schema的数据库，而你想使用同样的SQL来映射到他们上。
但有一点我们必须牢记：尽管你可以同时配置多个环境，一个SqlSessionFactory实例只能使用一个配置。
默认的environment和environment的id是可以望文生义的。只要保证默认的environment对应其中的一个，你可以任意对environment进行命名。
Transaction Manager
在MyBatis中有两种类型的Transaction Manager：JDBC和MANAGED
JDBC：直接使用JDBC提供的commit和rollback来实现事务，依赖于从dataSource中获得的连接来控制食物的范围。
MANAGED：这种配置相当于什么也不做。它从不提交、回滚一个连接。相反，它通过容器（例如：Spring或其他JavaEE容器）来管理事务。默认它是不关闭连接的。然而，有些容器默认并不是这样，因此需要手动来进行配置，将closeConnection属性设为false即可。
在本例中，我们将使用JDBC。

```

DataSource
dataSource元素用于用于配置使用标准JDBC接口的数据源。
driver – This is the fully qualified Java class of the JDBC driver (NOT of the DataSource class if your driver includes one).
url – This is the JDBC URL for your database instance.
username – The database username to log in with.
password – The database password to log in with.
4 – MyBatisConnectionFactory
每一个MyBatis应用的核心都是一个SqlSessionFactory。SqlSessionFactory对象可以通过SqlSessionFactoryBuilder获得。SqlSessionFactoryBuilder可以解析XML配置文件或已有的SqlSessionFactory对象来生成相应的SqlSessionFactory。
[java] view plain copy print?
package com.loiane.dao;  
   
import java.io.FileNotFoundException;  
import java.io.IOException;  
import java.io.Reader;  
   
import org.apache.ibatis.io.Resources;  
import org.apache.ibatis.session.SqlSessionFactory;  
import org.apache.ibatis.session.SqlSessionFactoryBuilder;  
   
public class MyBatisConnectionFactory {  
   
    private static SqlSessionFactory sqlSessionFactory;  
   
    static {  
        try {  
            String resource = "SqlMapConfig.xml";  
            Reader reader = Resources.getResourceAsReader(resource);  
   
            if (sqlSessionFactory == null) {  
                sqlSessionFactory = new SqlSessionFactoryBuilder().build(reader);  
            }  
        }  
        catch (FileNotFoundException fileNotFoundException) {  
            fileNotFoundException.printStackTrace();  
        }  
        catch (IOException iOException) {  
            iOException.printStackTrace();  
        }  
    }  
   
    public static SqlSessionFactory getSqlSessionFactory() {  
   
        return sqlSessionFactory;  
    }  
}  
```

5 – ContactDAO

现在我们已经准备好了所需的一切，下面来创建DAO。为了调用sql语句，我们将通过命名空间和SQL语句的id来调用。

```

package com.loiane.dao;  
   
import java.util.List;  
   
import org.apache.ibatis.session.SqlSession;  
import org.apache.ibatis.session.SqlSessionFactory;  
   
import com.loiane.model.Contact;  
   
public class ContactDAO {  
   
    private SqlSessionFactory sqlSessionFactory;  
   
    public ContactDAO(){  
        sqlSessionFactory = MyBatisConnectionFactory.getSqlSessionFactory();  
    }  
   
    /** 
     * Returns the list of all Contact instances from the database. 
     * @return the list of all Contact instances from the database. 
     */  
    @SuppressWarnings("unchecked")  
    public List<Contact> selectAll(){  
   
        SqlSession session = sqlSessionFactory.openSession();  
   
        try {  
            List<Contact> list = session.selectList("Contact.getAll");  
            return list;  
        } finally {  
            session.close();  
        }  
    }  
   
    /** 
     * Returns a Contact instance from the database. 
     * @param id primary key value used for lookup. 
     * @return A Contact instance with a primary key value equals to pk. null if there is no matching row. 
     */  
    public Contact selectById(int id){  
   
        SqlSession session = sqlSessionFactory.openSession();  
   
        try {  
            Contact contact = (Contact) session.selectOne("Contact.getById",id);  
            return contact;  
        } finally {  
            session.close();  
        }  
    }  
   
    /** 
     * Updates an instance of Contact in the database. 
     * @param contact the instance to be updated. 
     */  
    public void update(Contact contact){  
   
        SqlSession session = sqlSessionFactory.openSession();  
   
        try {  
            session.update("Contact.update", contact);  
            session.commit();  
        } finally {  
            session.close();  
        }  
    }  
   
    /** 
     * Insert an instance of Contact into the database. 
     * @param contact the instance to be persisted. 
     */  
    public void insert(Contact contact){  
   
        SqlSession session = sqlSessionFactory.openSession();  
   
        try {  
            session.insert("Contact.insert", contact);  
            session.commit();  
        } finally {  
            session.close();  
        }  
    }  
   
    /** 
     * Delete an instance of Contact from the database. 
     * @param id primary key value of the instance to be deleted. 
     */  
    public void delete(int id){  
   
        SqlSession session = sqlSessionFactory.openSession();  
   
        try {  
            session.delete("Contact.deleteById", id);  
            session.commit();  
        } finally {  
            session.close();  
        }  
    }  
} 

```

下载
您若想了解更多有关MyBatis的知识，请您下载MyBatis的用户手册，那里拥有您所需的关于MyBatis一切。所有带引号的句子都来自MyBatis用户手册。在实现这个样例工程的过程中我也经常查阅那个手册。
我同时还创建了一个测试类。您若想获得整个样例工程的源码，可以从github上下载： https://github.com/loiane/ibatis-helloworld

