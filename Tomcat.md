# 一、概述

服务器为后台开发(Java、c++、PHP、.NET、python、Go)，而移动开发(Android、ios)、嵌入式开发(c、c++、汇编)、前端开发(HTML、css、js)均属于客户端开发。

c、c++具有跨平台性，即使用平台相关的编译器生成对应平台的可执行文件。在windows上是PE格式，在MAC上是mach-o格式，在linux上是ELF格式。

Java编译后的是.class，字节码文件，对于操作系统来说，不是可执行文件。需要在操作系统上安装JVM(java virtual machine)，Java虚拟机。最后由JVM翻译成0101的机器指令。如果代码有语法错误，将编译失败，不会生成字节码文件。Oracle公司能提供JDK，JDK中包括了JRE(Java runtime environment),有了JRE，就有JVM。

<b>服务器要运行开发人员的Java代码，就需要服务器软件</b>。开源的服务器软件有tomcat，而tomcat本身就是由Java开发的。 

<b>流程</b>：客户端发送请求，对应端口的服务器软件就调用对应的开发人员的Java程序。

# 二、tomcat服务器软件使用

tomcat可以在bin目录下找到startup.bat进行启动。双击后过一段时间，看到Server startup in [560] milliseconnds表示已经启动了。

http地址格式：<b>`http://IP地址:端口号/context` path</b>，context path也是项目。IP地址用于找到服务器，端口号用于找到对应的服务器软件，context path用于找项目名称。在startup.bat里可以看到http-nio-8080，表示端口号为8080，只需要在浏览器地址栏里输入<b>`http://localhost:8080，或者http://IP地址：端口号`,就能访问tomcat服务器。</b>（当然是在tomcat开启的情况下，即startu.dat启动过）

打开webapps文件夹，可以看到有许多项目文件夹。可以看到里面有docs文件夹，因此在浏览器地址栏输入`http://localhost:8080/docs`，就会跳转到docs项目的目录下。

## 1、在tomcat上部署项目

只要将项目部署在tomcat上，客户端就可以通过端口(默认是8080)访问服务器。

步骤：在IDEA上新建一个项目，右键选择add framework support，选择第一个web application，默认建立就会出现一个web模块。右键web模块，选择新建一个HTML文件。为了能够访问tomcat，直接将tomcat集成到IDEA中。点击右上角锤子旁边的add configurations。找到tomcat server，选择local。然后在application server右边的configuration选择tomcat目录，就能看到http port为8080.然后点击deployment(部署)，点击右边的加号，选择add，就能把当前的项目部署到tomcat里了。在同一界面下可以看到application context为/Network_war_exploded，表明当前web模块的名称为这个，意味着启动tomcat后，只需要在浏览器地址栏里输入`http://127.0.0.1:8080/Network_war_exploded/test.html`就能访问该项目该模块里的test网页了。在services中可以看到部署花了206milliseconds，并且deployment successfully。

`http://localhost:8080/day01_war_exploded/test.html`中day01_war_exploded是访问的路径，表示以后可以通过该路径访问test.html文件。

在add configurations里可以进行设置服务器软件(这里是tomcat)，然后要进行部署(deploy)，部署的过程中要添加artifact，最好<b>改变application context方便记忆</b>。

如果<b>只是`http://localhost:8080/day01_war_exploded，访问的是默认的</b>index.jsp or index.html

如果改了文件的内容，需要选择左边的刷新符号(<b>update tomcat configuration)or按下ctrl+f10，选择redeploy</b>

输入用户名和密码后会跳转到百度的页面

~~~html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>登录</title>
</head>
<form action="http://www.baidu.com">	<!--一定要加http://才能访问；注释的格式是<!--内容这样子-->
    <body>
    <div>用户名<input name="username"></div>
    <div>密码<input name="password"></div>
    <button type="submit">登录</button>	<!--要加上subit才能提供到服务器中-->
    </body>
</form>

</html>
~~~

要提交到自己的服务器上，需要在form的action里输入自己服务器的路径。

~~~html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>登录</title>
</head>
<form action="/day01_war_exploded">	<!--一定要加http://才能访问；注释的格式是<!--内容这样子-->
    <body>
    <div>用户名<input name="username"></div>
    <div>密码<input name="password"></div>
    <button type="submit">登录</button>	<!--要加上subit才能提供到服务器中-->
    </body>
</form>

</html>
~~~

当输入用户名和密码后，选择登录就会跳转到index的界面上了。如果要接收发过来的内容，需要用到servlet，servlet位于tomcat中的库(jsp-api.jar和servlet-api.jar)。选择project structure,找到当前项目的modules，选择右边的dependencies，点击右边的+，选择library-tomcat,add selected，点击OK，可以看到左边的external libraries有tomcat。

可以创建多层文件夹，com.asus.servlet.loginservlet。如果是华为的工程师，`www.huawei.com`，包就是com.huawei.xx.xxx，后面是包的功能之类的

~~~java
package com.asus.servlet;

import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.IOException;
/*
处理登录请求
1、继承HttpServlet，处理HTTP请求
2、使用@WebServlet，说明要处理的请求的路径
*/
@WebServlet ("/login")  //WebServlet后要加上空格
public class LoginServlet extends HttpServlet {
    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        System.out.println("doGet-------");
    }

    @Override
    protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        //System.out.println("doPost-------");
        //req request；resp response
        //1、获取客户端发送的数据(请求数据)
        String username = req.getParameter("username");
        String password = req.getParameter("password");
        System.out.println(username+"_"+password);

        //2、判断
        if("123".equals(username) && "456".equals(password)){
            resp.getWriter().write("login success!!!");
        }else{
            resp.getWriter().write("login failure!!!");
        }
    }
}
~~~

~~~html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>登录</title>
</head>
<form action="/day01_war_exploded/login" method="get">	<!--一定要加http://才能访问；如果是本地的服务器，不需要html/，也不需要.html;注释的格式是这样子-->
    <body>
    <div>用户名<input name="username"></div>
    <div>密码<input name="password"></div>
    <button type="submit">登录</button>	<!--要加上submit才能提供到服务器中-->
    </body>
</form>

</html>
~~~

重新部署(Redeploy)后，就看到在IDEA中的output处有doGet-------显示，同时请求参数放在了url后面。

post请求，只需要将method改为post，即使不输入密码点击登录就能看到output处有dopost-----了。post与get不同，请求参数不会放在url后面。<b>可以在浏览器中，按下f12，打开network，选择login-标头(edge浏览器适用)</b>，就可以看到是post。

@param HttpServletRequest req:请求，用来获取客户端发送的数据

@param HttpServletResponse resp:响应，用来给客户端返回数据


