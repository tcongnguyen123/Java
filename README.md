# Java
---------------- Day 4 ------------------------------
search servlet 
```package servlet;

import java.io.IOException;
import java.sql.SQLException;
import java.util.List;
import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import javax.servlet.http.HttpSession;
import dao.searchDao;
import model.search;

@WebServlet("/search")
public class searchServlet extends HttpServlet {
    private static final long serialVersionUID = 1L;

    public searchServlet() {
        super();
    }

    @Override
    protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
//        HttpSession session = request.getSession(false); // Lấy session hiện tại, không tạo mới
//        if (session == null || session.getAttribute("username") == null) {
//            // Nếu chưa đăng nhập, chuyển hướng về trang đăng nhập
//            response.sendRedirect("login");
//            return;
//        }
    	
    	processRequest(request, response);
    }

    @Override
    protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        processRequest(request, response);
    }

    private void processRequest(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        searchDao customerDAO = new searchDao();
        HttpSession session = request.getSession();

        // Lấy các tham số từ request
        String action = request.getParameter("action");
        String customerName = request.getParameter("txtCustomerName");
        String sex = request.getParameter("cboSex");
        String birthdayFrom = request.getParameter("fromBirthday");
        String birthdayTo = request.getParameter("toBirthday");
        
        String datePattern = "^\\d{4}/\\d{2}/\\d{2}$"; // regex cho định dạng YYYY/MM/DD

        if (birthdayFrom != null && !birthdayFrom.isEmpty() && !birthdayFrom.matches(datePattern)) {
            request.setAttribute("errorMessage", "Invalid 'From Birthday' format. Please use YYYY/MM/DD.");

            // Load lại thông tin tìm kiếm từ session
            customerName = (String) session.getAttribute("txtCustomerName");
            sex = (String) session.getAttribute("cboSex");
            birthdayFrom = (String) session.getAttribute("fromBirthday");
            birthdayTo = (String) session.getAttribute("toBirthday");

            // Lấy lại danh sách khách hàng từ DB (theo thông tin tìm kiếm trước đó, nếu có)
            List<search> customerList;
			try {
				customerList = customerDAO.searchCustomers(customerName, sex, birthdayFrom, birthdayTo);
	            // Đưa dữ liệu khách hàng vào request để hiển thị lại bảng
	            request.setAttribute("customerList", customerList);
	            request.getRequestDispatcher("/WEB-INF/jsp/search.jsp").forward(request, response);
	            return;
			} catch (SQLException e) {
				// TODO Auto-generated catch block
				e.printStackTrace();
			}


        }

        // Nếu định dạng 'To Birthday' không đúng, tương tự như trên
        if (birthdayTo != null && !birthdayTo.isEmpty() && !birthdayTo.matches(datePattern)) {
            request.setAttribute("errorMessage", "Invalid 'To Birthday' format. Please use YYYY/MM/DD.");

            // Load lại thông tin tìm kiếm từ session
            customerName = (String) session.getAttribute("txtCustomerName");
            sex = (String) session.getAttribute("cboSex");
            birthdayFrom = (String) session.getAttribute("fromBirthday");
            birthdayTo = (String) session.getAttribute("toBirthday");

            // Lấy lại danh sách khách hàng từ DB
            List<search> customerList;
			try {
				customerList = customerDAO.searchCustomers(customerName, sex, birthdayFrom, birthdayTo);
	            request.setAttribute("customerList", customerList);
	            request.getRequestDispatcher("/WEB-INF/jsp/search.jsp").forward(request, response);
	            return;
			} catch (SQLException e) {
				// TODO Auto-generated catch block
				e.printStackTrace();
			}

            // Đưa dữ liệu khách hàng vào request để hiển thị lại bảng

        }
        if ("logout".equals(action)) {
            // Xử lý logout
            session.invalidate(); // Hủy session
            response.sendRedirect("login"); // Chuyển hướng đến trang đăng nhập
            return;
        }
        // Lưu thông tin tìm kiếm vào session
        if ("delete".equals(action)) {
            String[] deleteIds = request.getParameterValues("deleteIds");
            if (deleteIds != null) {
                try {
                    for (String id : deleteIds) {
                        customerDAO.deleteCustomerById(id);
                        System.out.println("Deleted customer with ID: " + id);
                    }
                } catch (SQLException e) {
                    e.printStackTrace();
                }
            }

            // Sau khi xóa, lấy thông tin tìm kiếm từ session
            customerName = (String) session.getAttribute("txtCustomerName");
            sex = (String) session.getAttribute("cboSex");
            birthdayFrom = (String) session.getAttribute("fromBirthday");
            birthdayTo = (String) session.getAttribute("toBirthday");
        } else if ("addNew".equals(action)) {
            // Chuyển đến trang chỉnh sửa
            response.sendRedirect("edit"); // Chuyển hướng đến servlet Edit
        }
        else {
            // Lưu thông tin tìm kiếm vào session
            session.setAttribute("txtCustomerName", customerName);
            session.setAttribute("cboSex", sex);
            session.setAttribute("fromBirthday", birthdayFrom);
            session.setAttribute("toBirthday", birthdayTo);

        }
        System.out.println("day la " + action);
     // Xử lý phân trang
        String pageStr = request.getParameter("page");
        Integer currentPage = (Integer) session.getAttribute("currentPage");
        if (pageStr != null) {
            currentPage = Integer.parseInt(pageStr);
        }
        if (currentPage == null || currentPage < 1 || "search".equals(action)) {
            currentPage = 1;
        }
        int recordsPerPage = 3;

        try {
            List<search> customerList;
            if (customerName != null || sex != null || birthdayFrom != null || birthdayTo != null) {
                customerList = customerDAO.searchCustomers(customerName, sex, birthdayFrom, birthdayTo);
            } else {	
                customerList = customerDAO.getAllCustomers();
            }

            // Đảm bảo số trang hiện tại không vượt quá tổng số trang
            int totalRecords = customerList.size();
            int totalPages = (int) Math.ceil((double) totalRecords / recordsPerPage);
            currentPage = Math.min(currentPage, totalPages); // Điều chỉnh số trang nếu cần

            // Tính toán giá trị start và end cho phân trang
            int start = (currentPage - 1) * recordsPerPage;
            int end = Math.min(start + recordsPerPage, totalRecords);

            // Đảm bảo start không âm và end không vượt quá kích thước danh sách
            start = Math.max(start, 0);
            end = Math.min(end, totalRecords);

            List<search> pageCustomerList = customerList.subList(start, end);

            // Lưu trang hiện tại vào session
            session.setAttribute("currentPage", currentPage);

            // Đưa các thuộc tính vào request để forward
            request.setAttribute("customerList", pageCustomerList);
            request.setAttribute("totalPages", totalPages);
            request.setAttribute("currentPage", currentPage);
            request.setAttribute("txtCustomerName", customerName);
            request.setAttribute("cboSex", sex);
            request.setAttribute("fromBirthday", birthdayFrom);
            request.setAttribute("toBirthday", birthdayTo);
            request.setAttribute("noData", totalRecords == 0);
            request.getRequestDispatcher("/WEB-INF/jsp/search.jsp").forward(request, response);
        } catch (SQLException e) {
            e.printStackTrace();
        }

    }
}```

edit servlet 
``` package servlet;

import java.io.IOException;
import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

import dao.editDao;
import model.search;

// Giả sử bạn đã có một DAO để tương tác với cơ sở dữ liệu


@WebServlet("/edit")
public class editServlet extends HttpServlet {
    private static final long serialVersionUID = 1L;

    @Override
    protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        String customerId = request.getParameter("id");
        if (customerId != null) {
            // Lấy thông tin khách hàng từ cơ sở dữ liệu
            editDao customerDao = new editDao();
            search customer = customerDao.getCustomerById(Integer.parseInt(customerId));
            request.setAttribute("customer", customer);
        }
        request.getRequestDispatcher("/WEB-INF/jsp/edit.jsp").forward(request, response);
    }

    @Override
    protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        // Xử lý dữ liệu từ form thêm mới
        // Ví dụ: Lưu thông tin khách hàng vào cơ sở dữ liệu
    }
} ```

login servlet 

```package servlet;

import java.io.IOException;
import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import javax.servlet.http.HttpSession;

import dao.loginDao;

/**
 * Servlet implementation class loginServlet
 */
@WebServlet("/login")
public class loginServlet extends HttpServlet {
	private static final long serialVersionUID = 1L;
       
    /**
     * @see HttpServlet#HttpServlet()
     */
    public loginServlet() {
        super();
        // TODO Auto-generated constructor stub
    }

	/**
	 * @see HttpServlet#doGet(HttpServletRequest request, HttpServletResponse response)
	 */
	protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
		// TODO Auto-generated method stub
		
		request.getRequestDispatcher("/WEB-INF/jsp/login.jsp").forward(request, response);
	}

	/**
	 * @see HttpServlet#doPost(HttpServletRequest request, HttpServletResponse response)
	 */
	protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
		// TODO Auto-generated method stub
		// Lấy thông tin username và password từ màn hình login
		String username = request.getParameter("username");
		String password = request.getParameter("password");
		if (username == null || username.trim().isEmpty()) {
			request.setAttribute("errorMessage", "Chưa nhập user name");
			request.getRequestDispatcher("/WEB-INF/jsp/login.jsp").forward(request, response);
		} else {
			// Tạo đối tượng LoginDao để kiểm tra đăng nhập
			loginDao loginDao = new loginDao();
			int count = loginDao.checkLogin(username, password);
			
			// Kiểm tra kết quả đăng nhập
			if (count == 1) {
				// Nếu đúng thông tin, chuyển hướng người dùng đến trang chính (trang dashboard)
				
	            HttpSession session = request.getSession();
	            session.setAttribute("username", username);
				response.sendRedirect("/CustomerSystem/search");
			} else {
				// Nếu sai thông tin, thông báo lỗi và quay lại trang login
				request.setAttribute("errorMessage", "Thông tin đăng nhập sai. Vui lòng kiểm tra lại.");
				request.getRequestDispatcher("/WEB-INF/jsp/login.jsp").forward(request, response);
			}
		}

	}
}
```

edit dao 
```
package dao;

import model.search;
import java.sql.*;

import database.connectDB;

public class editDao {
    // Phương thức lấy thông tin khách hàng theo ID
    public search getCustomerById(int id) {
        search customer = null;
        // Kết nối và truy vấn cơ sở dữ liệu
        try (Connection conn = connectDB.getConnection(); 
             PreparedStatement stmt = conn.prepareStatement("SELECT * FROM mstcustomer WHERE id = ?")) {
            stmt.setInt(1, id);
            ResultSet rs = stmt.executeQuery();
            if (rs.next()) {
                customer = new search();
                customer.setId(rs.getInt("id"));
                customer.setCustomerName(rs.getString("customerName"));
                customer.setSex(rs.getString("sex"));
                customer.setBirthday(rs.getString("birthday"));
                customer.setAddress(rs.getString("address"));
            }
        } catch (SQLException e) {
            e.printStackTrace();
        }
        return customer;
    }
}```

search.jsp 
```
	<%@ page language="java" contentType="text/html; charset=UTF-8"
		pageEncoding="UTF-8"%>
	<%@ taglib uri="http://java.sun.com/jsp/jstl/core" prefix="c"%>
	<!DOCTYPE html>
	<html>
	<head>
	<meta charset="UTF-8">
	<title>Insert title here</title>
	<style type="text/css">
	      body {
	        overflow-x: hidden;
	        font-family: Arial, sans-serif;
	        background-color: rgba(0, 255, 255, 0.336);
	      }
	      table {
	        width: 100%;
	        border-collapse: collapse;
	      }
	      th,
	      td {
	        padding: 8px;
	        text-align: left;
	        border-bottom: 1px solid #ddd;
	      }
	      th {
	        background-color: #f2f2f2;
	      }
	            tr:first-child{
	        > th{
	            background-color: rgb(0, 223, 0);
	        }
	      }
	      tr:nth-child(even) {
	        background-color: #f2f2f2;
	      }
	      tr:hover {
	        background-color: #f5f5f5;
	      }
	      .breadcrumb {
	        padding-block: 7px;
	        margin-top: 10px;
	      }
	      .welcome-user-container {
	        display: flex;
	        align-items: center;
	        justify-content: space-between;
	        padding-top: 10px;
	      }
	      .divider {
	        height: 2cap;
	        width: 100%;
	        background-color: blue;
	      }
	      .search {
	        display: flex;
	        justify-content: space-around;
	        align-items: center;
	        padding: 10px;
	        margin-top: 50px;
	        background-color: rgb(255, 255, 45);
	      }
	      .birthday {
	        width: 50px;
	      }
	            .pagination{
	        display: flex;
	        flex-direction: row;
	       justify-content: space-between;
	         div {
	            display: flex;
	            flex-direction: row;
	            align-items: center;
	           > p {
	            padding-inline: 10px;
	           }  
	
	       }
	      }
	</style>
	<script>
	function selectAllCheckboxes(source) {
	    var checkboxes = document.querySelectorAll('input[name="deleteIds"]');
	    for (var i = 0; i < checkboxes.length; i++) {
	        checkboxes[i].checked = source.checked;
	    }
	}
	document.getElementById("addNew").addEventListener("click", function() {
	    window.location.href = 'edit'; // Chuyển hướng đến servlet Edit
	});
	</script>
	</head>
	<body>
		    <div class="breadcrumb"><a href="index.html">Homee</a> > {{page-name}}</div>
	<div class="welcome-user-container">
	    <p>Welcome, ${user}</p>
	    <form action="search" method="post" style="display: inline;">
	        <input type="hidden" name="action" value="logout" />
	        <button type="submit">Log Out</button>
	    </form>
	</div>
	    <div class="divider"></div>
		<form class="search" action="search" method="post">
		<input type="hidden" name="action" value="search" />
		  <!-- customer name -->
		  <div>
		    <label for="search">Customer Name:</label>
		    <input type="text" name="txtCustomerName" value="${txtCustomerName != null ? txtCustomerName : ''}" />
		  </div>
		  <!-- sex -->
		<!-- sex -->
		<div>
		  <label for="search">Sex:</label>
		  <select name="cboSex">
		    <option value="0" ${cboSex == null || cboSex == '0' ? 'selected' : ''}></option>
		    <option value="M" ${cboSex == 'M' ? 'selected' : ''}>Male</option>
		    <option value="F" ${cboSex == 'F' ? 'selected' : ''}>Female</option>
		  </select>
		</div>
	
		  <!-- birthday -->
		  <div style="display: flex; justify-content: center; align-items: center;">
		    <label for="search">Birthday:</label>
		    <input class="birthday" type="text" name="fromBirthday" value="${fromBirthday != null ? fromBirthday : ''}" />
		    <p>~</p>
		    <input class="birthday" type="text" name="toBirthday" value="${toBirthday != null ? toBirthday : ''}" />
		  </div>
		  <button id="searchButton" type="submit" name="action" value="search">Search</button>   
		</form>
	
	<div class="pagination">
	    <form action="search" method="post">
	        <input type="hidden" name="txtCustomerName" value="${txtCustomerName != null ? txtCustomerName : ''}" />
	        <input type="hidden" name="cboSex" value="${cboSex != null ? cboSex : ''}" />
			<input type="hidden" name="fromBirthday" value="${fromBirthday != null ? fromBirthday : ''}" />
			<input type="hidden" name="toBirthday" value="${toBirthday != null ? toBirthday : ''}" />
	        
	        <!-- First Page Button -->
	        <c:choose>
	            <c:when test="${currentPage > 1}">
	                <button type="submit" name="page" value="1">&laquo;</button>
	            </c:when>
	            <c:otherwise>
	                <button type="button" disabled>&laquo;</button>
	            </c:otherwise>
	        </c:choose>
	
	        <!-- Previous Page Button -->
	        <c:choose>
	            <c:when test="${currentPage > 1}">
	                <button type="submit" name="page" value="${currentPage - 1}">&lt;</button>
	            </c:when>
	            <c:otherwise>
	                <button type="button" disabled>&lt;</button>
	            </c:otherwise>
	        </c:choose>
	
	        <!-- Current Page Display -->
	        <span>Page ${currentPage} of ${totalPages}</span>
	
	        <!-- Next Page Button -->
	        <c:choose>
	            <c:when test="${currentPage < totalPages}">
	                <button type="submit" name="page" value="${currentPage + 1}">&gt;</button>
	            </c:when>
	            <c:otherwise>
	                <button type="button" disabled>&gt;</button>
	            </c:otherwise>
	        </c:choose>
	
	        <!-- Last Page Button -->
	        <c:choose>
	            <c:when test="${currentPage < totalPages}">
	                <button type="submit" name="page" value="${totalPages}">&raquo;</button>
	            </c:when>
	            <c:otherwise>
	                <button type="button" disabled>&raquo;</button>
	            </c:otherwise>
	        </c:choose>
	    </form>
	</div>
	
	<form id="delete-form" action="search" method="post">
	    <input type="hidden" name="action" value="delete" />
	
		<table style="border: 2px solid black;">
		  <tr>
		    <th>
		     <!-- select all -->
		     <input type="checkbox" id="select-all" onclick="selectAllCheckboxes(this)">
		    </th>
		    <th>Customer ID</th>
		    <th>Customer Name</th>
		    <th>Sex</th>
		    <th>Birthday</th>
		    <th>Address</th>
		  </tr>
		  <c:forEach var="customer" items="${customerList}">
		    <tr>
		        <td>
		            <input type="checkbox" name="deleteIds" value="${customer.id}">
		        </td>
		      <!-- Customer ID as a link -->
		      <td><a href="edit?id=${customer.id}">${customer.id}</a></td>
		      <td>${customer.customerName}</td>
		      <td>
		      	<c:choose>
		        	<c:when test="${customer.sex == 'M'}">
		            	Male
		            </c:when>
		            <c:otherwise>
		            	Female
		            </c:otherwise>
		         </c:choose>
		      </td>
		      <td>${customer.birthday}</td>
		      <td>${customer.address}</td>
		    </tr>
		  </c:forEach>
		</table>
		<c:if test="${noData}">
	    <script>
	        // Disable delete button if no data is present
	        document.addEventListener("DOMContentLoaded", function() {
	            document.getElementById("deleteButton").disabled = true;
	        });
	    </script>
		</c:if>
	    <div style="padding-block: 20px; display: flex; gap: 20px">
	        <button id="addNew" type="button" onclick="location.href='edit'">Add New</button>   
	        <button id="deleteButton" type="submit" name="action" value="delete">Delete</button>    
	    </div>
	</form>
		<c:if test="${not empty errorMessage}">
		    <div style="color: red;">${errorMessage}</div>
		</c:if>
	</body>
	</html>
	
	edit.jsp 
	
	<%@ page language="java" contentType="text/html; charset=UTF-8"
	    pageEncoding="UTF-8"%>
	<%@ taglib uri="http://java.sun.com/jsp/jstl/core" prefix="c"%>
	<!DOCTYPE html>
	<html>
	<head>
	    <meta charset="UTF-8">
	    <title>Edit Customer</title>
	</head>
	<body>
	    <h1>Edit Customer</h1>
	    <form action="update" method="post">
	        <div>
	            <label for="id">Customer ID:</label>
	            <input type="text" id="id" name="id" value="${customer.id}" readonly />
	        </div>
	        <div>
	            <label for="name">Customer Name:</label>
	            <input type="text" name="name" value="${customer.customerName}" required />
	        </div>
	        <div>
	            <label for="sex">Sex:</label>
	            <select name="sex">
	                <option value="M" ${customer.sex == 'M' ? 'selected' : ''}>Male</option>
	                <option value="F" ${customer.sex == 'F' ? 'selected' : ''}>Female</option>
	            </select>
	        </div>
	        <div>
	            <label for="birthday">Birthday:</label>
	            <input type="text" name="birthday" value="${customer.birthday}" required />
	        </div>
	        <div>
	            <label for="address">Address:</label>
	            <input type="text" name="address" value="${customer.address}" required />
	        </div>
	        <button type="submit">Update</button>
	    </form>
	</body>
	</html>

```



















---------------- Day 3 ------------------------------
```
search servlet

package servlet;

import java.io.IOException;
import java.sql.SQLException;
import java.util.List;
import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import javax.servlet.http.HttpSession;
import dao.searchDao;
import model.search;

@WebServlet("/search")
public class searchServlet extends HttpServlet {
    private static final long serialVersionUID = 1L;

    public searchServlet() {
        super();
    }

    @Override
    protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        processRequest(request, response);
    }

    @Override
    protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        processRequest(request, response);
    }

    private void processRequest(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        searchDao customerDAO = new searchDao();
        HttpSession session = request.getSession();

        // Lấy các tham số từ request
        String action = request.getParameter("action");
        String customerName = request.getParameter("txtCustomerName");
        String sex = request.getParameter("cboSex");
        String birthdayFrom = request.getParameter("fromBirthday");
        String birthdayTo = request.getParameter("toBirthday");
        
        // Lưu thông tin tìm kiếm vào session
        if ("delete".equals(action)) {
            String[] deleteIds = request.getParameterValues("deleteIds");
            if (deleteIds != null) {
                try {
                    for (String id : deleteIds) {
                        customerDAO.deleteCustomerById(id);
                        System.out.println("Deleted customer with ID: " + id);
                    }
                } catch (SQLException e) {
                    e.printStackTrace();
                }
            }

            // Sau khi xóa, lấy thông tin tìm kiếm từ session
            customerName = (String) session.getAttribute("txtCustomerName");
            sex = (String) session.getAttribute("cboSex");
            birthdayFrom = (String) session.getAttribute("fromBirthday");
            birthdayTo = (String) session.getAttribute("toBirthday");
        } else if ("addNew".equals(action)) {
            // Chuyển đến trang chỉnh sửa
            response.sendRedirect("edit"); // Chuyển hướng đến servlet Edit
        }
        else {
            // Lưu thông tin tìm kiếm vào session
            session.setAttribute("txtCustomerName", customerName);
            session.setAttribute("cboSex", sex);
            session.setAttribute("fromBirthday", birthdayFrom);
            session.setAttribute("toBirthday", birthdayTo);
        }
        System.out.println("day la " + action);
     // Xử lý phân trang
        String pageStr = request.getParameter("page");
        Integer currentPage = (Integer) session.getAttribute("currentPage");
        if (pageStr != null) {
            currentPage = Integer.parseInt(pageStr);
        }
        if (currentPage == null || currentPage < 1) {
            currentPage = 1;
        }
        int recordsPerPage = 3;

        try {
            List<search> customerList;
            if (customerName != null || sex != null || birthdayFrom != null || birthdayTo != null) {
                customerList = customerDAO.searchCustomers(customerName, sex, birthdayFrom, birthdayTo);
            } else {
                customerList = customerDAO.getAllCustomers();
            }

            // Đảm bảo số trang hiện tại không vượt quá tổng số trang
            int totalRecords = customerList.size();
            int totalPages = (int) Math.ceil((double) totalRecords / recordsPerPage);
            currentPage = Math.min(currentPage, totalPages); // Điều chỉnh số trang nếu cần

            // Tính toán giá trị start và end cho phân trang
            int start = (currentPage - 1) * recordsPerPage;
            int end = Math.min(start + recordsPerPage, totalRecords);

            // Đảm bảo start không âm và end không vượt quá kích thước danh sách
            start = Math.max(start, 0);
            end = Math.min(end, totalRecords);

            List<search> pageCustomerList = customerList.subList(start, end);

            // Lưu trang hiện tại vào session
            session.setAttribute("currentPage", currentPage);

            // Đưa các thuộc tính vào request để forward
            request.setAttribute("customerList", pageCustomerList);
            request.setAttribute("totalPages", totalPages);
            request.setAttribute("currentPage", currentPage);
            request.setAttribute("txtCustomerName", customerName);
            request.setAttribute("cboSex", sex);
            request.setAttribute("fromBirthday", birthdayFrom);
            request.setAttribute("toBirthday", birthdayTo);
            request.setAttribute("noData", totalRecords == 0);
            request.getRequestDispatcher("/WEB-INF/jsp/search.jsp").forward(request, response);
        } catch (SQLException e) {
            e.printStackTrace();
        }

    }
}

```
```
Search jsp

<%@ page language="java" contentType="text/html; charset=UTF-8"
	pageEncoding="UTF-8"%>
<%@ taglib uri="http://java.sun.com/jsp/jstl/core" prefix="c"%>
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>Insert title here</title>
<style type="text/css">
      body {
        overflow-x: hidden;
        font-family: Arial, sans-serif;
        background-color: rgba(0, 255, 255, 0.336);
      }
      table {
        width: 100%;
        border-collapse: collapse;
      }
      th,
      td {
        padding: 8px;
        text-align: left;
        border-bottom: 1px solid #ddd;
      }
      th {
        background-color: #f2f2f2;
      }
            tr:first-child{
        > th{
            background-color: rgb(0, 223, 0);
        }
      }
      tr:nth-child(even) {
        background-color: #f2f2f2;
      }
      tr:hover {
        background-color: #f5f5f5;
      }
      .breadcrumb {
        padding-block: 7px;
        margin-top: 10px;
      }
      .welcome-user-container {
        display: flex;
        align-items: center;
        justify-content: space-between;
        padding-top: 10px;
      }
      .divider {
        height: 2cap;
        width: 100%;
        background-color: blue;
      }
      .search {
        display: flex;
        justify-content: space-around;
        align-items: center;
        padding: 10px;
        margin-top: 50px;
        background-color: rgb(255, 255, 45);
      }
      .birthday {
        width: 50px;
      }
            .pagination{
        display: flex;
        flex-direction: row;
       justify-content: space-between;
         div {
            display: flex;
            flex-direction: row;
            align-items: center;
           > p {
            padding-inline: 10px;
           }  

       }
      }
</style>
<script>
function selectAllCheckboxes(source) {
    var checkboxes = document.querySelectorAll('input[name="deleteIds"]');
    for (var i = 0; i < checkboxes.length; i++) {
        checkboxes[i].checked = source.checked;
    }
}
document.getElementById("addNew").addEventListener("click", function() {
    window.location.href = 'edit'; // Chuyển hướng đến servlet Edit
});
</script>
</head>
<body>
	    <div class="breadcrumb"><a href="index.html">Homee</a> > {{page-name}}</div>
    <div class="welcome-user-container">
      <p>Welcome, ${user}</p>
      <a href="${logouturl}">Log Out</a>
    </div>
    <div class="divider"></div>
	<form class="search" action="search" method="post">
	  <!-- customer name -->
	  <div>
	    <label for="search">Customer Name:</label>
	    <input type="text" name="txtCustomerName" value="${txtCustomerName != null ? txtCustomerName : ''}" />
	  </div>
	  <!-- sex -->
	<!-- sex -->
	<div>
	  <label for="search">Sex:</label>
	  <select name="cboSex">
	    <option value="0" ${cboSex == null || cboSex == '0' ? 'selected' : ''}></option>
	    <option value="M" ${cboSex == 'M' ? 'selected' : ''}>Male</option>
	    <option value="F" ${cboSex == 'F' ? 'selected' : ''}>Female</option>
	  </select>
	</div>

	  <!-- birthday -->
	  <div style="display: flex; justify-content: center; align-items: center;">
	    <label for="search">Birthday:</label>
	    <input class="birthday" type="text" name="fromBirthday" value="${fromBirthday != null ? fromBirthday : ''}" />
	    <p>~</p>
	    <input class="birthday" type="text" name="toBirthday" value="${toBirthday != null ? toBirthday : ''}" />
	  </div>
	  <button type="submit">Search</button>
	</form>

<div class="pagination">
    <form action="search" method="post">
        <input type="hidden" name="txtCustomerName" value="${txtCustomerName != null ? txtCustomerName : ''}" />
        <input type="hidden" name="cboSex" value="${cboSex != null ? cboSex : ''}" />
		<input type="hidden" name="fromBirthday" value="${fromBirthday != null ? fromBirthday : ''}" />
		<input type="hidden" name="toBirthday" value="${toBirthday != null ? toBirthday : ''}" />
        
        <!-- First Page Button -->
        <c:choose>
            <c:when test="${currentPage > 1}">
                <button type="submit" name="page" value="1">&laquo;</button>
            </c:when>
            <c:otherwise>
                <button type="button" disabled>&laquo;</button>
            </c:otherwise>
        </c:choose>

        <!-- Previous Page Button -->
        <c:choose>
            <c:when test="${currentPage > 1}">
                <button type="submit" name="page" value="${currentPage - 1}">&lt;</button>
            </c:when>
            <c:otherwise>
                <button type="button" disabled>&lt;</button>
            </c:otherwise>
        </c:choose>

        <!-- Current Page Display -->
        <span>Page ${currentPage} of ${totalPages}</span>

        <!-- Next Page Button -->
        <c:choose>
            <c:when test="${currentPage < totalPages}">
                <button type="submit" name="page" value="${currentPage + 1}">&gt;</button>
            </c:when>
            <c:otherwise>
                <button type="button" disabled>&gt;</button>
            </c:otherwise>
        </c:choose>

        <!-- Last Page Button -->
        <c:choose>
            <c:when test="${currentPage < totalPages}">
                <button type="submit" name="page" value="${totalPages}">&raquo;</button>
            </c:when>
            <c:otherwise>
                <button type="button" disabled>&raquo;</button>
            </c:otherwise>
        </c:choose>
    </form>
</div>
<form id="delete-form" action="search" method="post">
    <input type="hidden" name="action" value="delete" />

	<table style="border: 2px solid black;">
	  <tr>
	    <th>
	     <!-- select all -->
	     <input type="checkbox" id="select-all" onclick="selectAllCheckboxes(this)">
	    </th>
	    <th>Customer ID</th>
	    <th>Customer Name</th>
	    <th>Sex</th>
	    <th>Birthday</th>
	    <th>Address</th>
	  </tr>
	  <c:forEach var="customer" items="${customerList}">
	    <tr>
	        <td>
	            <input type="checkbox" name="deleteIds" value="${customer.id}">
	        </td>
	      <!-- Customer ID as a link -->
	      <td><a href="edit?id=${customer.id}">${customer.id}</a></td>
	      <td>${customer.customerName}</td>
	      <td>
	      	<c:choose>
	        	<c:when test="${customer.sex == 'M'}">
	            	Male
	            </c:when>
	            <c:otherwise>
	            	Female
	            </c:otherwise>
	         </c:choose>
	      </td>
	      <td>${customer.birthday}</td>
	      <td>${customer.address}</td>
	    </tr>
	  </c:forEach>
	</table>
	<c:if test="${noData}">
    <script>
        // Disable delete button if no data is present
        document.addEventListener("DOMContentLoaded", function() {
            document.getElementById("deleteButton").disabled = true;
        });
    </script>
	</c:if>
    <div style="padding-block: 20px; display: flex; gap: 20px">
        <button id="addNew" type="button" onclick="location.href='edit'">Add New</button>   
        <button id="deleteButton" type="submit" name="action" value="delete">Delete</button>    
    </div>
</form>
	
</body>
</html>
```
```
Seacrh dao
package dao;

import java.sql.Connection;
import java.sql.PreparedStatement;
import java.sql.ResultSet;
import java.sql.SQLException;
import java.util.ArrayList;
import java.util.List;

import database.connectDB;
import model.search;

public class searchDao {
    public List<search> getAllCustomers() throws SQLException {
        List<search> customers = new ArrayList<>();
        
        // Câu lệnh SQL để truy vấn tất cả khách hàng
        String query = "SELECT id, customerName, sex,birthday, address FROM mstcustomer WHERE delete_ymd is null";
        
        try (Connection con = connectDB.getConnection(); // Lấy kết nối
             PreparedStatement ps = con.prepareStatement(query); // Chuẩn bị câu lệnh
             ResultSet rs = ps.executeQuery()) { // Thực thi truy vấn
             
            // Lặp qua ResultSet và thêm các đối tượng Customer vào danh sách
            while (rs.next()) {
                int customerId = rs.getInt("id");
                String customerName = rs.getString("customerName");
                String sex = rs.getString("sex");
                String birthday = rs.getString("birthday");
                String address = rs.getString("address");
                
                // Tạo đối tượng Customer và thêm vào danh sách
                search customer = new search(customerId, customerName, sex,birthday, address);
                customers.add(customer);
            }
        } catch (SQLException e) {
            e.printStackTrace();
            throw e; // Ném ngoại lệ để xử lý ở tầng trên
        }
        
        return customers; // Trả về danh sách khách hàng
    }
    public List<search> searchCustomers(String customerName, String sex, String birthdayFrom, String birthdayTo) throws SQLException {
        List<search> customers = new ArrayList<>();
        
        // Câu lệnh SQL cơ bản
        StringBuilder query = new StringBuilder("SELECT id, customerName, sex, birthday, address FROM mstcustomer WHERE delete_ymd is null and 1=1");
        
        // Thêm điều kiện tìm kiếm nếu các tham số không null hoặc không rỗng
        if (customerName != null && !customerName.trim().isEmpty()) {
            query.append(" AND customerName LIKE ?");
        }
        if (sex != null && !sex.trim().isEmpty() && !sex.equals("0")) {
            query.append(" AND sex = ?");
        }
        if (birthdayFrom != null && !birthdayFrom.trim().isEmpty()) {
            query.append(" AND birthday >= ?");
        }
        if (birthdayTo != null && !birthdayTo.trim().isEmpty()) {
            query.append(" AND birthday <= ?");
        }
        System.out.println(query);
        try (Connection con = connectDB.getConnection(); // Lấy kết nối
             PreparedStatement ps = con.prepareStatement(query.toString())) { // Chuẩn bị câu lệnh
            
            int paramIndex = 1;
            
            // Gán giá trị cho các tham số của câu lệnh PreparedStatement
            if (customerName != null && !customerName.trim().isEmpty()) {
                ps.setString(paramIndex++, "%" + customerName + "%"); // Tìm kiếm theo chuỗi tương tự
            }
            if (sex != null && !sex.trim().isEmpty() && !sex.equals("0")) {
                ps.setString(paramIndex++, sex); // So sánh trực tiếp
            }
            if (birthdayFrom != null && !birthdayFrom.trim().isEmpty()) {
                ps.setString(paramIndex++, birthdayFrom); // Tìm kiếm theo ngày từ
            }
            if (birthdayTo != null && !birthdayTo.trim().isEmpty()) {
                ps.setString(paramIndex++, birthdayTo); // Tìm kiếm theo ngày đến
            }
            
            try (ResultSet rs = ps.executeQuery()) { // Thực thi truy vấn
                // Lặp qua ResultSet và thêm các đối tượng Customer vào danh sách
                while (rs.next()) {
                    int customerId = rs.getInt("id");
                    String name = rs.getString("customerName");
                    String gender = rs.getString("sex");
                    String birthday = rs.getString("birthday");
                    String address = rs.getString("address");
                    
                    // Tạo đối tượng Customer và thêm vào danh sách
                    search customer = new search(customerId, name, gender, birthday, address);
                    customers.add(customer);
                }
            }
        } catch (SQLException e) {
            e.printStackTrace();
            throw e; // Ném ngoại lệ để xử lý ở tầng trên
        }
        
        return customers; // Trả về danh sách khách hàng
    }
    public boolean deleteCustomerById(String customerId) throws SQLException {
        // Kết nối với cơ sở dữ liệu
        Connection conn = null;
        PreparedStatement stmt = null;
        boolean rowUpdated = false;

        try {
            // Lấy kết nối từ lớp DatabaseConnection
            conn = connectDB.getConnection();

            // Câu query để cập nhật delete_ymd bằng ngày giờ hiện tại
            String sql = "UPDATE mstcustomer SET delete_ymd = CURRENT_TIMESTAMP WHERE id = ?";

            // Tạo PreparedStatement
            stmt = conn.prepareStatement(sql);

            // Gán giá trị cho tham số customer_id
            stmt.setString(1, customerId);

            // Thực thi câu lệnh SQL và trả về số hàng bị ảnh hưởng
            rowUpdated = stmt.executeUpdate() > 0;
        } catch (SQLException e) {
            e.printStackTrace();
            throw new SQLException("Lỗi khi thực hiện xóa khách hàng: " + e.getMessage());
        } finally {
            // Đóng PreparedStatement và Connection
            if (stmt != null) {
                stmt.close();
            }
            if (conn != null) {
                conn.close();
            }
        }

        // Trả về true nếu có hàng được cập nhật, false nếu không có
        return rowUpdated;
    }

}

```


---------------- Day 2 ------------------------------
```
	<div class="pagination">
	    <c:choose>
	        <c:when test="${currentPage > 1}">
	            <a href="search?page=1">&laquo;</a>
	        </c:when>
	        <c:otherwise>
	            <span>&laquo;</span>
	        </c:otherwise>
	    </c:choose>
	
	    <c:choose>
	      
	        <c:when test="${currentPage > 1}">
	            <a href="search?page=${currentPage - 1}">&lt;</a>
	        </c:when>
	        <c:otherwise>
	            <span>&lt;</span>
	        </c:otherwise>
	    </c:choose>
	    <span>Page ${currentPage} of ${totalPages}</span>
	
	    <c:choose>
	        <c:when test="${currentPage < totalPages}">
	            <a href="search?page=${currentPage + 1}">&gt;</a>
	        </c:when>
	        <c:otherwise>
	            <span>&gt;</span>
	        </c:otherwise>
	    </c:choose>
	
	    <c:choose>
	        <c:when test="${currentPage < totalPages}">
	            <a href="search?page=${totalPages}">&raquo;</a>
	        </c:when>
	        <c:otherwise>
	            <span>&raquo;</span>
	        </c:otherwise>
	    </c:choose>
	</div>
```
```
    protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        searchDao customerDAO = new searchDao();

        // Lấy giá trị phân trang
        String pageStr = request.getParameter("page");
        int currentPage = (pageStr != null) ? Integer.parseInt(pageStr) : 1;
        int recordsPerPage = 5;

        try {
            // Lấy toàn bộ danh sách khách hàng
            List<search> customerList = customerDAO.getAllCustomers();

            // Tính toán tổng số trang
            int totalRecords = customerList.size();
            int totalPages = (int) Math.ceil((double) totalRecords / recordsPerPage);

            // Lấy dữ liệu cho trang hiện tại
            int start = (currentPage - 1) * recordsPerPage;
            int end = Math.min(start + recordsPerPage, totalRecords);
            List<search> pageCustomerList = customerList.subList(start, end);

            // Đưa danh sách và các thông tin phân trang vào request
            request.setAttribute("customerList", pageCustomerList);
            request.setAttribute("totalPages", totalPages);
            request.setAttribute("currentPage", currentPage);
            
            // Chuyển tiếp đến trang JSP
            request.getRequestDispatcher("/WEB-INF/jsp/search.jsp").forward(request, response);
        } catch (SQLException e) {
            e.printStackTrace();
        }
    }
```
 // <div>
//  <label for="search">Sex:</label>
//  <select name="cboSex">
  //  <option value="0" ${cboSex == null || cboSex == '0' ? 'selected' : ''}></option>
//    <option value="M" ${cboSex == 'M' ? 'selected' : ''}>Male</option>
   // <option value="F" ${cboSex == 'F' ? 'selected' : ''}>Female</option>
//  </select>
//</div>
        request.setAttribute("customerList", customerList);
        request.setAttribute("txtCustomerName", customerName); // Giữ lại giá trị txtCustomerName
        request.setAttribute("cboSex", sex); // Giữ lại giá trị cboSex
        

        request.getRequestDispatcher("/WEB-INF/jsp/search.jsp").forward(request, respon
  ``` <!-- sex -->
<div>
  <label for="search">Sex:</label>
  <select name="cboSex">
    <option value="0" ${cboSex == null || cboSex == '0' ? 'selected' : ''}></option>
    <option value="M" ${cboSex == 'M' ? 'selected' : ''}>Male</option>
    <option value="F" ${cboSex == 'F' ? 'selected' : ''}>Female</option>
  </select>
</div>
```


















Java@WebServlet("/searchServlet")
public class SearchServlet extends HttpServlet {
    protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        String customerName = request.getParameter("customerName");
        String sex = request.getParameter("sex");
        String fromDateStr = request.getParameter("fromDate");
        String toDateStr = request.getParameter("toDate");
        
        // Chuyển đổi ngày từ String sang java.sql.Date
        Date fromDate = null;
        Date toDate = null;
        try {
            if (fromDateStr != null && !fromDateStr.isEmpty()) {
                fromDate = Date.valueOf(fromDateStr);
            }
            if (toDateStr != null && !toDateStr.isEmpty()) {
                toDate = Date.valueOf(toDateStr);
            }
        } catch (IllegalArgumentException e) {
            e.printStackTrace();
            // Xử lý lỗi chuyển đổi ngày
        }

        StringBuilder query = new StringBuilder("SELECT * FROM customers WHERE 1=1");
        
        if (customerName != null && !customerName.trim().isEmpty()) {
            query.append(" AND customerName LIKE ?");
        }
        if (sex != null && !sex.trim().isEmpty()) {
            query.append(" AND sex = ?");
        }
        if (fromDate != null) {
            query.append(" AND birthday >= ?");
        }
        if (toDate != null) {
            query.append(" AND birthday <= ?");
        }

        try (Connection connection = DriverManager.getConnection("jdbc_url", "username", "password");
             PreparedStatement pstmt = connection.prepareStatement(query.toString())) {
             
            int index = 1;

            if (customerName != null && !customerName.trim().isEmpty()) {
                pstmt.setString(index++, "%" + customerName + "%");
            }
            if (sex != null && !sex.trim().isEmpty()) {
                pstmt.setString(index++, sex);
            }
            if (fromDate != null) {
                pstmt.setDate(index++, fromDate);
            }
            if (toDate != null) {
                pstmt.setDate(index++, toDate);
            }

            ResultSet rs = pstmt.executeQuery();

            // Xử lý kết quả truy vấn và truyền dữ liệu đến JSP để hiển thị
            request.setAttribute("resultSet", rs);
            RequestDispatcher dispatcher = request.getRequestDispatcher("results.jsp");
            dispatcher.forward(request, response);

        } catch (SQLException e) {
            e.printStackTrace();
            // Xử lý lỗi cơ sở dữ liệu
        }
    }
}
<% ResultSet rs = (ResultSet) request.getAttribute("resultSet"); %>
<table>
    <tr>
        <th>ID</th>
        <th>Name</th>
        <th>Sex</th>
        <th>Birthday</th>
    </tr>
    <% while (rs.next()) { %>
    <tr>
        <td><%= rs.getInt("id") %></td>
        <td><%= rs.getString("customerName") %></td>
        <td><%= rs.getString("sex") %></td>
        <td><%= rs.getDate("birthday") %></td>
    </tr>
    <% } %>
</table>
<form action="searchServlet" method="get">
    <label for="customerName">Customer Name:</label>
    <input type="text" id="customerName" name="customerName" value="<%= request.getParameter("customerName") %>">

    <label for="sex">Sex:</label>
    <select id="sex" name="sex">
        <option value="">All</option>
        <option value="Male" <%= "Male".equals(request.getParameter("sex")) ? "selected" : "" %>>Male</option>
        <option value="Female" <%= "Female".equals(request.getParameter("sex")) ? "selected" : "" %>>Female</option>
    </select>

    <label for="fromDate">From Date:</label>
    <input type="text" id="fromDate" name="fromDate" value="<%= request.getParameter("fromDate") %>">

    <label for="toDate">To Date:</label>
    <input type="text" id="toDate" name="toDate" value="<%= request.getParameter("toDate") %>">

    <input type="submit" value="Search">
</form>

