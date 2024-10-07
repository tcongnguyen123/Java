# Java
```
1. Thêm Thư Viện Cần Thiết
Bạn cần thêm các thư viện của Spring và Hibernate vào dự án của mình. Tải các JAR cần thiết từ trang chính thức hoặc từ Maven Repository và thêm chúng vào thư mục WEB-INF/lib của dự án.

Thư viện cần thiết:

Spring Framework: spring-core, spring-web, spring-webmvc, spring-context
Hibernate: hibernate-core, hibernate-entitymanager, hibernate-validator
MySQL Connector: mysql-connector-java (để kết nối với MySQL)
```
```
Tạo một file cấu hình Spring, ví dụ applicationContext.xml, trong thư mục WEB-INF. Nội dung file có thể giống như sau:
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
                           http://www.springframework.org/schema/beans/spring-beans.xsd">

    <!-- DataSource configuration -->
    <bean id="dataSource" class="org.apache.commons.dbcp.BasicDataSource">
        <property name="driverClassName" value="com.mysql.jdbc.Driver" />
        <property name="url" value="jdbc:mysql://localhost:3306/your_database_name" />
        <property name="username" value="your_username" />
        <property name="password" value="your_password" />
    </bean>

    <!-- Hibernate SessionFactory configuration -->
    <bean id="sessionFactory" class="org.springframework.orm.hibernate5.LocalSessionFactoryBean">
        <property name="dataSource" ref="dataSource" />
        <property name="packagesToScan" value="fjs.cs.dto" />
        <property name="hibernateProperties">
            <props>
                <prop key="hibernate.dialect">org.hibernate.dialect.MySQL5Dialect</prop>
                <prop key="hibernate.show_sql">true</prop>
                <prop key="hibernate.hbm2ddl.auto">update</prop>
            </props>
        </property>
    </bean>

    <!-- HibernateTemplate configuration -->
    <bean id="hibernateTemplate" class="org.springframework.orm.hibernate5.HibernateTemplate">
        <property name="sessionFactory" ref="sessionFactory" />
    </bean>
</beans>

```
```
Nếu bạn chưa có, tạo một file hibernate.cfg.xml trong src/main/resources với nội dung tương tự:
<!DOCTYPE hibernate-configuration PUBLIC 
    "-//Hibernate/Hibernate Configuration DTD 3.0//EN"
    "http://hibernate.sourceforge.net/hibernate-configuration-3.0.dtd">
<hibernate-configuration>
    <session-factory>
        <property name="hibernate.dialect">org.hibernate.dialect.MySQL5Dialect</property>
        <property name="hibernate.connection.driver_class">com.mysql.jdbc.Driver</property>
        <property name="hibernate.connection.url">jdbc:mysql://localhost:3306/your_database_name</property>
        <property name="hibernate.connection.username">your_username</property>
        <property name="hibernate.connection.password">your_password</property>
        <property name="hibernate.hbm2ddl.auto">update</property>
        <mapping class="fjs.cs.dto.CustomerDto" />
    </session-factory>
</hibernate-configuration>

```
```
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.orm.hibernate5.HibernateTemplate;
import org.springframework.stereotype.Repository;

import java.util.List;

@Repository
public class CustomerDao {

    @Autowired
    private HibernateTemplate hibernateTemplate;

    public void saveCustomer(CustomerDto customer) {
        hibernateTemplate.save(customer);
    }

    public List<CustomerDto> getAllCustomers() {
        return hibernateTemplate.loadAll(CustomerDto.class);
    }
}
<servlet>
    <servlet-name>spring</servlet-name>
    <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
    <load-on-startup>1</load-on-startup>
</servlet>
<servlet-mapping>
    <servlet-name>spring</servlet-name>
    <url-pattern>/spring/*</url-pattern>
</servlet-mapping>
import org.springframework.beans.factory.annotation.Autowired;

public class LoginAction extends Action {

    @Autowired
    private CustomerDao customerDao;

    public ActionForward execute(ActionMapping mapping, ActionForm form,
                                 HttpServletRequest req, HttpServletResponse resp) {
        // Sử dụng customerDao để thực hiện truy vấn
        List<CustomerDto> customers = customerDao.getAllCustomers();
        // Thực hiện logic khác
        return mapping.findForward("success");
    }
}
public ActionForward execute(ActionMapping mapping, ActionForm form,
                             HttpServletRequest req, HttpServletResponse resp) {
    ApplicationContext context = WebApplicationContextUtils.getWebApplicationContext(req.getServletContext());
    customerDao = context.getBean(CustomerDao.class);
    // Thực hiện logic
}

```
```
		<html:messages id="message" property="deleteError">
    		<input type="hidden" id="errorMessage" value="<bean:write name='message' />" />
		</html:messages>
function showErrorMessage(message) {
    alert(message);
}

window.onload = function() {
    const errorMessage = document.getElementById("errorMessage");
    if (errorMessage && errorMessage.value) {
        setTimeout(function() {
            showErrorMessage(errorMessage.value);
        }, 1000);
    }

    validateForm(); // Gọi validateForm ngay sau khi trang tải xong
}
        if ("delete".equals(action)) {
            String[] deleteIds = req.getParameterValues("deleteIds");
            
            // Validate if any checkbox was selected
            if (deleteIds == null || deleteIds.length == 0) {
                errors = new ActionMessages();
                errors.add("deleteError", new ActionMessage("error.delete.noselection"));
                saveErrors(req, errors);
                return mapping.findForward("search");
            }

            // Proceed with the delete logic if rows are selected
            searchDao customerDAO = new searchDao();
            for (String id : deleteIds) {
                customerDAO.deleteCustomerById(id);
            }

            // After deletion, refresh the list and pagination
            return execute(mapping, form, req, resp); // Re-run the search action to refresh the customer list
        }
```
```
/**
 * 
 */
<html:form action="/search" method="post"> // dat vo truoc table 
boolean selectAll = searchForm.isSelectAll();
<html:checkbox property="selectAll" styleId="select-all" onclick="selectAllCheckboxes(this)" />
    // Getter và setter cho selectAll
    public boolean isSelectAll() {
        return selectAll;
    }

    public void setSelectAll(boolean selectAll) {
        this.selectAll = selectAll;
    }
private boolean selectAll; 
function selectAllCheckboxes(source) {
    var checkboxes = document.querySelectorAll('input[name="deleteIds"]');
    for (var i = 0; i < checkboxes.length; i++) {
        checkboxes[i].checked = source.checked;
    }
}
function updateSelectAll() {
    var checkboxes = document.querySelectorAll('input[name="deleteIds"]');
    var selectAll = document.getElementById('select-all');
    selectAll.checked = Array.from(checkboxes).every(checkbox => checkbox.checked);
}
document.getElementById("addNew").addEventListener("click", function() {
    window.location.href = 'edit'; // Chuyển hướng đến servlet Edit
});

function isValidDate(date) {
    // Kiểm tra định dạng YYYY/MM/DD
    return /^\d{4}\/\d{2}\/\d{2}$/.test(date);
}

function validateForm() {
    var errorFlag = document.getElementById("errorFlag").value;
    if (errorFlag === "true") {
        alert("Có lỗi xảy ra. Vui lòng kiểm tra lại thông tin.");
        return false; // Ngăn chặn form submit
    }
    return true; // Cho phép form submit nếu không có lỗi
}

window.onload = function() {
    setTimeout(validateForm, 100); // Gọi hàm validateForm sau 100ms khi trang tải xong
};
<script src="<c:url value='/js/scripts.js' />"></script>
webcontent : jsp (search.jsp ), js(scripts.js)
```
```

```
```
<%@ page language="java" contentType="text/html; charset=UTF-8"
	pageEncoding="UTF-8"%>
<%@ taglib uri="http://java.sun.com/jsp/jstl/core" prefix="c"%>
<%@ taglib uri="http://struts.apache.org/tags-html" prefix="html" %>
<%@ taglib uri="/WEB-INF/struts-bean.tld" prefix="bean" %>
<%@ taglib uri="/WEB-INF/struts-logic.tld" prefix="logic" %>
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
	   	margin-left : 30px;
		margin-right : 30px;
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
	tr:first-child > th {
	    background-color: rgb(0, 223, 0);
	}
	tr:nth-child(even) {
	    background-color: #f2f2f2;
	}
	tr:hover {
	    background-color: #f5f5f5;
	}
	.breadcrumb {
	    border-bottom : 2px solid;
	}
	.breadcrumb h1 {
		color : red;
		
	}
	.welcome-user-container {
	    display: flex;
	    align-items: center;
	    justify-content: space-between;
	    padding-top: 10px;
	}
	.container {
		margin-left : 20px;
		margin-right : 20px;
	}
	.divider {
	    height: 40px;
	    width: 100%;
	    background-color: blue;
	}
	.search form {
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
	.pagination {
	    display: flex;
	    flex-direction: row;
	    justify-content: space-between;
	}
	.pagination div {
	    display: flex;
	    flex-direction: row;
	    align-items: center;
	}
	.pagination div > p {
	    padding-inline: 10px;
	}

</style>

</head>
<body>
	<div class="breadcrumb">
		<h1>Training</h1>
		<html:errors/>
		<p id="valid">${isvalid}</p>
	</div>
	<div class="welcome-user-container">
	    <p>Welcome, <bean:write name="username" scope="session" /></p>
        <html:form action="/search" style="display: inline;">
            <html:hidden property="action" value="logout" />
            <a href="#" onclick="document.forms[0].submit(); return false;">Log Out</a>
        </html:form>
	</div>
	<div class="divider"></div>
	<div class = "search">
		<html:form action="/search" method="post">
		    <!-- Hidden field for action -->
		    <html:hidden property="action" value="search" />
		    
		    <!-- Customer Name -->
		    <div>
		        <label for="search">Customer Name:</label>
		        <html:text property="customerName" />
		    </div>
		
		    <!-- Sex -->
		    <div>
		        <label for="search">Sex:</label>
		        <html:select property="sex">
		            <html:option value=" "></html:option>
		            <html:option value="M">Male</html:option>
		            <html:option value="F">Female</html:option>
		        </html:select>
		    </div>
		
		    <!-- Birthday -->
		    <div style="display: flex; justify-content: center; align-items: center;">
		        <label for="search">Birthday:</label>
		        <html:text property="fromBirthday" styleClass="birthday" />
		        <p>~</p>
		        <html:text property="toBirthday" styleClass="birthday" />
		    </div>
		
		    <!-- Submit Button -->
		    <button type="submit" value="Search">Search</button>
		</html:form>
	</div>


	<div class="pagination">
	    <html:form action="/search" method="post">
	        <html:hidden property="customerName" />
	        <html:hidden property="sex" />
	        <html:hidden property="fromBirthday" />
	        <html:hidden property="toBirthday" />
	        <!-- First Page Button -->
			<logic:present name="currentPage">
			    <logic:greaterThan name="currentPage" value="1">
			        <button type="submit" name="page" value="1">&laquo;</button>
			    </logic:greaterThan>
			    <logic:lessEqual name="currentPage" value="1">
			        <button type="button" disabled>&laquo;</button>
			    </logic:lessEqual>
			</logic:present>
	
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
			<logic:present name="currentPage">
			    <logic:lessThan name="currentPage" value="${totalPages}">
			        <button type="submit" name="page" value="${currentPage + 1}">></button>
			    </logic:lessThan>
			    <logic:greaterEqual name="currentPage" value="${totalPages}">
			        <button type="button" disabled>></button>
			    </logic:greaterEqual>
			</logic:present>
			

	
	        <!-- Last Page Button -->
			<logic:present name="currentPage">
			    <logic:lessThan name="currentPage" value="${totalPages}">
	                <button type="submit" name="page" value="${totalPages}">&raquo;</button>
			    </logic:lessThan>
			    <logic:greaterEqual name="currentPage" value="${totalPages}">
	                <button type="button" disabled>&raquo;</button>
			    </logic:greaterEqual>
			</logic:present>
	    </html:form>
	</div>
	    <table style="width: 100%; border-collapse: collapse;">
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
            <logic:iterate id="customer" name="customerList">
                <tr>
                    <td>
                        <input type="checkbox" name="deleteIds" id = "select-all" value="${customer.id}" onclick="updateSelectAll()">
                    </td>
                    <td><a href="edit?id=${customer.id}">${customer.id}</a></td>
                    <td>${customer.customerName}</td>
                    <td>
					<logic:equal name="customer" property="sex" value="M">
					    Male
					</logic:equal>
					<logic:notEqual name="customer" property="sex" value="M">
					    Female
					</logic:notEqual>
						
                    </td>
                    <td>${customer.birthday}</td>
                    <td>${customer.address}</td>
                </tr>
            </logic:iterate>
    </table> 
	<script type="text/javascript">
        function submitForm() {
            var isValid = '<%= request.getAttribute("isvalid") %>';
            if (isValid === "fail") {
                var errorMessage = '<bean:message key="error.birthday.invalid" />';
                alert(errorMessage);
            }
		}
        
    </script>
    	<logic:messagesPresent message="true">
		<html:messages id="error">
			<script type="text/javascript">
				var errorMessage = '<bean:write name="error"/>';
				if (errorMessage) {
					alert(errorMessage);
				}
			</script>
		</html:messages>
	</logic:messagesPresent>
</body>

</html>
```
```
package com.example.action;

import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import javax.servlet.http.HttpSession;

import org.apache.struts.action.Action;
import org.apache.struts.action.ActionForm;
import org.apache.struts.action.ActionForward;
import org.apache.struts.action.ActionMapping;
import org.apache.struts.action.ActionMessage;
import org.apache.struts.action.ActionMessages;

import com.example.dao.searchDao;
import com.example.form.search;

import java.io.IOException;
import java.sql.SQLException;
import java.util.List;
import java.util.regex.Pattern;

public class searchAction extends Action {
    private static final Pattern DATE_PATTERN = Pattern.compile("^\\d{4}/\\d{2}/\\d{2}$"); // YYYY/MM/DD format

    @Override
    public ActionForward execute(ActionMapping mapping, ActionForm form,
                                 HttpServletRequest request, HttpServletResponse response) throws SQLException, IOException {
        HttpSession session = request.getSession();
        String action = request.getParameter("action");

        search searchForm = (search) form;

        String customerName = searchForm.getCustomerName();
        String sex = searchForm.getSex();
        String fromBirthday = searchForm.getFromBirthday();
        String toBirthday = searchForm.getToBirthday();

        if ("logout".equals(action)) {
            session.invalidate();
            return mapping.findForward("login");
        } else if ("search".equals(action)) {
            if (fromBirthday != null && !fromBirthday.isEmpty() && !DATE_PATTERN.matcher(fromBirthday).matches()) {
                ActionMessages errors = new ActionMessages();
                errors.add("birthday", new ActionMessage("error.birthday.invalid"));
                saveErrors(request, errors);
                request.setAttribute("invalidBirthday", true);
                return mapping.findForward("search"); // Return to the search page
            }
        }

        searchDao customerDAO = new searchDao();
        int PAGE_SIZE = 5;
        String pageParam = request.getParameter("page");
        int currentPage = (pageParam != null) ? Integer.parseInt(pageParam) : 1;
        int startRow = (currentPage - 1) * PAGE_SIZE;
        List<search> customerList = customerDAO.getAllCustomers();
        int totalCustomers = customerList.size();
        int totalPages = (int) Math.ceil((double) totalCustomers / PAGE_SIZE);
        List<search> paginatedList = customerList.subList(
                Math.min(startRow, totalCustomers),
                Math.min(startRow + PAGE_SIZE, totalCustomers)
        );

        request.setAttribute("customerList", paginatedList);
        request.setAttribute("currentPage", currentPage);
        request.setAttribute("totalPages", totalPages);
        return mapping.findForward("search");
    }
}

```
```
<%@ page language="java" contentType="text/html; charset=UTF-8"
	pageEncoding="UTF-8"%>
<%@ taglib uri="http://java.sun.com/jsp/jstl/core" prefix="c"%>
<%@ taglib uri="http://struts.apache.org/tags-html" prefix="html" %>
<%@ taglib uri="/WEB-INF/struts-bean.tld" prefix="bean" %>
<%@ taglib uri="/WEB-INF/struts-logic.tld" prefix="logic" %>
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
	   	margin-left : 30px;
		margin-right : 30px;
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
	tr:first-child > th {
	    background-color: rgb(0, 223, 0);
	}
	tr:nth-child(even) {
	    background-color: #f2f2f2;
	}
	tr:hover {
	    background-color: #f5f5f5;
	}
	.breadcrumb {
	    border-bottom : 2px solid;
	}
	.breadcrumb h1 {
		color : red;
		
	}
	.welcome-user-container {
	    display: flex;
	    align-items: center;
	    justify-content: space-between;
	    padding-top: 10px;
	}
	.container {
		margin-left : 20px;
		margin-right : 20px;
	}
	.divider {
	    height: 40px;
	    width: 100%;
	    background-color: blue;
	}
	.search form {
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
	.pagination {
	    display: flex;
	    flex-direction: row;
	    justify-content: space-between;
	}
	.pagination div {
	    display: flex;
	    flex-direction: row;
	    align-items: center;
	}
	.pagination div > p {
	    padding-inline: 10px;
	}

</style>
<script type="text/javascript">
	function thongBao (){
		alert("invalid")	
	}
</script>
</head>
<body>
	<div class="breadcrumb">
		<h1>Training</h1>
    <logic:equal name="invalidBirthday" value="true">
        <script type="text/javascript">
            thongBao();
        </script>
    </logic:equal>
	</div>
	<div class="welcome-user-container">
	    <p>Welcome, <bean:write name="username" scope="session" /></p>
        <html:form action="/search" style="display: inline;">
            <html:hidden property="action" value="logout" />
            <a href="#" onclick="document.forms[0].submit(); return false;">Log Out</a>
        </html:form>
	</div>
	<div class="divider"></div>
	<div class = "search">
		<html:form action="/search" method="post">
		    <!-- Hidden field for action -->
		    <html:hidden property="action" value="search" />
		    
		    <!-- Customer Name -->
		    <div>
		        <label for="search">Customer Name:</label>
		        <html:text property="customerName" />
		    </div>
		
		    <!-- Sex -->
		    <div>
		        <label for="search">Sex:</label>
		        <html:select property="sex">
		            <html:option value=" "></html:option>
		            <html:option value="M">Male</html:option>
		            <html:option value="F">Female</html:option>
		        </html:select>
		    </div>
		
		    <!-- Birthday -->
		    <div style="display: flex; justify-content: center; align-items: center;">
		        <label for="search">Birthday:</label>
		        <html:text property="fromBirthday" styleClass="birthday" />
		        <p>~</p>
		        <html:text property="toBirthday" styleClass="birthday" />
		    </div>
		
		    <!-- Submit Button -->
		    <html:submit value="Search">Search</html:submit>
		</html:form>
	</div>


	<div class="pagination">
	    <html:form action="/search" method="post">
	        <html:hidden property="customerName" />
	        <html:hidden property="sex" />
	        <html:hidden property="fromBirthday" />
	        <html:hidden property="toBirthday" />
	        <!-- First Page Button -->
			<logic:present name="currentPage">
			    <logic:greaterThan name="currentPage" value="1">
			        <button type="submit" name="page" value="1">&laquo;</button>
			    </logic:greaterThan>
			    <logic:lessEqual name="currentPage" value="1">
			        <button type="button" disabled>&laquo;</button>
			    </logic:lessEqual>
			</logic:present>
	
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
	    </html:form>
	</div>
	    <table style="width: 100%; border-collapse: collapse;">
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
                        <input type="checkbox" name="deleteIds" id = "select-all" value="${customer.id}" onclick="updateSelectAll()">
                    </td>
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
</body>
</html>
```
```
03/10/2022
<%@ taglib uri="http://struts.apache.org/tags-html" prefix="html" %>
<%@ taglib uri="http://struts.apache.org/tags-bean" prefix="bean" %>
<%@ taglib uri="http://java.sun.com/jsp/jstl/core" prefix="c" %>

<c:forEach var="customer" items="${customerList}">
    <tr>
        <td>
            <html:checkbox property="deleteIds" value="${customer.id}" onclick="updateSelectAll()"/>
        </td>
        <td>
            <html:link action="edit?id=${customer.id}">
                <bean:write name="customer" property="id"/>
            </html:link>
        </td>
        <td><bean:write name="customer" property="customerName"/></td>
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
        <td><bean:write name="customer" property="birthday"/></td>
        <td><bean:write name="customer" property="address"/></td>
    </tr>
</c:forEach>

```
```
loginjsp
<%@ taglib uri="http://java.sun.com/jsp/jstl/core" prefix="c" %> 
<%@ taglib uri="http://struts.apache.org/tags-html" prefix="html" %>
<%@ taglib uri="/WEB-INF/struts-bean.tld" prefix="bean" %>
<%@ page language="java" contentType="text/html; charset=UTF-8" pageEncoding="UTF-8"%>
<!DOCTYPE html>
<html>

<head>
    <meta charset="UTF-8">
    <title>Login</title>
    <style>
        body {
            background-color: lightblue;
            font-family: Arial, sans-serif;
        }

        .container {
            display: flex;
            justify-content: center;
            align-items: center;
            height: 100vh;
            flex-direction: column;
        }

        .login-form {
            padding: 20px;
            background-color: white;
            border-radius: 10px;
            box-shadow: 0px 0px 10px rgba(0, 0, 0, 0.1);
            text-align: center;
            width: 300px;
        }

        h1 {
            text-align: center;
            font-size: 24px;
            margin-bottom: 20px;
        }

        label {
            margin-right: 10px;
        }

        .form-title {
            font-size: 20px;
            margin-bottom: 15px;
            color: #333;
        }

        input[type="submit"],
        input[type="button"] {
            margin-top: 10px;
            padding: 8px 12px;
            width: 100%;
            background-color: #007BFF;
            color: white;
            border: none;
            cursor: pointer;
            border-radius: 5px;
        }

        input[type="submit"]:hover,
        input[type="button"]:hover {
            background-color: #0056b3;
        }
    </style>
    <script>
        function clearForm() {
            document.forms[0].reset();
            var errorMessageDiv = document.getElementById("errorMessageDiv");
            if (errorMessageDiv) {
                errorMessageDiv.style.display = 'none'; 
            }
        }
    </script>
</head>

<body>
<div class="container">
    <h1>TRAINING</h1>
    <div class="login-form">
        <div class="form-title">Login</div> 

        <div id="errorMessageDiv" style="color: red;">
            <html:errors />
        </div>

        <html:form action="/login" method="post">
            <label for="username">Username:</label>
            <html:text property="username" /><br><br>

            <label for="password">Password:</label>
            <html:password property="password" /><br><br>

            <input type="submit" value="Login">
            <input type="button" value="Clear" onclick="clearForm()">
        </html:form>
    </div>
</div>
</body>

</html>

```
```
searchjsp
<%@ page language="java" contentType="text/html; charset=UTF-8"
	pageEncoding="UTF-8"%>
<%@ taglib uri="http://java.sun.com/jsp/jstl/core" prefix="c"%>
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>Insert title here</title>

<link rel="stylesheet" type="text/css" href="<c:url value='/css/search.css' />">
<script src="<c:url value='/js/search.js'/>"></script>

</head>
<body>
<div class="pagination">
    <form action="search.do" method="post">
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
    <h1>Danh sách Khách hàng</h1>
    <table border="1">
        <tr>
            <th>ID</th>
            <th>Tên Khách hàng</th>
            <th>Giới tính</th>
            <th>Ngày sinh</th>
            <th>Địa chỉ</th>
        </tr>
        <c:forEach var="customer" items="${customerList}">
            <tr>
                <td>${customer.id}</td>
                <td>${customer.customerName}</td>
                <td>${customer.sex}</td>
                <td>${customer.birthday}</td>
                <td>${customer.address}</td>
            </tr>
        </c:forEach>
    </table>
</body>
</html>
```
```
strutsconfig
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE struts-config PUBLIC
    "-//Apache Software Foundation//DTD Struts Configuration 1.3//EN"
    "dtd/struts-config_1_3.dtd">

<struts-config>
	<form-beans>
	    <form-bean name="loginForm" type="com.example.form.LoginForm"/>
	    <form-bean name="searchForm" type="com.example.form.search"/>
	</form-beans>
	
	<action-mappings>
	    <action path="/login"
	            type="com.example.action.LoginAction"
	            name="loginForm"
	            scope="request"
	            validate="true">
	        <forward name="success" path="/search.do" redirect="true"/>
	        <forward name="failure" path="/jsp/login.jsp"/>
	    </action>
	    <action path="/search"
                type="com.example.action.searchAction"
                name="searchForm"
                scope="request"
                validate="true">
            <forward name="success" path="/jsp/searchResults.jsp"/>
            <forward name="search" path="/jsp/search.jsp"/>
        </action>
	</action-mappings>
	<message-resources parameter="message" />
</struts-config>

```
```
web.xml
<web-app xmlns="http://java.sun.com/xml/ns/j2ee" 
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" 
         xsi:schemaLocation="http://java.sun.com/xml/ns/j2ee 
                             http://java.sun.com/xml/ns/j2ee/web-app_2_4.xsd" 
         version="2.4">
	<context-param>
	    <param-name>javax.servlet.jsp.jstl.fmt.encoding</param-name>
	    <param-value>UTF-8</param-value>
	</context-param>

    <display-name>HelloStruts1x</display-name>
	<welcome-file-list>
	    <welcome-file>index.jsp</welcome-file> <!-- Đặt trang login.do làm welcome file -->
	</welcome-file-list>

    <servlet>
        <servlet-name>action</servlet-name>
        <servlet-class>org.apache.struts.action.ActionServlet</servlet-class>
        <init-param>
            <param-name>config</param-name>
            <param-value>/WEB-INF/struts-config.xml</param-value>
        </init-param>
        <load-on-startup>2</load-on-startup>
    </servlet>

    <servlet-mapping>
        <servlet-name>action</servlet-name>
        <url-pattern>*.do</url-pattern>
    </servlet-mapping>
    

    
</web-app>

```
```
searchAction
package com.example.action;

import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import org.apache.struts.action.Action;
import org.apache.struts.action.ActionForm;
import org.apache.struts.action.ActionForward;
import org.apache.struts.action.ActionMapping;

import com.example.dao.searchDao;
import com.example.form.search;

import java.sql.SQLException;
import java.util.List;

public class searchAction extends Action {
    @Override
    public ActionForward execute(ActionMapping mapping, ActionForm form,
            HttpServletRequest request, HttpServletResponse response) throws SQLException {
        
        // Tạo một đối tượng DAO
        searchDao customerDAO = new searchDao();
        int PAGE_SIZE = 5;
        String pageParam = request.getParameter("page");
        int currentPage = (pageParam != null) ? Integer.parseInt(pageParam) : 1;

        // Tính toán số lượng khách hàng cần lấy
        int startRow = (currentPage - 1) * PAGE_SIZE;
        List<search> customerList = customerDAO.getAllCustomers(); // Lấy tất cả khách hàng
        
        // Chia nhỏ danh sách khách hàng
        int totalCustomers = customerList.size();
        int totalPages = (int) Math.ceil((double) totalCustomers / PAGE_SIZE);

        // Đặt danh sách khách hàng vào request
        List<search> paginatedList = customerList.subList(
            Math.min(startRow, totalCustomers), 
            Math.min(startRow + PAGE_SIZE, totalCustomers)
        );
        
        request.setAttribute("customerList", paginatedList);
        request.setAttribute("currentPage", currentPage);
        request.setAttribute("totalPages", totalPages);
        
        // Chuyển hướng tới trang hiển thị kết quả
        return mapping.findForward("search");
    }
}

```
```
loginAction
package com.example.action;
import org.apache.struts.action.ActionMessages;

import com.example.dao.loginDao;
import com.example.form.LoginForm;

import java.io.UnsupportedEncodingException;

import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import javax.servlet.http.HttpSession;

import org.apache.struts.action.Action;
import org.apache.struts.action.ActionForm;
import org.apache.struts.action.ActionForward;
import org.apache.struts.action.ActionMapping;
import org.apache.struts.action.ActionMessage;

public class LoginAction extends Action {
    public ActionForward execute(ActionMapping mapping, ActionForm form,
                                 HttpServletRequest request, HttpServletResponse response) throws UnsupportedEncodingException {
        request.setCharacterEncoding("UTF-8");
        response.setContentType("text/html; charset=UTF-8");
        response.setCharacterEncoding("UTF-8");
        LoginForm loginForm = (LoginForm) form;
        String username = loginForm.getUsername();
        String password = loginForm.getPassword();

        ActionMessages errors = new ActionMessages();

        if (username == null || username.trim().isEmpty()) {
            errors.add("username", new ActionMessage("error.username.required"));
            saveErrors(request, errors);
            return mapping.findForward("failure");
        } 
        if (password == null || password.trim().isEmpty()) {
            errors.add("password", new ActionMessage("error.password.required"));
            saveErrors(request, errors);
            return mapping.findForward("failure");
        } 

        loginDao loginDao = new loginDao();
        int count = loginDao.checkLogin(username, password);

        if (count == 1) {
            HttpSession session = request.getSession();
            session.setAttribute("username", username);
            return mapping.findForward("success");
        } else {
            errors.add("login", new ActionMessage("error.login.invalid"));
            saveErrors(request, errors);
            return mapping.findForward("failure");
        }
    }
}

```
```
search.java
package com.example.form;

import org.apache.struts.action.ActionForm;

public class search extends ActionForm {
    /**
	 * 
	 */
	private static final long serialVersionUID = 1L;
	private int id;
    private String customerName;
    private String sex;
    private String birthday;
    private String address;
    
    
    // No-args constructor is required
    public search() {
        super();
    }
	public search(int id, String customerName, String sex, String birthday, String address) {
		super();
		this.id = id;
		this.customerName = customerName;
		this.sex = sex;
		this.birthday = birthday;
		this.address = address;
	}
	// Getters and Setters for form properties
    public int getId() {
        return id;
    }

    public void setId(int id) {
        this.id = id;
    }

    public String getCustomerName() {
        return customerName;
    }

    public void setCustomerName(String customerName) {
        this.customerName = customerName;
    }

    public String getSex() {
        return sex;
    }

    public void setSex(String sex) {
        this.sex = sex;
    }

    public String getBirthday() {
        return birthday;
    }

    public void setBirthday(String birthday) {
        this.birthday = birthday;
    }

    public String getAddress() {
        return address;
    }

    public void setAddress(String address) {
        this.address = address;
    }

    // Optionally, override the reset method to initialize default values
    @Override
    public void reset(org.apache.struts.action.ActionMapping mapping, javax.servlet.http.HttpServletRequest request) {
        // Reset fields to default values (optional)
        this.id = 0;
        this.customerName = null;
        this.sex = null;
        this.birthday = null;
        this.address = null;
    }
}
```



```
editdao
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
    public boolean addCustomer(search customer, String username) {
        boolean isInserted = false;

        // Câu lệnh SQL để chèn dữ liệu khách hàng và lấy mã psn_cd từ bảng mstuser
        String insertSQL = "INSERT INTO mstcustomer (customerName, sex, birthday, address, delete_ymd, insert_psn_cs) "
                         + "SELECT ?, ?, ?, ?, ?, NULL, u.psn_cd "
                         + "FROM mstuser u "
                         + "WHERE u.username = ? AND u.deleteymd IS NULL";

        try (Connection conn = connectDB.getConnection(); 
             PreparedStatement stmt = conn.prepareStatement(insertSQL)) {

            // Thiết lập các giá trị cho PreparedStatement
            stmt.setString(1, customer.getCustomerName());    // customerName
            stmt.setString(2, customer.getSex());             // sex
            stmt.setString(3, customer.getBirthday());        // birthday
            stmt.setString(4, customer.getAddress());         // address
//            stmt.setString(5, customer.getEmail());           // email
            stmt.setString(6, username);                      // username để tìm mã psn_cd

            // Thực thi câu lệnh và kiểm tra kết quả
            int rowsAffected = stmt.executeUpdate();
            if (rowsAffected > 0) {
                isInserted = true; // Nếu có ít nhất một dòng được thêm vào, đánh dấu là thành công
            }

        } catch (SQLException e) {
            e.printStackTrace();
        }

        return isInserted;
    }
//    UPDATE mstcustomer
//    SET customerName = "Long Thanh Phe", 
//        sex = "M", 
//        birthday = "2002/02/10", 
//        address = "Tay Ninh", 
//        email = "test@gmail.com", 
//        update_psn_cd = (SELECT u.userid FROM mstuser u WHERE u.username = "nguyen" AND u.deleteymd IS NULL LIMIT 1) 
//    WHERE id = 4 
    
    
// SELECT COUNT(*) AS cnt, userid 
//    FROM mstuser 
//    WHERE deleteymd IS NULL AND username = 'nguyen' AND password = '123' 
//    GROUP BY userid;

    public boolean updateCustomer(search customer, String username) {
        boolean isUpdated = false;
        String updateSQL = "UPDATE mstcustomer "
                         + "SET customerName = ?, "
                         + "sex = ?, "
                         + "birthday = ?, "
                         + "address = ?, "
                         + "email = ?, "
                         + "update_psn_cd = (SELECT u.userid FROM mstuser u WHERE u.username = ? AND u.deleteymd IS NULL LIMIT 1) "
                         + "WHERE id = ?";

        try (Connection conn = connectDB.getConnection();
             PreparedStatement stmt = conn.prepareStatement(updateSQL)) {

            // Thiết lập các giá trị cho PreparedStatement
            stmt.setString(1, customer.getCustomerName()); // customerName
            stmt.setString(2, customer.getSex());           // sex
            stmt.setString(3, customer.getBirthday());      // birthday
            stmt.setString(4, customer.getAddress());       // address
//            stmt.setString(5, customer.getEmail());         // email
            stmt.setString(6, username);                     // username để lấy userid
            stmt.setInt(7, customer.getId());               // id (khóa chính để xác định khách hàng)

            // Thực thi câu lệnh và kiểm tra kết quả
            int rowsAffected = stmt.executeUpdate();
            if (rowsAffected > 0) {
                isUpdated = true; // Nếu có ít nhất một dòng được cập nhật
            }

        } catch (SQLException e) {
            e.printStackTrace();
        }

        return isUpdated; // Trả về kết quả cập nhật
    }



}

```
```
function updateSelectAll() {
    var checkboxes = document.querySelectorAll('input[name="deleteIds"]');
    var selectAll = document.getElementById('select-all');
    selectAll.checked = Array.from(checkboxes).every(checkbox => checkbox.checked);
}
<input type="checkbox" id="select-all" onclick="selectAllCheckboxes(this)">
```



```
package com.example.form;

import org.apache.struts.action.ActionForm;

public class SearchForm extends ActionForm {
    private String txtCustomerName;
    private String cboSex;
    private String fromBirthday;
    private String toBirthday;

    // Getters and setters
    public String getTxtCustomerName() { return txtCustomerName; }
    public void setTxtCustomerName(String txtCustomerName) { this.txtCustomerName = txtCustomerName; }

    public String getCboSex() { return cboSex; }
    public void setCboSex(String cboSex) { this.cboSex = cboSex; }

    public String getFromBirthday() { return fromBirthday; }
    public void setFromBirthday(String fromBirthday) { this.fromBirthday = fromBirthday; }

    public String getToBirthday() { return toBirthday; }
    public void setToBirthday(String toBirthday) { this.toBirthday = toBirthday; }
}
```
```
package com.example.action;

import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import org.apache.struts.action.Action;
import org.apache.struts.action.ActionForm;
import org.apache.struts.action.ActionForward;
import org.apache.struts.action.ActionMapping;
import com.example.dao.CustomerDAO;
import java.util.List;

public class SearchAction extends Action {
    public ActionForward execute(ActionMapping mapping, ActionForm form,
                                 HttpServletRequest request, HttpServletResponse response) throws Exception {
        SearchForm searchForm = (SearchForm) form;
        
        // Lấy dữ liệu từ form
        String customerName = searchForm.getTxtCustomerName();
        String sex = searchForm.getCboSex();
        String fromBirthday = searchForm.getFromBirthday();
        String toBirthday = searchForm.getToBirthday();

        // Thực hiện tìm kiếm qua DAO
        CustomerDAO customerDAO = new CustomerDAO();
        List<Customer> customerList = customerDAO.searchCustomers(customerName, sex, fromBirthday, toBirthday);
        
        request.setAttribute("customerList", customerList);

        return mapping.findForward("success");
    }
}

```
```
<action-mappings>
    <action path="/search"
            type="com.example.action.SearchAction"
            name="searchForm"
            scope="request"
            validate="false">
        <forward name="success" path="/jsp/search.jsp"/>
    </action>
</action-mappings>

<form-beans>
    <form-bean name="searchForm" type="com.example.form.SearchForm"/>
</form-beans>

```
```
<%@ page language="java" contentType="text/html; charset=UTF-8" pageEncoding="UTF-8"%>
<%@ taglib uri="http://java.sun.com/jsp/jstl/core" prefix="c"%>
<%@ taglib uri="http://struts.apache.org/tags-html" prefix="html" %>
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>Search</title>
<!-- Giữ nguyên CSS và JavaScript -->
</head>
<body>

    <html:form action="/search" method="post">
        <label for="search">Customer Name:</label>
        <html:text property="txtCustomerName" /><br>

        <label for="search">Sex:</label>
        <html:select property="cboSex">
            <html:option value="0"></html:option>
            <html:option value="M">Male</html:option>
            <html:option value="F">Female</html:option>
        </html:select><br>

        <label for="search">Birthday:</label>
        <html:text property="fromBirthday" class="birthday" /> ~ 
        <html:text property="toBirthday" class="birthday" /><br>

        <input type="submit" value="Search"/>
    </html:form>

    <table>
        <tr>
            <th>Customer ID</th>
            <th>Customer Name</th>
            <th>Sex</th>
            <th>Birthday</th>
            <th>Address</th>
        </tr>
        <c:forEach var="customer" items="${customerList}">
            <tr>
                <td><a href="edit.do?id=${customer.id}">${customer.id}</a></td>
                <td>${customer.customerName}</td>
                <td>${customer.sex}</td>
                <td>${customer.birthday}</td>
                <td>${customer.address}</td>
            </tr>
        </c:forEach>
    </table>

</body>
</html>

```
------------------------ Struts --------------------------------------
```
web.xml
<web-app xmlns="http://java.sun.com/xml/ns/j2ee" 
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" 
         xsi:schemaLocation="http://java.sun.com/xml/ns/j2ee 
                             http://java.sun.com/xml/ns/j2ee/web-app_2_4.xsd" 
         version="2.4">

    <display-name>HelloStruts1x</display-name>
	<welcome-file-list>
	    <welcome-file>index.jsp</welcome-file> <!-- Đặt trang login.do làm welcome file -->
	</welcome-file-list>

    <servlet>
        <servlet-name>action</servlet-name>
        <servlet-class>org.apache.struts.action.ActionServlet</servlet-class>
        <init-param>
            <param-name>config</param-name>
            <param-value>/WEB-INF/struts-config.xml</param-value>
        </init-param>
        <load-on-startup>2</load-on-startup>
    </servlet>

    <servlet-mapping>
        <servlet-name>action</servlet-name>
        <url-pattern>*.do</url-pattern>
    </servlet-mapping>
</web-app>
```
```
struts-config
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE struts-config PUBLIC
    "-//Apache Software Foundation//DTD Struts Configuration 1.3//EN"
    "dtd/struts-config_1_3.dtd">

<struts-config>
	<form-beans>
	    <form-bean name="loginForm" type="com.example.form.LoginForm"/>
	</form-beans>
	
	<action-mappings>
	    <action path="/login"
	            type="com.example.action.LoginAction"
	            name="loginForm"
	            scope="request"
	            validate="true">
	        <forward name="success" path="/jsp/search.jsp" redirect="true"/>
	        <forward name="failure" path="/jsp/login.jsp"/>
	    </action>
	</action-mappings>
</struts-config>
```
```
login.jsp
<%@ taglib uri="http://java.sun.com/jsp/jstl/core" prefix="c" %> 
<%@ taglib uri="http://struts.apache.org/tags-html" prefix="html" %>
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <title>Login</title>
    <style>
        body {
            background-color: lightblue;
            font-family: Arial, sans-serif;
        }

        .container {
            display: flex;
            justify-content: center;
            align-items: center;
            height: 100vh;
            flex-direction: column;
        }

        .login-form {
            padding: 20px;
            background-color: white;
            border-radius: 10px;
            box-shadow: 0px 0px 10px rgba(0, 0, 0, 0.1);
            text-align: center;
            width: 300px;
        }

        h1 {
            text-align: center;
            font-size: 24px;
            margin-bottom: 20px;
        }

        label {
            margin-right: 10px;
        }

        .form-title {
            font-size: 20px;
            margin-bottom: 15px;
            color: #333;
        }

        input[type="submit"],
        input[type="button"] {
            margin-top: 10px;
            padding: 8px 12px;
            width: 100%;
            background-color: #007BFF;
            color: white;
            border: none;
            cursor: pointer;
            border-radius: 5px;
        }

        input[type="submit"]:hover,
        input[type="button"]:hover {
            background-color: #0056b3;
        }
    </style>
    <script>
        function clearForm() {
            document.forms[0].reset();
            var errorMessageDiv = document.getElementById("errorMessageDiv");
            if (errorMessageDiv) {
                errorMessageDiv.style.display = 'none'; 
            }
        }
    </script>
</head>

<body>
    <div class="container">
        <h1>TRAINING</h1>
        <div class="login-form">
            <div class="form-title">Login</div> 

            <c:if test="${not empty errorMessage}">
                <div id="errorMessageDiv" style="color: red;">${errorMessage}</div>
            </c:if>

            <html:form action="/login" method="post">
                <label for="username">Username:</label>
                <html:text property="username" /><br><br>

                <label for="password">Password:</label>
                <html:password property="password" /><br><br>

                <input type="submit" value="Login">
                <input type="button" value="Clear" onclick="clearForm()">
            </html:form>
        </div>
    </div>
</body>

</html>
```
```
loginAction
package com.example.action;

import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import javax.servlet.http.HttpSession;
import org.apache.struts.action.Action;
import org.apache.struts.action.ActionForm;
import org.apache.struts.action.ActionForward;
import org.apache.struts.action.ActionMapping;

import com.example.dao.loginDao;
import com.example.form.LoginForm;

public class LoginAction extends Action {
    public ActionForward execute(ActionMapping mapping, ActionForm form,
                                 HttpServletRequest request, HttpServletResponse response) {
        LoginForm loginForm = (LoginForm) form;
        String username = loginForm.getUsername();
        String password = loginForm.getPassword();

        if (username == null || username.trim().isEmpty()) {
            request.setAttribute("errorMessage", "Chưa nhập user name");
            return mapping.findForward("failure");
        } 
        if (password == null || password.trim().isEmpty()) {
            request.setAttribute("errorMessage", "Chưa nhập password");
            return mapping.findForward("failure");
        } 
        // kiem tra thong tin dang nhap
        loginDao loginDao = new loginDao();
        int count = loginDao.checkLogin(username, password);

        if (count == 1) {
            // Đang nhap thanh cong
            HttpSession session = request.getSession();
            session.setAttribute("username", username);
            return mapping.findForward("success");
        } else {
            // Thông báo lỗi
            request.setAttribute("errorMessage", "Thông tin đăng nhập sai. Vui lòng kiểm tra lại.");
            return mapping.findForward("failure");
        }
    }
}
```
```
loginForm.java
package com.example.form;

import org.apache.struts.action.ActionForm;

public class LoginForm extends ActionForm {
    /**
	 * 
	 */
	private static final long serialVersionUID = 1L;
	private String username;
    private String password;

    public String getUsername() {
        return username;
    }

    public void setUsername(String username) {
        this.username = username;
    }

    public String getPassword() {
        return password;
    }

    public void setPassword(String password) {
        this.password = password;
    }
}

```
```
index
<%@ page language="java" contentType="text/html; charset=UTF-8"
    pageEncoding="UTF-8"%>
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>Insert title here</title>
</head>
<body>
<%-- index.jsp --%>
<%
    response.sendRedirect("login.do");
%>
</body>
</html>
```




```
protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
    String action = request.getParameter("action");

    if ("clear".equals(action)) {
        String customerId = request.getParameter("id"); // Lấy id khách hàng

        // Tạo một đối tượng Customer mới và chỉ đặt id
        Customer customer = new Customer();
        customer.setId(customerId);
        
        // Set thuộc tính khác thành rỗng
        customer.setCustomerName("");
        customer.setSex("");
        customer.setBirthday("");
        customer.setAddress("");
        customer.setEmail(""); // Nếu bạn có trường email

        // Gửi đối tượng customer về JSP
        request.setAttribute("customer", customer);
        request.getRequestDispatcher("edit.jsp").forward(request, response);
    } else if ("save".equals(action)) {
        // Xử lý lưu thông tin
    }
}
function clearForm() {
    // Xóa nội dung của các trường input và textarea khác
    document.getElementById('name').value = '';
    document.getElementById('sex').value = 'M'; // Hoặc giá trị mặc định bạn muốn
    document.getElementById('birthday').value = '';
    document.getElementById('address').value = '';
    document.getElementById('email').value = '';

    // Ngăn không cho gửi yêu cầu về Servlet
    // Nếu không cần gửi yêu cầu, chỉ cần xóa trường
}

```
```
<%@ page language="java" contentType="text/html; charset=UTF-8"
    pageEncoding="UTF-8"%>
<%@ taglib uri="http://java.sun.com/jsp/jstl/core" prefix="c"%>
<!DOCTYPE html>
<html>
<head>
    <meta charset="UTF-8">
    <title>Edit Customer</title>
    <style>
        body {
            font-family: Arial, sans-serif;
            background-color: #f5f5f5;
            margin: 0;
            padding: 20px;
        }

        .container {
            max-width: 600px;
            margin: 0 auto;
            background-color: white;
            padding: 20px;
            border-radius: 8px;
            box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
        }

        h1 {
            text-align: center;
        }

        .form-group {
            display: flex;
            justify-content: space-between;
            align-items: center;
            margin-bottom: 20px;
        }

        .form-group label {
            flex: 1;
            margin-right: 10px;
            font-weight: bold;
        }

        .form-group input[type="text"], 
        .form-group input[type="email"], 
        .form-group select, 
        .form-group textarea {
            flex: 2;
            padding: 8px;
            border: 1px solid #ccc;
            border-radius: 4px;
        }

        textarea {
            resize: none;
        }

        .button-group {
            display: flex;
            justify-content: space-between;
            gap: 10px;
        }

        button {
            flex: 1;
            padding: 10px;
            background-color: #4CAF50;
            color: white;
            border: none;
            border-radius: 4px;
            cursor: pointer;
            font-size: 16px;
        }

        button:hover {
            background-color: #45a049;
        }

        .clear-button {
            background-color: #f44336;
        }

        .clear-button:hover {
            background-color: #e53935;
        }

        .error-message {
            color: red;
            text-align: center;
        }

        /* Breadcrumb and user welcome styling */
        .breadcrumb {
            padding: 10px 0;
            font-size: 14px;
        }

        .breadcrumb a {
            color: blue;
            text-decoration: none;
        }

        .breadcrumb a:hover {
            text-decoration: underline;
        }

        .welcome-user-container {
            display: flex;
            justify-content: space-between;
            align-items: center;
            margin-bottom: 20px;
        }

        .divider {
            height: 2px;
            width: 100%;
            background-color: #ccc;
            margin: 20px 0;
        }
    </style>
</head>
<body>
    <div class="container">
        <!-- Breadcrumb and User Welcome Section -->
        <div class="breadcrumb"><a href="index.html">Home</a> > Edit Customer</div>

        <div class="welcome-user-container">
            <p>Welcome, ${user}</p>
            <form action="search" method="post" style="display: inline;">
                <input type="hidden" name="action" value="logout" />
                <button type="submit">Log Out</button>
            </form>
        </div>

        <div class="divider"></div>

        <form action="edit" method="post">
            <h1>Edit Customer</h1>

            <!-- Hiển thị thông báo lỗi (nếu có) -->
            <c:if test="${not empty errorMessage}">
                <p class="error-message">${errorMessage}</p>
            </c:if>

            <div class="form-group">
                <label for="id">Customer ID:</label>
                <input type="text" id="id" name="id" value="${customer.id}" readonly />
            </div>

            <div class="form-group">
                <label for="name">Customer Name:</label>
                <input type="text" id="name" name="name" value="${customer.customerName}" />
            </div>

            <div class="form-group">
                <label for="email">Email:</label>
                <input type="email" id="email" name="email" value="${customer.email}" />
            </div>

            <div class="form-group">
                <label for="sex">Sex:</label>
                <select id="sex" name="sex">
                    <option value="M" ${customer.sex == 'M' ? 'selected' : ''}>Male</option>
                    <option value="F" ${customer.sex == 'F' ? 'selected' : ''}>Female</option>
                </select>
            </div>

            <div class="form-group">
                <label for="birthday">Birthday:</label>
                <input type="text" id="birthday" name="birthday" value="${customer.birthday}" />
            </div>

            <div class="form-group">
                <label for="address">Address:</label>
                <textarea id="address" name="address" rows="3">${customer.address}</textarea>
            </div>

            <!-- Group of buttons: Save and Clear -->
            <div class="button-group">
                <button type="submit">Save</button>
                <button type="reset" class="clear-button">Clear</button>
            </div>
        </form>
    </div>
</body>
</html>

```
--------------------------------DAY 5------------------------------------------
```
searchServlet
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
        HttpSession session = request.getSession(false); // Lấy session hiện tại, không tạo mới
        if (session == null || session.getAttribute("username") == null) {
            // Nếu chưa đăng nhập, chuyển hướng về trang đăng nhập
            response.sendRedirect("login");
            return;
        }
    	
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
        
        // Nếu không có giá trị từ request (trường hợp quay lại từ edit), lấy từ session
        if (customerName == null) {
            customerName = (String) session.getAttribute("txtCustomerName");
        }
        if (sex == null) {
            sex = (String) session.getAttribute("cboSex");
        }
        if (birthdayFrom == null) {
            birthdayFrom = (String) session.getAttribute("fromBirthday");
        }
        if (birthdayTo == null) {
            birthdayTo = (String) session.getAttribute("toBirthday");
        }
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
        }
        else if ("addNew".equals(action)) {
        	    // Lưu thông tin tìm kiếm hiện tại vào session trước khi chuyển hướng
        	    session.setAttribute("txtCustomerName", customerName);
        	    session.setAttribute("cboSex", sex);
        	    session.setAttribute("fromBirthday", birthdayFrom);
        	    session.setAttribute("toBirthday", birthdayTo);
//        	    session.setAttribute("currentPage", currentPage);
        	    // Chuyển đến trang edit
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
}
```
```
search.jsp
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

function isValidDate(date) {
    // Kiểm tra định dạng YYYY/MM/DD
    return /^\d{4}\/\d{2}\/\d{2}$/.test(date);
}

function validateForm() {
    var birthdayFrom = document.getElementsByName('fromBirthday')[0].value;
    var birthdayTo = document.getElementsByName('toBirthday')[0].value;

    // Kiểm tra định dạng của Birthday From
    if (birthdayFrom && !isValidDate(birthdayFrom)) {
        alert("Birthday From is not in the correct format (YYYY/MM/DD).");
        return false; // Ngăn không cho gửi form
    }

    // Kiểm tra định dạng của Birthday To
    if (birthdayTo && !isValidDate(birthdayTo)) {
        alert("Birthday To is not in the correct format (YYYY/MM/DD).");
        return false; // Ngăn không cho gửi form
    }

    // So sánh ngày
    if (birthdayFrom && birthdayTo && birthdayFrom > birthdayTo) {
        alert("Birthday From must be less than Birthday To.");
        return false; // Ngăn không cho gửi form
    }

    return true; // Nếu tất cả đúng, form sẽ được gửi
}

// Gán sự kiện validate cho form
document.getElementsByClassName('search')[0].onsubmit = validateForm
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
```
```
editServlet 
package servlet;

import java.io.IOException;
import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import javax.servlet.http.HttpSession;

import dao.editDao;
import model.search;

// Giả sử bạn đã có một DAO để tương tác với cơ sở dữ liệu


@WebServlet("/edit")
public class editServlet extends HttpServlet {
    private static final long serialVersionUID = 1L;

  
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
        String customerId = request.getParameter("id");

        // Lấy dữ liệu từ form
        String customerName = request.getParameter("name");
        String sex = request.getParameter("sex");
        String birthday = request.getParameter("birthday");
        String address = request.getParameter("address");

        // Tạo đối tượng search để chứa thông tin khách hàng
        search customer = new search();
        customer.setCustomerName(customerName);
        customer.setSex(sex);
        customer.setBirthday(birthday);
        customer.setAddress(address);

        editDao customerDao = new editDao();
        boolean isSuccess;

        if (customerId != null && !customerId.isEmpty()) {
            // Cập nhật khách hàng nếu có ID
            customer.setId(Integer.parseInt(customerId));
            isSuccess = customerDao.updateCustomer(customer);
        } else {
            // Thêm mới khách hàng nếu không có ID
            isSuccess = customerDao.addCustomer(customer);
        }

        // Chuyển hướng sau khi thêm/cập nhật thành công
        if (isSuccess) {
            response.sendRedirect("search"); // Chuyển về trang danh sách khách hàng
        } else {
            request.setAttribute("errorMessage", "Lưu khách hàng không thành công.");
            request.getRequestDispatcher("/WEB-INF/jsp/edit.jsp").forward(request, response);
        }
    }


    }
//    @Override
//    protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
//        String customerId = request.getParameter("id");
//        
//        if (customerId != null) {
//            // Lấy thông tin khách hàng từ cơ sở dữ liệu bằng ID
//            editDao customerDao = new editDao();
//            search customer = customerDao.getCustomerById(Integer.parseInt(customerId));
//            
//            // Đưa thông tin khách hàng vào request để hiển thị trên trang edit
//            request.setAttribute("customer", customer);
//            
//            // Forward đến trang edit
//            request.getRequestDispatcher("/WEB-INF/jsp/edit.jsp").forward(request, response);
//        }
//    }
```
```
editDao
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
    public boolean addCustomer(search customer) {
        boolean isInserted = false;
        // Kết nối và chèn dữ liệu khách hàng vào cơ sở dữ liệu
        String insertSQL = "INSERT INTO mstcustomer (customerName, sex, birthday, address) VALUES (?, ?, ?, ?)";
        
        try (Connection conn = connectDB.getConnection(); 
             PreparedStatement stmt = conn.prepareStatement(insertSQL)) {
            
            // Thiết lập các giá trị cho PreparedStatement
            stmt.setString(1, customer.getCustomerName());
            stmt.setString(2, customer.getSex());
            stmt.setString(3, customer.getBirthday());
            stmt.setString(4, customer.getAddress());
            
            // Thực thi câu lệnh và kiểm tra kết quả
            int rowsAffected = stmt.executeUpdate();
            if (rowsAffected > 0) {
                isInserted = true; // Nếu có ít nhất một dòng được thêm vào, đánh dấu là thành công
            }
            
        } catch (SQLException e) {
            e.printStackTrace();
        }
        
        return isInserted;
    }
    public boolean updateCustomer(search customer) {
        boolean isUpdated = false;
        String updateSQL = "UPDATE mstcustomer SET customerName = ?, sex = ?, birthday = ?, address = ? WHERE id = ?";

        try (Connection conn = connectDB.getConnection();
             PreparedStatement stmt = conn.prepareStatement(updateSQL)) {

            stmt.setString(1, customer.getCustomerName());
            stmt.setString(2, customer.getSex());
            stmt.setString(3, customer.getBirthday());
            stmt.setString(4, customer.getAddress());
            stmt.setInt(5, customer.getId());

            int rowsAffected = stmt.executeUpdate();
            if (rowsAffected > 0) {
                isUpdated = true;
            }

        } catch (SQLException e) {
            e.printStackTrace();
        }

        return isUpdated;
    }

}
```
```
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
<form action="edit" method="post">
    <div>
        <label for="id">Customer ID:</label>
        <input type="text" id="id" name="id" value="${customer.id}" readonly />
    </div>
    <div>
        <label for="name">Customer Name:</label>
        <input type="text" name="name" value="${customer.customerName}" />
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
        <input type="text" name="birthday" value="${customer.birthday}" />
    </div>
    <div>
        <label for="address">Address:</label>
        <input type="text" name="address" value="${customer.address}" />
    </div>
    <button type="submit">Save</button>
</form>
</body>
</html>
```

--------------------------------------------------------------------------
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

function isValidDate(date) {
    // Kiểm tra định dạng YYYY/MM/DD
    return /^\d{4}\/\d{2}\/\d{2}$/.test(date);
}

function validateForm() {
    var birthdayFrom = document.getElementsByName('fromBirthday')[0].value;
    var birthdayTo = document.getElementsByName('toBirthday')[0].value;

    // Kiểm tra định dạng của Birthday From
    if (birthdayFrom && !isValidDate(birthdayFrom)) {
        alert("Birthday From is not in the correct format (YYYY/MM/DD).");
        return false; // Ngăn không cho gửi form
    }

    // Kiểm tra định dạng của Birthday To
    if (birthdayTo && !isValidDate(birthdayTo)) {
        alert("Birthday To is not in the correct format (YYYY/MM/DD).");
        return false; // Ngăn không cho gửi form
    }

    // So sánh ngày
    if (birthdayFrom && birthdayTo && birthdayFrom > birthdayTo) {
        alert("Birthday From must be less than Birthday To.");
        return false; // Ngăn không cho gửi form
    }

    return true; // Nếu tất cả đúng, form sẽ được gửi
}

// Gán sự kiện validate cho form
document.getElementsByClassName('search')[0].onsubmit = validateForm
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
```

```
https://github.com/tcongnguyen123/Java/blob/main/README.mdif (!validInput) {
    // Giữ lại các giá trị nhập vào mới
    String previousCustomerName = (String) session.getAttribute("txtCustomerName");
    String previousSex = (String) session.getAttribute("cboSex");
    String previousBirthdayFrom = (String) session.getAttribute("fromBirthday");
    String previousBirthdayTo = (String) session.getAttribute("toBirthday");

    // Lấy lại danh sách khách hàng từ DB theo thông tin tìm kiếm trước đó
    List<search> customerList;
    try {
        customerList = customerDAO.searchCustomers(previousCustomerName, previousSex, previousBirthdayFrom, previousBirthdayTo);
        request.setAttribute("customerList", customerList);
        request.setAttribute("txtCustomerName", previousCustomerName); // Giữ lại tên khách hàng trước đó
        request.setAttribute("cboSex", sex != null ? sex : previousSex); // Giữ lại giới tính mới nhập vào
        request.setAttribute("fromBirthday", birthdayFrom); // Giữ lại ngày sinh từ
        request.setAttribute("toBirthday", birthdayTo); // Giữ lại ngày sinh đến
        request.getRequestDispatcher("/WEB-INF/jsp/search.jsp").forward(request, response);
        return; // Dừng lại không thực hiện tiếp
    } catch (SQLException e) {
        e.printStackTrace();
    }
}

```
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

