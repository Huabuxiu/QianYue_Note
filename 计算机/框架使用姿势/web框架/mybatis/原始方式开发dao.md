# 原始开发dao方法

## SqlSession使用范围

- SqlSessionFactoryBuilder

通过`SqlSessionFactoryBuilder`创建会话工厂`SqlSessionFactory`将`SqlSessionFactoryBuilder`当成一个工具类使用即可，不需要使用单例管理`SqlSessionFactoryBuilder`。在需要创建`SqlSessionFactory`时候，只需要new一次`SqlSessionFactoryBuilder`即可。

- `SqlSessionFactory`

通过`SqlSessionFactory`创建`SqlSession`，使用单例模式管理`sqlSessionFactory`（工厂一旦创建，使用一个实例）。将来mybatis和spring整合后，使用单例模式管理`sqlSessionFactory`。

- `SqlSession`

`SqlSession`是一个面向用户（程序员）的接口。SqlSession中提供了很多操作数据库的方法：如：`selectOne`(返回单个对象)、`selectList`（返回单个或多个对象）。

`SqlSession`是线程不安全的，在`SqlSesion`实现类中除了有接口中的方法（操作数据库的方法）还有数据域属性。

`SqlSession`最佳应用场合在方法体内，定义成局部变量使用。



### dao接口

```java
public interface UserDao {
    //根据id查询用户信息
    public User findUserById(int id) throws Exception;

    //根据用户名列查询用户列表
    public List<User> findUserByName(String name) throws Exception;

    //添加用户信息
    public void insertUser(User user) throws Exception;

    //删除用户信息
    public void deleteUser(int id) throws Exception;
}
```

### dao接口实现类

```java

public class UserdaoImpl implements UserDao{

    private  SqlSessionFactory sqlSessionFactory ;

    public UserdaoImpl(SqlSessionFactory sessionFactory) {
        this.sqlSessionFactory = sessionFactory;
    }

    @Override
    public User findUserById(int id) {
        User user = null;
        SqlSession sqlSession = sqlSessionFactory.openSession();
        user =  sqlSession.selectOne("test.findUserById",id);
        //提交session
        sqlSession.commit();
        sqlSession.close();
        return user;
    }

    @Override
    public void insertUser(User user) {
        SqlSession sqlSession = sqlSessionFactory.openSession();
        //插入
        sqlSession.insert("test.insertUser",user);
        //提交session
        sqlSession.commit();
        sqlSession.close();
    }

    @Override
    public void deleteUser(User user) {
        SqlSession sqlSession = sqlSessionFactory.openSession();
        //删除
        sqlSession.insert("test.deleteById",user);
        //提交session
        sqlSession.commit();
        sqlSession.close();

    }
```

### 测试

```java
public class UserDaoImplTest {

	private SqlSessionFactory sqlSessionFactory;

	// 此方法是在执行testFindUserById之前执行
	@Before
	public void setUp() throws Exception {
		// 创建sqlSessionFactory

		// mybatis配置文件
		String resource = "SqlMapConfig.xml";
		// 得到配置文件流
		InputStream inputStream = Resources.getResourceAsStream(resource);

		// 创建会话工厂，传入mybatis的配置文件信息
		sqlSessionFactory = new SqlSessionFactoryBuilder()
				.build(inputStream);
	}

	@Test
	public void testFindUserById() throws Exception {
		// 创建UserDao的对象
		UserDao userDao = new UserDaoImpl(sqlSessionFactory);

		// 调用UserDao的方法
		User user = userDao.findUserById(1);
		
		System.out.println(user);
	}

}
```



