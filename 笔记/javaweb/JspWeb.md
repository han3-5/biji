## 项目搭建

1. 搭建一个maven web项目

2. 配置Tomcat

3. 导入jar包

    jsp、Servlet、mysql驱动、jstl、stand...

4. 创建实体类

    和数据库 ORM

5. 编写基础公共类

    1.  数据库配置文件
    2.  编写数据库的公共类
    3.  编写字符编码过滤器

6. 导入静态资源

## 登录功能

1. 编写前端页面

2. 设置首页

    ~~~xml
    <!--  设置欢迎界面-->
    <welcome-file-list>
      <welcome-file>login.jsp</welcome-file>
    </welcome-file-list>
    ~~~

3. 编写dao层用户登录的接口

    ~~~java
    public interface UserDao {
        // 得到要登录的用户
        public User getLoginUser(Connection connection, String userCode);
    } 
    ~~~

4. 编写dao接口的实现方法

    ~~~java
    @Override
    public User getLoginUser(Connection connection, String userCode) {
    
        PreparedStatement pstm = null;
        ResultSet rs = null;
        User user = null;
    
        if (connection != null) {
            String sql = "select * from smbms_user where userCode=?";
            Object[] parmas = {userCode};
            try {
                rs = BaseDao.execute(connection, sql, rs, pstm, parmas);
                if (rs.next()) {
                    user = new User();
                    user.setId(rs.getInt("id"));
                    user.setUserCode(rs.getString("userCode"));
                    user.setUserName(rs.getString("userName"));
                    user.setUserPassword(rs.getString("userPassword"));
                    user.setGender(rs.getInt("gender"));
                    user.setBirthday(rs.getDate("birthday"));
                    user.setPhone(rs.getString("phone"));
                    user.setAddress(rs.getString("address"));
                    user.setUserRole(rs.getInt("userRole"));
                    user.setCreatedBy(rs.getInt("createBy"));
                    user.setCreationDate(rs.getDate("creationDate"));
                    user.setModifyBy(rs.getInt("modifyBy"));
                    user.setModifyDate(rs.getDate("modifyDate"));
                }
                BaseDao.closed(connection,pstm,rs);
            } catch (SQLException e) {
                e.printStackTrace();
            }
        }
        return user;
    }
    ~~~

5. 业务层接口

    ~~~java
    public interface UserService {
        // 用户登录
        public User login(String userCode,String password);
    }
    ~~~

6. 业务层实现类

    ~~~java
    public class UserServiceImpl implements UserService{
        // 业务层都会调用Dao层，所以要引入Dao层
        private UserDao userDao;
        public UserServiceImpl(){
            userDao = new UserDaoImpl();
        }
        @Override
        public User login(String userCode, String password) {
            Connection connection = null;
            connection = BaseDao.getConnection();
            User user = null;
            // 通过业务层调用对应的具体的数据库操作
            user = userDao.getLoginUser(connection,userCode);
    
            BaseDao.closed(connection,null,null);
    
            return user;
        }
    }
    ~~~

7. 编写 Servlet

    ~~~java
    public class LoginServlet extends HttpServlet {
        // Servlet控制层：调用业务层代码
        @Override
        protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
            System.out.println("进入Servlet");
    
            // 获取用户名和密码
            String userCode = req.getParameter("userCode");
            String userPassword = req.getParameter("userPassword");
    
            // 和数据库的密码进行对比，调用业务层
            UserServiceImpl userService = new UserServiceImpl();
            User user = userService.login(userCode, userPassword);
            if (user != null){ // 查到这个人了，可以登录
                req.getSession().setAttribute(Constants.USER_SESSION,user);
                // 跳转到主页
                resp.sendRedirect("/jsp/frame.jsp");
            }else{  // 查不到这个人，无法登录
                // 转发回登录页面，顺便提示，用户名或者密码错误
                req.setAttribute("error","错误错误");
                req.getRequestDispatcher("/login.jsp").forward(req,resp);
            }
        }
        @Override
        protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
            doGet(req, resp);
        }
    }
    ~~~

8. 注册Servlet

    ~~~xml
    <servlet>
        <servlet-name>LoginServlet</servlet-name>
        <servlet-class>com.zhang.servlet.user.LoginServlet</servlet-class>
    </servlet>
    <servlet-mapping>
        <servlet-name>LoginServlet</servlet-name>
        <url-pattern>/user/login.do</url-pattern>
    </servlet-mapping>
    ~~~

## 登录功能优化

**注销功能**

~~~java
~~~

