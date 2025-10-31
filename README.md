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
package com.myapp.controller;

import java.io.*;
import java.sql.*;
import javax.servlet.*;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.*;

@WebServlet("/employee")
public class EmployeeServlet extends HttpServlet {
    private String jdbcURL  = "jdbc:mysql://localhost:3306/yourdb";
    private String jdbcUser = "root";
    private String jdbcPass = "yourpassword";

    protected void doGet(HttpServletRequest req, HttpServletResponse resp)
            throws ServletException, IOException {
        resp.setContentType("text/html;charset=UTF-8");
        PrintWriter out = resp.getWriter();
        out.println("<h2>Employee Records</h2>");

        String id = req.getParameter("id");

        try {
            Class.forName("com.mysql.cj.jdbc.Driver");
            try (Connection conn = DriverManager.getConnection(jdbcURL, jdbcUser, jdbcPass)) {
                Statement stmt = conn.createStatement();
                String sql;
                if (id != null && !id.trim().isEmpty()) {
                    sql = "SELECT EmpID, Name, Salary FROM employees WHERE EmpID = " + Integer.parseInt(id);
                } else {
                    sql = "SELECT EmpID, Name, Salary FROM employees";
                }
                ResultSet rs = stmt.executeQuery(sql);
                out.println("<table border='1'><tr><th>ID</th><th>Name</th><th>Salary</th></tr>");
                while (rs.next()) {
                    out.println("<tr><td>" + rs.getInt("EmpID") + "</td>"
                               + "<td>" + rs.getString("Name") + "</td>"
                               + "<td>" + rs.getDouble("Salary") + "</td></tr>");
                }
                out.println("</table>");
            }
        } catch (Exception e) {
            out.println("<p>Error: " + e.getMessage() + "</p>");
        }

        out.println("<h3>Search by ID</h3>");
        out.println("<form method='get' action='employee'>");
        out.println("ID: <input type='text' name='id'/>");
        out.println("<input type='submit' value='Search'/>");
        out.println("</form>");
    }

    protected void doPost(HttpServletRequest req, HttpServletResponse resp)
            throws ServletException, IOException {
        doGet(req, resp);
    }
}
CREATE TABLE attendance (
  id INT AUTO_INCREMENT PRIMARY KEY,
  student_id VARCHAR(50) NOT NULL,
  att_date DATE NOT NULL,
  status VARCHAR(10) NOT NULL
);
<%@ page language="java" contentType="text/html; charset=UTF-8" pageEncoding="UTF-8"%>
<!DOCTYPE html>
<html>
<head>
  <meta charset="UTF-8">
  <title>Record Attendance</title>
</head>
<body>
  <h2>Student Attendance Form</h2>
  <form action="RecordAttendance" method="post">
    Student ID: <input type="text" name="studentId" required/><br/><br/>
    Date:       <input type="date" name="attDate" required/><br/><br/>
    Status:     <select name="status">
                  <option value="Present">Present</option>
                  <option value="Absent">Absent</option>
                </select><br/><br/>
    <input type="submit" value="Submit Attendance"/>
  </form>
</body>
</html>
package com.myapp.controller;

import java.io.IOException;
import java.sql.*;
import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.*;

@WebServlet("/RecordAttendance")
public class RecordAttendanceServlet extends HttpServlet {
    private static final long serialVersionUID = 1L;

    // Update these with your DB details
    private String jdbcURL  = "jdbc:mysql://localhost:3306/yourdb";
    private String jdbcUser = "root";
    private String jdbcPass = "yourpassword";

    protected void doPost(HttpServletRequest request, HttpServletResponse response)
            throws ServletException, IOException {

        String studentId = request.getParameter("studentId");
        String attDate   = request.getParameter("attDate");
        String status    = request.getParameter("status");

        try {
            Class.forName("com.mysql.cj.jdbc.Driver");
            try (Connection conn = DriverManager.getConnection(jdbcURL, jdbcUser, jdbcPass)) {
                String sql = "INSERT INTO attendance (student_id, att_date, status) VALUES (?, ?, ?)";
                try (PreparedStatement ps = conn.prepareStatement(sql)) {
                    ps.setString(1, studentId);
                    ps.setDate(2, Date.valueOf(attDate));
                    ps.setString(3, status);
                    ps.executeUpdate();
                }
            }
            // redirect to success page
            response.sendRedirect("success.jsp");
        } catch (Exception e) {
            e.printStackTrace();
            response.getWriter().println("Error: " + e.getMessage());
        }
    }

    protected void doGet(HttpServletRequest request, HttpServletResponse response)
            throws ServletException, IOException {
        response.sendRedirect("attendance.jsp");
    }
}
<%@ page language="java" contentType="text/html; charset=UTF-8" pageEncoding="UTF-8"%>
<!DOCTYPE html>
<html>
<head>
  <meta charset="UTF-8">
  <title>Success</title>
</head>
<body>
  <h2>Attendance recorded successfully!</h2>
  <a href="attendance.jsp">Record another</a>
</body>
</html>

