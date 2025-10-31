<!DOCTYPE html>
<html>
<head>
    <meta charset="UTF-8">
    <title>User Login</title>
</head>
<body>
    <h2>Login Form</h2>
    <form method="post" action="login">
        <label for="username">Username:</label>
        <input type="text" name="username" id="username" required /><br/><br/>

        <label for="password">Password:</label>
        <input type="password" name="password" id="password" required /><br/><br/>

        <input type="submit" value="Login" />
    </form>
</body>
</html>
package com.myapp.servlet;

import java.io.IOException;
import java.io.PrintWriter;
import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

@WebServlet("/login")
public class LoginServlet extends HttpServlet {
    private static final long serialVersionUID = 1L;

    // Hard-coded credentials for demonstration
    private static final String VALID_USERNAME = "admin";
    private static final String VALID_PASSWORD = "password123";

    @Override
    protected void doPost(HttpServletRequest request, HttpServletResponse response)
            throws ServletException, IOException {
        // Set response content type
        response.setContentType("text/html;charset=UTF-8");

        // Get writer
        PrintWriter out = response.getWriter();

        // Retrieve form parameters
        String username = request.getParameter("username");
        String password = request.getParameter("password");

        // Validate credentials
        if (VALID_USERNAME.equals(username) && VALID_PASSWORD.equals(password)) {
            // Success
            out.println("<html><body>");
            out.println("<h2>Welcome, " + username + "!</h2>");
            out.println("<p>You have successfully logged in.</p>");
            out.println("</body></html>");
        } else {
            // Failure
            out.println("<html><body>");
            out.println("<h2>Login Failed</h2>");
            out.println("<p>Invalid username or password.</p>");
            out.println("<a href=\"login.html\">Try again</a>");
            out.println("</body></html>");
        }

        out.close();
    }
}
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
         version="3.1"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee
                             http://xmlns.jcp.org/xml/ns/javaee/web-app_3_1.xsd">

    <servlet>
        <servlet-name>LoginServlet</servlet-name>
        <servlet-class>com.myapp.servlet.LoginServlet</servlet-class>
    </servlet>

    <servlet-mapping>
        <servlet-name>LoginServlet</servlet-name>
        <url-pattern>/login</url-pattern>
    </servlet-mapping>

    <welcome-file-list>
        <welcome-file>login.html</welcome-file>
    </welcome-file-list>

</web-app>
