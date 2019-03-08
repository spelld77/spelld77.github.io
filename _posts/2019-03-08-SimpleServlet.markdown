---
layout: post
title:  "Simple Servlet Work"
date:   2019-03-08 08:54:59
author: Db Jin
categories: Study
---
## web.xml
```xml
<!-- 서블릿 설정 -->  
  <servlet>
    <servlet-name>MemberUpdateServlet</servlet-name>
    <servlet-class>spms.servlet.MemberUpdateServlet</servlet-class>
    <init-param>
      <param-name>driver</param-name>
      <param-value>org.mariadb.jdbc.Driver</param-value>
    </init-param>
    <init-param>
      <param-name>url</param-name>
      <param-value>jdbc:mariadb://localhost/DB_name</param-value>
    </init-param>
    <init-param>
      <param-name>username</param-name>
      <param-value>DB_id</param-value>
    </init-param>
    <init-param>
      <param-name>password</param-name>
      <param-value>DB_password</param-value>
    </init-param>
  </servlet>


  <!-- url 매핑 -->
  <servlet-mapping>
    <servlet-name>MemberUpdateServlet</servlet-name>
    <url-pattern>/member/update</url-pattern>
  </servlet-mapping>
  
  
  <!-- context 초기화 매개변수 -->
  <context-param>
    <param-name>driver</param-name>
    <param-value>org.mariadb.jdbc.Driver</param-value>
  </context-param>
  <context-param>
    <param-name>url</param-name>
    <param-value>jdbc:mariadb://localhost/DB_name</param-value>
  </context-param>
  <context-param>
    <param-name>username</param-name>
    <param-value>DB_id</param-value>
  </context-param>
  <context-param>
    <param-name>password</param-name>
    <param-value>DB_password</param-value>
  </context-param>
```

## List Servlet
```java
@WebServlet("/member/list")
public class MemberListServlet extends HttpServlet {

//	@Override
//	public void service(ServletRequest req, ServletResponse res) throws ServletException, IOException {
//		Connection conn = null;
//		Statement stmt = null;
//		ResultSet rs = null;
//		
//		try {
//			ServletContext sc = this.getServletContext();
//			Class.forName(sc.getInitParameter("driver"));
//			conn = DriverManager.getConnection(
//					sc.getInitParameter("url"),
//					sc.getInitParameter("username"),
//					sc.getInitParameter("password")
//					);
//			stmt = conn.createStatement();
//			rs = stmt.executeQuery("SELECT MNO, MNAME, EMAIL, CRE_DATE"
//					+ " FROM MEMBERS ORDER BY MNO ASC");
//			
//			res.setContentType("text/html; charset=UTF-8");
//			PrintWriter out = res.getWriter();
//			out.println("<html><head><title>회원목록</title></head>");
//			out.println("<body><h1>회원목록</h1>");
//			out.println("<p><a href='add'>신규 회원</a></p>");
//			while(rs.next()) {
//				out.println(
//						rs.getInt("MNO") + ", " +
//						"<a href='update?no=" +rs.getInt("MNO") +"'>" +
//						rs.getString("MNAME") + "</a>, " +
//						rs.getString("EMAIL") + ", " +
//						rs.getDate("CRE_DATE") + "<br>"
//						
//						);
//				
//			}
//			out.println("</body></html>");
//		} catch (Exception e) {
//			// TODO: handle exception
//			throw new ServletException(e);
//		} finally {
//			try {
//				if(rs != null) rs.close();
//				if(stmt != null) rs.close();
//				if(conn != null) rs.close();
//			} catch(Exception e) {
//				
//			}
//		}
//
//	} //service()
	
	@Override
	protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
		Connection conn = null;
		Statement stmt = null;
		ResultSet rs = null;
		
		try {
			ServletContext sc = this.getServletContext();
			Class.forName(sc.getInitParameter("driver"));
			conn = DriverManager.getConnection(
					sc.getInitParameter("url"),
					sc.getInitParameter("username"),
					sc.getInitParameter("password")
					);
			stmt = conn.createStatement();
			rs = stmt.executeQuery("SELECT MNO, MNAME, EMAIL, CRE_DATE"
					+ " FROM MEMBERS ORDER BY MNO ASC");
			
			resp.setContentType("text/html; charset=UTF-8");
			PrintWriter out = resp.getWriter();
			out.println("<html><head><title>회원목록</title></head>");
			out.println("<body><h1>회원목록</h1>");
			out.println("<p><a href='add'>신규 회원</a></p>");
			while(rs.next()) {
				out.println(
						rs.getInt("MNO") + ", " +
						"<a href='update?no=" +rs.getInt("MNO") +"'>" +
						rs.getString("MNAME") + "</a>, " +
						rs.getString("EMAIL") + ", " +
						rs.getDate("CRE_DATE") + 
						"<a href='delete?no=" + rs.getInt("MNO") + "'>[삭제]</a>" + "<br>"
						);
				
			}
			out.println("</body></html>");
		} catch (Exception e) {
			// TODO: handle exception
			throw new ServletException(e);
		} finally {
			try {
				if(rs != null) rs.close();
				if(stmt != null) rs.close();
				if(conn != null) rs.close();
			} catch(Exception e) {
				
			}
		}//finally
	}

}
```
## Add Servlet
```java
@WebServlet("/member/add")
public class MemberAddServlet extends HttpServlet {

	@Override
	protected void doGet(HttpServletRequest reqest, HttpServletResponse response) throws ServletException, IOException {
		response.setContentType("text/html; charset=UTF-8"); 
		PrintWriter out = response.getWriter();
		out.println("<html><head><title>회원등록</title></head>");
		out.println("<body><h1>회원 등록</h1>");
		out.println("<form action='add' method='post'>");
		out.println("이름: <input type='text' name='name'><br>");
		out.println("이메일: <input type='text' name='email'><br>");
		out.println("암호: <input type='password' name='password'><br>");
		out.println("<input type='submit' value='추가'>");
		out.println("<input type='reset' value='취소'>");
		out.println("</form>");
		out.println("</body></html>");
	}
	@Override
	protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
		request.setCharacterEncoding("UTF-8");
		Connection conn = null;
		PreparedStatement pstmt = null;
		String sql = "INSERT INTO MEMBERS(EMAIL, PWD, MNAME, CRE_DATE, MOD_DATE) VALUES (?, ?, ?, NOW(), NOW())";
		try {
			ServletContext sc = this.getServletContext();
			Class.forName(sc.getInitParameter("driver"));
			conn = DriverManager.getConnection(
					sc.getInitParameter("url"),
					sc.getInitParameter("username"),
					sc.getInitParameter("password"));
			pstmt = conn.prepareStatement(sql);
			pstmt.setString(1, request.getParameter("email"));
			pstmt.setString(2, request.getParameter("password"));
			pstmt.setString(3, request.getParameter("name"));
			pstmt.executeUpdate();
			
			response.sendRedirect("list");
			
			/* 리다이렉트는 HTML을 출력하지 않는다
			response.setContentType("text/html; charset=UTF-8");
			PrintWriter out = response.getWriter();
			
			out.println("<html><head><title>회원등록결과</title>");
			//out.println("<meta http-equiv='Refresh' content='1; url=list'>");
			out.println("</head>");
			out.println("<body>");
			out.println("<p>등록 성공입니다</p>");
			out.println("</body></html>");
			 */
			//리프레시 정보를 응답 헤더에 추가
			//response.addHeader("Refresh", "1;url=list");
			
			
		}catch (Exception e) {
			throw new ServletException(e);
		} finally {
			try {
				if(pstmt != null) pstmt.close();
				if(conn != null) conn.close();
			} catch (Exception e2) {
				// TODO: handle exception
			}
		}
	}
	
}
```

## Update Servlet
```java
//@WebServlet(
//		urlPatterns= {"/member/update"},
//		initParams= {
//				@WebInitParam(name="driver", value="org.mariadb.jdbc.Driver"),
//				@WebInitParam(name="url", value="jdbc:mariadb://localhost/studydb"),
//				@WebInitParam(name="username", value="root"),
//				@WebInitParam(name="password", value="root")
//				
//				
//		}
//		)
public class MemberUpdateServlet extends HttpServlet {

	@Override
	protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
		Connection conn = null;
		Statement stmt = null;
		ResultSet rs = null;
		
		try {
			ServletContext sc = this.getServletContext();
			Class.forName(sc.getInitParameter("driver"));
			conn = DriverManager.getConnection(
					sc.getInitParameter("url"),
					sc.getInitParameter("username"),
					sc.getInitParameter("password"));
			stmt = conn.createStatement();
			String sql = "select MNO, EMAIL, MNAME, CRE_DATE from MEMBERS where MNO=" +req.getParameter("no");
			
			rs = stmt.executeQuery(sql);
			if(rs.next()) {
				resp.setContentType("text/html; charset=UTF-8");
				PrintWriter out = resp.getWriter();
				out.println("<html><head><title>회원정보</title></head>");
				out.println("<body>");
				out.println("<h1>회원정보</h1>");
				out.println("<form action='update' method='post'>");
				out.println("번호: <input type='text' name='no' value='" + rs.getInt("mno") +"' readonly><br>");
				out.println("이름: <input type='text' name='name' value='" + rs.getString("MNAME") +"'><br>");
				out.println("이메일: <input type='text' name='email' value='" + rs.getString("EMAIL") +"'><br>");
				out.println("가입일: " + rs.getDate("CRE_DATE") + "<br>");
				out.println("<input type='submit' value='저장'>");
				out.println("<input type='button' value='삭제' onclick=" + "\'location.href=\"delete?no="+ rs.getInt("mno")  +"\"\'>");
				out.println("<input type='button' value='취소' onclick='location.href=\"list\"'>");
				out.println("</form>");
				out.println("</body></html>");
			}
		} catch (Exception e) {
			throw new ServletException(e);
		} finally {
			try {
				if(rs !=null) rs.close();
				if(stmt !=null) stmt.close();
				if(conn !=null) conn.close();
			} catch(Exception e2) {
				
			}
		}
	}

	@Override
	protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
		req.setCharacterEncoding("UTF-8");
		Connection conn = null;
		PreparedStatement pstmt = null;
		try {
			ServletContext sc = this.getServletContext();
			Class.forName(sc.getInitParameter("driver"));
			conn = DriverManager.getConnection(
					sc.getInitParameter("url"),
					sc.getInitParameter("username"),
					sc.getInitParameter("password"));
			String sql = "update MEMBERS set EMAIL=?, MNAME=?, MOD_DATE=now() WHERE MNO=?";
			pstmt = conn.prepareStatement(sql);
			pstmt.setString(1, req.getParameter("email"));
			pstmt.setString(2, req.getParameter("name"));
			pstmt.setInt(3, Integer.parseInt(req.getParameter("no")));
			pstmt.executeQuery();
			resp.sendRedirect("list");
		} catch (Exception e) {
			throw new ServletException();
		} finally {
			try {
				if(pstmt != null) pstmt.close();
				if(conn != null) conn.close();
			} catch (Exception e2) {
				// TODO: handle exception
			}
		}
	}

}

```

## Delete Servlet
```java
@WebServlet("/member/delete")
public class MemberDeleteServlet extends HttpServlet {
	@Override
	protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
		ServletContext sc = this.getServletContext();
		Connection conn = null;
		Statement stmt = null;
		String sql = "delete from MEMBERS where mno = " + req.getParameter("no");
		try {
			Class.forName(sc.getInitParameter("driver"));
			conn = DriverManager.getConnection(
					sc.getInitParameter("url"),
					sc.getInitParameter("username"),
					sc.getInitParameter("password"));
			stmt = conn.createStatement();
			stmt.executeUpdate(sql);
			
			//삭제후 이동
			resp.sendRedirect("list");
		}catch (Exception e) {
			throw  new ServletException();
		}finally {
			try {
				if(stmt != null) stmt.close();
				if(conn != null) conn.close();
			} catch (Exception e2) {
				e2.printStackTrace();
			}
		}
	}
}
```