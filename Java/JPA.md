# JPA
- JPA 관련하여 설명을 기재한다.

<br>

## JPA란?
- Java App에서 JDBC API 요청 전 단계에 위치하여 DBMS를 관리하고 데이터를 조작하기 위한 자바 표준 인터페이스이다.
- ORM을 통해 객체 지향 프로그래밍을 지향할 수 있으며, 유지보수 및 개발이 용이해진다.

<br>

### Open Source
- Hibernate 오픈 소스를 통해 ORM 프레임 워크를 개발할 수 있다.
- [MVN Rep](https://mvnrepository.com/artifact/org.hibernate.orm/hibernate-core)

<br>

### 동작 원리
<img src = "https://github.com/user-attachments/assets/4166d0c2-8a9e-495a-b9ca-603bc9269220" width="500" height= "70%">

- Java App에서 JPA 사용 시 내부적으로 JDBC API를 호출하여 SQL을 호출하고, DBMS와 통신한다.
  - 다시 말하면, 직접 JDBC API를 사용하는 것이 아닌 JPA를 사용하는 것이다.

<br>

## JPA 환경 구성

<br>

### Persistence 구성
- JPA 사용 전에 환경 구성 해야하며 Java App 또는 XML 파일을 통해 구성이 가능하다.

<br>

### Code (persistence.xml)
- 기본적으로 IntelliJ 의 Maven Project를 사용한다면, META-INF 디렉토리 하위 경로에 배치해야한다.

```xml
// persistence.xml
<persistence xmlns="http://xmlns.jcp.org/xml/ns/persistence"
             xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
             xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/persistence
             http://xmlns.jcp.org/xml/ns/persistence/persistence_2_2.xsd"
             version="2.2">

    <persistence-unit name= "exmaple" transaction-type = "RESOURCE_LOCAL">

        <description>jpa basic</description>
        <provider>org.hibernate.jpa.HibernatePersistenceProvider</provider>
        <properties>
            <property name="hibernate.connection.driver_class" value="com.mysql.cj.jdbc.Driver"/>
            <property name="hibernate.connection.url" value="jdbc:mysql://localhost:3306/exmaple"/>
            <property name="hibernate.connection.username" value="root"/>
            <property name="hibernate.connection.password" value="1234"/>
        </properties>

    </persistence-unit></persistence>
```

<br>

### Entity 구성
- DB에 실제 존재하는 Entity를 구성하고 받아오려면, Entity class 선언해야한다.
- @Entity, @Table, @Id 등 어노테이션을 통해 선언할 수 있다.

<br>

### Code (Product)
```Java
// Product Entity
import jakarta.persistence.Entity;
import jakarta.persistence.Id;

@Entity
public class Product {

    @Id 
    private long id; // PK는 반드시 @Id를 선언해야 한다.
    private String name;

    public long getId() {
        return id;
    }

    public void setId(long id) {
        this.id = id;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }
}
```

<br>

### Main 구성
- 이제 구성한 JPA를 바탕으로 직접 테스트를 진행해보자.

<br>

### Code (Main)
```Java
// Main
import com.mycom.myapp.entity.Product;
import jakarta.persistence.EntityManager;
import jakarta.persistence.EntityManagerFactory;
import jakarta.persistence.Persistence;


public class Main {

    public static void main(String[] args) {

        EntityManagerFactory entityManagerFactory = Persistence.createEntityManagerFactory("exmple");
        EntityManager entityManager = entityManagerFactory.createEntityManager();

        try {
            // 트랜잭션을 시작하는 코드 
            entityManager.getTransaction().begin();

            // ----- 실제 작업 
            Product product = new Product(); // Entity 인스턴스를 생성한다.
            product.setId(2L); // SQL로 치면 INSERT 절이다.
            product.setName("Phone"); 
            entityManager.persist(product); // context 내부로 들어온다.
            // 작업 끝 ------

            // Commit을 통해 트랜잭션을 완료시킨다.
            entityManager.getTransaction().commit();
        } finally {
            entityManager.close();
        }
    }
}
```

<br>

> 즉, Entity class로 선언한 Product 객체를 갖고, SQL 문법 없이 INSERT를 한 것이다.  
EntityManagerFactory에서 xml에서 작성한 config를 적용하고,   
EntityManager를 통해 JPA에 접근하여 JDBC API가 작동 된 것이다.

<br>

## CRUD 과 JPQL
- 그럼 이제 JPQL을 통해 SELECT 문을 작성하고, JPA를 통해 Update, Delete를 해보자.
  - Insert는 위에서 진행했으므로 SKIP.

<br>

### Code (CRUD JPA)

```Java
// --- JPA 

// Update
{
    entityManager.getTransaction().begin();

    Product product1 = entityManager.find(Product.class, 1); // entity 객체와 pk 전달
    product1.setName("수정 닉네임");

    entityManager.getTransaction().commit();
}


// Update2
{
    entityManager.getTransaction().begin();

    Product product2 = new Product();
    product2.setId(2);
    product2.setName("홍길동");

    /*
    Merge는 다음과 같은 특징을 갖는다.
    해당 Id가 존재한다면 Update를 수행하고, 존재하지 않다면 새로운 인스턴스를 생성한다.
    즉, 존재 여부에 따라 생성 되거나 수정 되는 메소드이다.

    반대로, persist()는 예외를 발생시킨다.
    해당 Id가 존재한다면 Duplicate Error 를 반환한다.
    */ 

    entityManager.merge(product2)

    entityManager.getTransaction().commit();
}


// Delete
{
    entityManager.getTransaction().begin();

    Product product1 = entityManager.find(Product.class, 1);
    entityManager.remove(product1);

    entityManager.getTransaction().commit();    
}

```

<br>

### JPQL
- SELECT 절 같은 경우 JOIN 연산과 WHERE 구문이 많아지면 JPA로 한계가 있다.
  - SQL의 SELECT 문과 유사하기 때문에 작성이 용이하다.
- JPQL은 엔티티와 필드를 대상으로 작업하기 때문에 DB에서의 독립적인 쿼리를 작성할 수 있다.

<br>

### Code (SELECT JPQL)
```JAVA
// Typed SELECT Query
{
    String jpql = "SELECT p FROM Product p";

    // Product Entity 객체와 jpql을 전달하여 query에 할당한다.
    TypedQuery<Product> query = entityManager.createQuery(jpql, Product.class);

    // query.getResultList()를 통해 List로 결과를 반환 받는다.
    List<Product> productList = query.getResultList();

    // 출력
    for (Product product : productList) {
        System.out.println(product);
    }
}


// Named Parameter Query 
{
    String jpql = "SELECT p FROM Product p WHERE p.id = :id";
    TypedQuery<Product> query = entityManager.createQuery(jpql, Product.class);

    query.setParameter("id", 1); // :id => 해당 name에 값을 전달 해야한다.

    Product product = query.getSingleResult(); // 결과 1개 반환

    System.out.println(product);
}


// Named Parameter Query with LIKE
{
    String searchWord = "덤벨";
    String searchPattern = "%" + searchWord + "%";

    String jpql = "SELECT p FROM Product p WHERE p.name LIKE :searchPattern";
    TypedQuery<Product> query = entityManager.createQuery(jpql, Product.class);

    query.setParameter("searchPattern", searchPattern); // :id => 해당 name에 값을 전달 해야한다.

    List<Product> productList = query.getResultList();

    for (Product product : productList) {
        System.out.println(product);
    }
}
```