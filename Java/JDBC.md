# JDBC
- JDBC에 관련하여 설명을 기재한다.

<br>

## JDBC란?
- Java Application에서 DataBase와 연동할 수 있는 API이다.
- MySQL, Oracle 등 다양한 DBMS과의 상호작용이 가능하다. 

<br>

## JDBC 구성요소

### JDBC 드라이버
- 상호작용 하고자 하는 DataBase의 드라이버를 통해 API를 이용할 수 있다.
  - MySQL을 사용한다면, MySQL Driver 적용

<br>

### Connection
- 해당 DB와 연결에 사용 되는 객체이다.

<br>

### Statement
- SQL 쿼리문을 실행하는데 사용 되는 객체이다.
  - Statement, PrepareStatement, CallableStatement 가 대표적이다.
  - 최근 트렌드에서는 Statement를 잘 사용하지 않는 추세이다.

<br>

### ResultSet
- SQL 쿼리의 SELECT 절을 사용하여 결과를 얻어 낼 때 필요한 객체 타입이다.
- 테이블 형식의 데이터를 읽는데 필수적이다.

<br>

### JDBC API Flow
- 아래와 같이 Java App에서 JDBC API를 요청하면, 해당 DB의 Driver이 작동하여 DB에 접근한다.

<img src = "https://github.com/user-attachments/assets/d8395deb-a909-48ea-9ca6-0c2b377849b5" width="900" height="100%">

<br>

## Example Code
```Java
package partice;

import com.sun.source.tree.BreakTree;

import java.sql.*;

public class Test {

    public static void main(String[] args) throws Exception{
        String url = "jdbc:mysql://localhost:3306/testDB";
        String user = "root";
        String pwd = "1234";

        Connection con = DriverManager.getConnection(url, user, pwd);
        Statement stmt = con.createStatement();
        ResultSet rs = null;

         // Insert
        {
            String insertSql = "INSERT INTO customer VALUES (6, '손흥민', '영국 토트넘', '010-3333-1234'); ";
            int ret = stmt.executeUpdate(insertSql);
            System.out.println(ret);
        }

        // Update
        {
            String updateSql = "UPDATE customer SET address = '한국 서울' WHERE custid = 6; ";
            int ret = stmt.executeUpdate(updateSql);
            System.out.println(ret);
        }

        // Select
        {
            String selectSql = "SELECT * FROM customer; ";
            rs = stmt.executeQuery(selectSql);

            while (rs.next()) {
                // row 1
                System.out.println(rs.getInt("custid" ) + " | " +
                                   rs.getString("name") + " | " +
                                   rs.getString("address") + " | " +
                                   rs.getString("phone"));
            }
        }

        // Select 1 row
        {
            String selectDetailSql = "SELECT * FROM customer WHERE custid = 6; ";
            rs = stmt.executeQuery(selectDetailSql);

            if (rs.next()) {
                // row 1
                System.out.println(rs.getInt("custid" ) + " | " +
                        rs.getString("name") + " | " +
                        rs.getString("address") + " | " +
                        rs.getString("phone"));
            }
        }

        // DELETE
        {
            String deleteSql = "DELETE + " +
                                "FROM Customer " +
                                "WHERE custid = 6; ";
            int ret = stmt.executeUpdate(deleteSql);
            System.out.println(ret);
        }


        if (rs != null) rs.close();
        stmt.close();
        con.close();
    }
}
```

<br>

# 구성 요소 별 심화 내용
- 각 구성요소 별로 상세 내용을 다뤄보자.

<br>

## Connection 객체
- 특히, 커넥션 관련해서는 JDBC에서 제일 중요한 요소이다.
- DB와의 연결 리소스가 가장 많이 소모되기 때문에 보다 효율적인 방법을 통해 설계해야 한다.

<br>

### Connection 리소스 고려 사항
- 항상 DB와의 Connection을 하게 되면 close()를 통해 리소스를 닫아줘야한다.
- 만약 닫지않는다면, Connection 객체를 사용하지 않는 상태에서도 리소스가 소모되고 있기 때문에 자원 소모가 비약적으로 커진다.

<br>

### 개선안 1
- 그래서 Connection 이후에 사용이 끝나면, close()를 통해 바로바로 닫아주는 형태가 고안되었다.
- 하지만, 일일이 모든 로직에서 close() 코드를 작성하는 것도 굉장히 번거롭다.
  - 뿐만 아니라, 1시간에 3,000번의 요청이 들어오면 3,000번의 (연결 - 종료) 리소스가 굉장히 빈번하여 자원 소모가 심하다.

<br>

### Connection Pool
- 위 문제를 해결하기 위해 나타난 것이 *Connection Pool* 이다.
- Java App이 생성 되는 시점에 사전에 Connection 객체를 미리 생성하여, 보관하고 Java App에서 요청하면 필요할 때 꺼내어 응답을 준다.
- 즉, 미리 생성되어 있는 Connection 객체를 받아 사용 되는 것이기 때문에 일일이 새로 생성하는 리소스가 발생하지 않게 된다.

<img src = "https://github.com/user-attachments/assets/e874887d-73c5-4e05-a6dd-955dc29573e4
" width="900" height="100%">

<br>

## Statement 객체
- Java App에서 Drvier로 API로 요청 보낼 때 SQL 쿼리를 보내는 요소이다.

<br>

### Statement
- SQL 전달 할 때 사전 처리 등이 진행되며, 객체 생성 시에 SQL과 상관없이 생성한다.
- 예시 코드를 보자.

```Java
Connection con = DriverManager.getConnection(url, user, pwd);
Statement stmt = con.createStatement();

String updateSql = "UPDATE Customer SET address = '한국 서울', WHERE customerId = 6; ";
stmt.executeUpdate(updateSql);
```

### PrepareStatement
- 객체가 만들어 질 때 SQL 문에 대한 사전 처리 등이 진행 (권장)
- Statement와의 차이점을 비교해보자.

```Java
Connection con = DriverManager.getConnection(url, user, pwd);

// ? 순서대로 index 1부터 적용된다.
String updateSql = "UPDATE Customer SET address = ?, WHERE customerId = ?; ";  
Statement pstmt = con.prepareStatement(updateSql);

pstmt.setString(1, "한국 서울"); // 즉, ?의 첫 번째인 1번 Index에 "한국 서울" 값을 업데이트한다.
pstmt.setInt(2, 6); // 2번 Index인 customerId에 6을 넣는다.

pstmt.executeUpdate();
```

<br>

## ResultSet
- SELECT query를 사용할 때 필요한 객체이다. 이 객체를 통해 값을 얻어올 수 있다.
- Dto를 설계를 통해 같이 구현하면 보다 효과적으로 처리 할 수 있다.

```Java
package partice;

// --CustomerDto
public class CustomerDto {
    private int custId;
    private String name;
    private String address;
    private String phone;

    public CustomerDto() {}

    public CustomerDto(int custId, String name, String address, String phone) {
        this.custId = custId;
        this.name = name;
        this.address = address;
        this.phone = phone;
    }

    // get
    public int getCustId() {
        return custId;
    }

    public String getName() {
        return name;
    }

    public String getAddress() {
        return address;
    }

    public String getPhone() {
        return phone;
    }

    // set
    public void setCustId(int custId) {
        this.custId = custId;
    }

    public void setName(String name) {
        this.name = name;
    }

    public void setAddress(String address) {
        this.address = address;
    }

    public void setPhone(String phone) {
        this.phone = phone;
    }

    @Override
    public String toString() {
        return "CustomerDto [custId=" + custId + ", name=" + name + ", address=" + address + ", phone=" + phone + "]";
    }
}


// --Main
List<CustomerDto> list = listCustomer();

// 생성이 모두 완료 됐고, for문을 통해 print 처리
for (CustomerDto dto : list) {
    System.out.println(dto);
}


// --listCustomer
public List<CustomerDto> listCustomer() {
    Connection con = DriverManager.getConnection(url, user, pwd);

    List<CustomerDto> list = new ArraysList<>();

    String selectSql = "SELECT * FROM Customer; ";
    PrepareStatement pstmt = con.prepareStatement(selectSql);
    ResultSet rs = pstmt.executeQuery();


    while (rs.next()) {
        // row로 읽은 1개 행의 데이터를 Customer Dto를 통해 인스턴스를 생성한다.
        CustomerDto dto = new Customer();
        dto.setCustomerId(rs.getInt("customerId"));
        dto.setName(rs.getString("name"));
        dto.setAddress(rs.getString("address"));
        dto.setPhone(rs.getString("phone"));

        // 인스턴스를 ArrayList에 추가한다.
        list.add(dto);
    }

    return list;
}
```

참고 사이트
- [1](https://ittrue.tistory.com/250)