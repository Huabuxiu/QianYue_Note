# JDBC 流程






## JDBC操作数据库步骤

1. 加载数据库驱动

2. 创建并获取数据库链接

3. 创建jdbc statement对象

4. 设置sql语句

5. 设置sql语句中的参数(使用preparedStatement)

6. 通过statement执行sql并获取结果

7. 对sql执行结果进行解析处理

8. 释放资源(resultSet、preparedstatement、connection)


## 问题总结

1. 数据库连接使用时创建不用后立即释放，对数据进行频繁连接，影响性能，可以考虑数据库连接池提升效率。
2. sql语句写在java程序中不便于维护和修改，修改过后需要重新编译，通过加载配置文件来改进。
3. preparedStatement 设置参数写死，可以考虑配置文件或者传递pojo对象
4. resultSet 结果集遍历也不便于修改，可以考虑生成对象返回。



## 实例代码



```java 

public class JdbcTest {
    private static final String DRIVER_NAME = "com.mysql.jdbc.Driver";
    //数据库连接地址
    private static final String URL = "jdbc:mysql://localhost:3306/mybatis";
    //用户名
    private static final String USER_NAME = "root";
    //密码
    private static final String PASSWORD = "123456";
    
    public static void main(String[] args) {
        //数据库连接
        Connection connection = null;
        //预编译的Statement，使用预编译的Statement提高数据库性能
        PreparedStatement preparedStatement = null;
        //结果集
        ResultSet resultSet = null;

        try {
            //加载数据库驱动
            Class.forName("com.mysql.jdbc.Driver");
    
            //通过驱动管理类获取数据库链接
            connection =  DriverManager.getConnection(URL,USER_NAME,PASSWORD);
            //定义sql语句 ?表示占位符
            String sql = "select * from user where username = ?";
            //获取预处理statement
            preparedStatement = connection.prepareStatement(sql);
            //设置参数，第一个参数为sql语句中参数的序号（从1开始），第二个参数为设置的参数值
            preparedStatement.setString(1, "王五");
            //向数据库发出sql执行查询，查询出结果集
            resultSet =  preparedStatement.executeQuery();
            //遍历查询结果集
            while(resultSet.next()){
                System.out.println(resultSet.getString("id")+"  "+resultSet.getString("username"));
            }
        } catch (Exception e) {
            e.printStackTrace();
        }finally{
            //释放资源
            if(resultSet!=null){
                try {
                    resultSet.close();
                } catch (SQLException e) {
                    // TODO Auto-generated catch block
                    e.printStackTrace();
                }
            }
            if(preparedStatement!=null){
                try {
                    preparedStatement.close();
                } catch (SQLException e) {
                    // TODO Auto-generated catch block
                    e.printStackTrace();
                }
            }
            if(connection!=null){
                try {
                    connection.close();
                } catch (SQLException e) {
                    // TODO Auto-generated catch block
                    e.printStackTrace();
                }
            }
    
        }
    
    }

}
```
