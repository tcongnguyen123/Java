# Java
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

