## 项目搭建

1. 搭建一个maven web项目

2. 配置Tomcat

3. 导入jar包

    jsp、Servlet、mysql驱动、jstl、stand...

    ~~~xml
    <dependency>
        <groupId>junit</groupId>
        <artifactId>junit</artifactId>
        <version>4.11</version>
    </dependency>
    <dependency>
        <groupId>javax.servlet</groupId>
        <artifactId>servlet-api</artifactId>
        <version>2.5</version>
    </dependency>
    <!-- https://mvnrepository.com/artifact/mysql/mysql-connector-java -->
    <dependency>
        <groupId>mysql</groupId>
        <artifactId>mysql-connector-java</artifactId>
        <version>8.0.27</version>
    </dependency>
    <!-- https://mvnrepository.com/artifact/javax.servlet.jsp/jsp-api -->
    <dependency>
        <groupId>javax.servlet.jsp</groupId>
        <artifactId>jsp-api</artifactId>
        <version>2.2</version>
    </dependency>
    <dependency>
        <groupId>taglibs</groupId>
        <artifactId>standard</artifactId>
        <version>1.1.2</version>
    </dependency>
    <dependency>
        <groupId>javax.servlet.jsp.jstl</groupId>
        <artifactId>javax.servlet.jsp.jstl-api</artifactId>
        <version>1.2.2</version>
    </dependency>
    <!-- https://mvnrepository.com/artifact/com.alibaba/fastjson -->
    <dependency>
        <groupId>com.alibaba</groupId>
        <artifactId>fastjson</artifactId>
        <version>1.2.79</version>
    </dependency>
    ~~~

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

#### 登录功能优化

**注销功能**

~~~java
protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
    // 移除用户的session 中的值
    req.getSession().removeAttribute(Constants.USER_SESSION);
    resp.sendRedirect("/login.jsp");    // 返回登录页面
}
~~~

~~~xml
<servlet>
    <servlet-name>LoginOutServlet</servlet-name>
    <servlet-class>com.zhang.servlet.user.LoginOutServlet</servlet-class>
</servlet>
<servlet-mapping>
    <servlet-name>LoginOutServlet</servlet-name>
    <url-pattern>/user/logout</url-pattern>
</servlet-mapping>
~~~

**拦截器**

~~~java
public void doFilter(ServletRequest servletRequest, ServletResponse servletResponse, FilterChain filterChain) throws IOException, ServletException {

    // 向下强转
    HttpServletRequest rsq = (HttpServletRequest) servletRequest;
    HttpServletResponse resp = (HttpServletResponse) servletResponse;

    // 从Session 中获取用户
    if (rsq.getSession().getAttribute(Constants.USER_SESSION) == null){
        resp.sendRedirect("/error.jsp");
    }else{
        filterChain.doFilter(servletRequest,servletResponse);
    }
}
~~~

~~~xml
<!--  用户登录过滤器-->
<filter>
    <filter-name>SysFilter</filter-name>
    <filter-class>com.zhang.filter.SysFilter</filter-class>
</filter>
<filter-mapping>
    <filter-name>SysFilter</filter-name>
    <url-pattern>/jsp/*</url-pattern>
</filter-mapping>
~~~

## 密码修改

建议从底层往上写

1. UserDao 接口

    ~~~java
    // 修改当前用户密码
    public int updataPwd(Connection connection, int id, String password)
    ~~~

2. UserDao实现类

    ~~~java
    // 修改当前用户密码
    @Override
    public int updataPwd(Connection connection, int id, String password) throws SQLException {
        int execute = 0;
        PreparedStatement pstm = null;
        String sql = "update smbms.smbms_user set userPassword = ? where id = ?";
        if (connection != null) {
            Object params[] = {password, id};
            execute = BaseDao.execute(connection, sql, pstm, params);
            BaseDao.closed(null, pstm, null);
        }
        return execute;
    }
    ~~~

3. UserService 接口

    ~~~java
    // 根据用户ID 修改密码
    public boolean updataPwd(int id, String password);
    ~~~

4. UserService 实现类

    ~~~java
    public boolean updataPwd(int id, String password) {
        Connection connection = null;
        boolean flag = false;
        connection = BaseDao.getConnection();
        // 修改密码
        try {
            if (userDao.updataPwd(connection,id,password)>0){
                flag = true;
            }
        } catch (SQLException e) {
            e.printStackTrace();
        }finally {
            BaseDao.closed(connection,null,null);
        }
        return flag;
    }
    ~~~

5. servlet 层实验跳转

    ~~~java
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        // 从 Session 里面拿Id
        Object attribute = req.getSession().getAttribute(Constants.USER_SESSION);
        String newpassword = req.getParameter("newpassword");
        boolean flag = false;
        if (attribute != null && !StringUtils.isNullOrEmpty(newpassword)){
            UserServiceImpl userServlet = new UserServiceImpl();
            flag= userServlet.updataPwd(((User) attribute).getId(), newpassword);
            if (flag){
                req.setAttribute("message","修改密码成功，请退出，使用新密码登录");
                // 密码修改成功，移除当前Session
                req.getSession().removeAttribute(Constants.USER_SESSION);
            }else{
                req.setAttribute("message","密码修改失败！");
                // 密码修改失败
            }
        }else{
            req.setAttribute("message","新密码有问题");
        }
        req.getRequestDispatcher("/jsp/pwdmodify.jsp").forward(req,resp);
    }
    ~~~

    ~~~xml
    <!--  修改密码的注册-->
    <servlet>
        <servlet-name>userServlet</servlet-name>
        <servlet-class>com.zhang.servlet.user.UserServlet</servlet-class>
    </servlet>
    <servlet-mapping>
        <servlet-name>userServlet</servlet-name>
        <url-pattern>/user/user.do</url-pattern>
    </servlet-mapping>
    ~~~

#### 密码修改优化（Ajax）

~~~js
$.ajax({
    type:"GET",
    url:path+"/user/user.do",
    data:{method:"pwdmodify",oldpassword:oldpassword.val()},
    dataType:"json",
    success:function(data){
        if(data.result == "true"){//旧密码正确
            validateTip(oldpassword.next(),{"color":"green"},imgYes,true);
        }else if(data.result == "false"){//旧密码输入不正确
            validateTip(oldpassword.next(),{"color":"red"},imgNo + " 原密码输入不正确",false);
        }else if(data.result == "sessionerror"){//当前用户session过期，请重新登录
            validateTip(oldpassword.next(),{"color":"red"},imgNo + " 当前用户session过期，请重新登录",false);
        }else if(data.result == "error"){//旧密码输入为空
            validateTip(oldpassword.next(),{"color":"red"},imgNo + " 请输入旧密码",false);
        }
    },
    error:function(data){
        //请求出错
        validateTip(oldpassword.next(),{"color":"red"},imgNo + " 请求错误",false);
    }
});
~~~

~~~java
// 验证旧密码，Session 中有用户的密码
public void pwdModify(HttpServletRequest req, HttpServletResponse resp){
    // 从Session里面拿Id；
    Object attribute = req.getSession().getAttribute(Constants.USER_SESSION);
    String oldpassword = req.getParameter("oldpassword");

    // 使用 Map 存放结果集
    Map<String,String> resultMap = new HashMap<>();

    if (attribute == null){ // Session 失效了，过期了
        resultMap.put("result","sessionerror");
    }else if (oldpassword == null){ // 输入的密码为空
        resultMap.put("result","error");
    }else{
        String userPassword = ((User) attribute).getUserPassword(); // session中用户的密码
        if (oldpassword.equals(userPassword)){
            resultMap.put("result","true");
        }else{
            resultMap.put("result","false");
        }
    }
    try {
        resp.setContentType("application/json");
        PrintWriter writer = resp.getWriter();
        writer.write(JSONArray.toJSONString(resultMap));

        writer.flush();
        writer.close();
    } catch (IOException e) {
        e.printStackTrace();
    }

}
~~~

## *用户管理实现

#### 获取用户数量(分页)

1. UserDao 接口

    ~~~java
    // 查询用户总数
    public int getUserCount(Connection connection,int userRole,String uername)
    ~~~

2. UserDaoImol实体类

    ~~~java
    // 查询用户总数
    @Override
    public int getUserCount(Connection connection, int userRole,String username) throws SQLException {
        ResultSet rs = null;
        PreparedStatement pstm = null;
        int count = 0;
        if (connection != null){
            // 用户的总数
            String sql = "select count(1) as count from smbms_user u,smbms_role r where u.userRole = r.id";
            ArrayList<Object> list = new ArrayList<>();
            // 有了限制条件的总数
            if (userRole > 0 && username != null){
                sql = sql  + " and u.userName like ?"+" and u.userRole = ?";
                list.add(username);
                list.add(userRole);
            }else if (userRole > 0){    // 用户角色总数
                sql = sql+" and u.userRole = ?";
                list.add(userRole);
            }else if (username != null){    // 用户名字模糊查询
                sql = sql + " and u.userName like ?";
                list.add("%"+username+"%");
    
            }
            System.out.println(sql);
            Object[] params = list.toArray();
            System.out.println(Arrays.toString(params));
            rs = BaseDao.execute(connection,sql,rs,pstm,params);
            if (rs.next()){
                // 从结果集获得最终的数量
                count = rs.getInt("count");
            }
            BaseDao.closed(null,pstm,rs);
        }
        return count;
    }
    ~~~

3. UserService 接口

    ~~~java
    // 查询记录数
    public int getUserCount(String username,int userRole);
    ~~~

4. UserServiceImpl 实体类

    ~~~java
    @Override
    public int getUserCount(String username, int userRole) {
        Connection connection = null;
        int count = 0;
        try {
            connection = BaseDao.getConnection();
            count = userDao.getUserCount(connection, userRole, username);
    
        } catch (SQLException e) {
            e.printStackTrace();
        }finally {
            BaseDao.closed(connection,null,null);
        }
        return count;
    }
    ~~~

#### 获取用户列表

1. UserDao 接口

     ~~~java
     // 通过条件查询 userList
     public List<User> getUserList(Connection connection,String userName,int userRole,int currentPageNo,int pageSize) 
     ~~~

2. UserDaoImpl 实现类

     ~~~java
     // 通过条件查询 user-list
     @Override
     public List<User> getUserList(Connection connection, String userName, int userRole, int currentPageNo, int pageSize) throws Exception {
     
         PreparedStatement pstm = null;
         ResultSet rs = null;
         ArrayList<User> userArrayList = new ArrayList<>();
     
         if (connection != null){
             String sql = "select * from smbms_user u,smbms_role r where u.userRole = r.id";
             ArrayList<Object> list = new ArrayList<>();
     
             // 限制条件
             if (userRole > 0 && userName != null){
                 sql = sql + " and userRole = ? and userName = ?";
                 list.add(userRole);
                 list.add("%"+userName+"%");
             }else if (userRole > 0){
                 sql = sql + " and userRole = ?";
                 list.add(userRole);
             }else if (userName != null){
                 sql = sql + " and userName = ?";
                 list.add("%"+userName+"%");
             }
     
             // 分页
             if (currentPageNo < 1){     // 如果输入了小于 1 则让 等于查询第一页
                 currentPageNo = 1;
             }
             sql = sql + " order by r.creationDate desc limit ?,?";
             currentPageNo = (currentPageNo-1)*pageSize;
             list.add(currentPageNo);
             list.add(pageSize);
     
             // 读取数据
             Object[] params = list.toArray();
             System.out.println(Arrays.toString(params));
             System.out.println(sql);
             rs = BaseDao.execute(connection,sql,rs,pstm,params);
             while(rs.next()){
                 User user = new User();
                 user.setUserCode(rs.getString("userCode"));
                 user.setUserName(rs.getString("userName"));
                 user.setGender(rs.getInt("gender"));
                 user.setBirthday(rs.getDate("birthday"));
                 user.setPhone(rs.getString("phone"));
                 user.setUserRoleName(rs.getString("roleName"));
                 userArrayList.add(user);
             }
             BaseDao.closed(null,pstm,rs);
         }
         return userArrayList;
     }
     ~~~

3. UserService 接口

     ~~~java
     // 根据条件查询用户列表
     public List<User> getUserList(String queryUserName,int queryUserRole,int currentPageNo,int pageSize);
     ~~~

4. UserServiceImol 实现类

    ~~~java
    @Override
    public List<User> getUserList(String queryUserName, int queryUserRole, int currentPageNo, int pageSize) {
        Connection connection = null;
        List<User> userList = null;
        try {
            connection = BaseDao.getConnection();
            userList = userDao.getUserList(connection, queryUserName, queryUserRole, currentPageNo, pageSize);
        } catch (Exception e) {
            e.printStackTrace();
        }finally {
            BaseDao.closed(connection,null,null);
        }
        return userList;
    }
    ~~~

#### 获取角色列表

可以将角色的操作单独放在一个包中，和POJO 一一对应

1. RoleDao 接口

     ~~~java
     // 获取角色列表
     public List<Role> getRoleList(Connection connection) throws SQLException;
     ~~~

2. RoleDaoImpl 实现类

     ~~~java
     // 角色列表
     @Override
     public List<Role> getRoleList(Connection connection) throws SQLException {
     
         PreparedStatement prsm = null;
         ResultSet rs = null;
         ArrayList<Role> list = new ArrayList<>();
         if (connection != null){
             String sql = "select * from smbms_role";
             Object[] param = {};
             rs = BaseDao.execute(connection, sql, rs, prsm, param);
             while(rs.next()){
                 Role role = new Role();
                 role.setId(rs.getInt("id"));
                 role.setRoleCode(rs.getString("roleCode"));
                 role.setRoleName(rs.getString("roleName"));
                 list.add(role);
             }
         }
         return list;
     }
     ~~~

3. RoleService 接口

     ~~~java
     // 获取角色列表
     public List<Role> getRoleList();
     ~~~

4. RoleServiceImol 实现类

    ~~~java
    @Override
    public List<Role> getRoleList() {
    
        Connection connection = null;
        List<Role> roleList = null;
        try {
            connection = BaseDao.getConnection();
            roleList = roleDao.getRoleList(connection);
        } catch (SQLException e) {
            e.printStackTrace();
        }finally {
            BaseDao.closed(connection,null,null);
        }
        return roleList;
    }
    ~~~


#### 用户显示的Servlet

1. 获取用户前端的数据

    ~~~java
    // 从前端取得数据
    String queryUserName = req.getParameter("queryname");           // 用户名
    String temp = req.getParameter("queryUserRole");            // 用户角色
    String pageIndex = req.getParameter("pageIndex");           // 开始页
    ~~~

2. 判断请求是否需要执行，通过值来判断

    ~~~java
    // 第一次走这个请求，一定是第一页，页面暂时设置固定大小
    int pageSize = 5;   // 最好写到配置文件中，方便后期修改
    int currentPageNo = 1;
    int queryUserRole = 0;
    
    if (queryUserName == null){
        queryUserName = "";
    }
    if (temp != null && ! temp.equals("")){
        queryUserRole = Integer.parseInt(temp);
    }
    if (pageIndex != null){
        currentPageNo = Integer.parseInt(pageIndex);
    }
    ~~~

3. 实现分页，需要计算出当前页面和总页面，页面大小

    ~~~java
    // 获取用户列表
    UserServiceImpl userService = new UserServiceImpl();
    // 获取用户总数
    int totalCount = userService.getUserCount(queryUserName, queryUserRole);
    // 总页数
    PageSupport pageSupport = new PageSupport();
    pageSupport.setCurrentPageNo(currentPageNo);
    pageSupport.setPageSize(pageSize);
    pageSupport.setTotalCount(totalCount);
    
    double tempTemp = totalCount;
    int totalPageCount = (int)Math.ceil(tempTemp/pageSize);  // 总共有几页
    
    // 控制首页和尾页
    // 如果页面要小于 1了，就显示第一页的东西
    if (currentPageNo<1){
        currentPageNo = 1;
    }else if (currentPageNo > totalPageCount){
        currentPageNo = totalPageCount;
    }
    ~~~

4. 用户列表展示

    ~~~java
    // 获取用户列表展示
    List<User> userList = userService.getUserList(queryUserName, queryUserRole,currentPageNo,pageSize);
    req.setAttribute("userList",userList);
    
    RoleServiceImpl roleService = new RoleServiceImpl();
    List<Role> roleList = roleService.getRoleList();
    req.setAttribute("roleList",roleList);
    
    req.setAttribute("totalCount",totalCount);
    req.setAttribute("currentPageNo",currentPageNo);
    req.setAttribute("totalPageCount",totalPageCount);
    req.setAttribute("queryUserName",queryUserName);
    req.setAttribute("queryUserRole",queryUserRole);
    ~~~

5. 返回前端

    ~~~java
    // 返回前端
    req.getRequestDispatcher("/jsp/userlist.jsp").forward(req,resp);
    ~~~

