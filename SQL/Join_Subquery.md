# JOIN, Subquery
- SQL에서 사용되는 JOIN, Subquery에 대한 설명을 기재한다.

<br>

## JOIN
- 한 테이블과 다른 테이블을 연결하여 결합하는 연산이다.
- 다른 단어로 카티션 프로덕트 연산이라고 정의한다.

<br>

## Code
```SQL
-- 기본
SELECT *
  FROM Customer, Orders;


-- 고객과 고객의 주문에 관한 데이터를 모두 조회
SELECT *
  FROM Customer c, Oreders o
  WHERE c.customerId = o.cutomerId


-- 고객 별로 주문한 모든 도서의 총판매액과, 고객별로 정렬
SELECT DISTINCT c.name, SUM(o.saleprice) -- 동명이인 중복 처리
  FROM Customer c, Orders o
  WHERE c.customerId = o.customerId
  GROUP BY c.name
  ORDER BY c.name;

SELECT DISTINCT c.name, SUM(o.saleprice) 
  FROM Customer c, Orders o
  WHERE c.customerId = o.customerId
  GROUP BY c.customerId -- GROUP BY 절에서 unique Id 사용 시 SELECT 절에 표현하지 않아도 된다.
  ORDER BY c.name;


-- 3개 이상 테이블 JOIN
SELECT *
  FROM Customer c, Orders o, Book b;


-- 고객의 이름과 고객이 주문한 도서의 이름 조회
SELECT c.name, b.bookname
  FROM Customer c, Orders o, Book b
  WHERE c.customerId = o.customerId 
    AND o.bookId = b.bookId;


-- 위 문법을 다음과 같이도 표현할 수 있다. (ASNI SQL JOIN)
SELECT c.name, b.bookname
  FROM Customer c INNER JOIN Orders o ON c.customerId = o.customerId
                        JOIN Book b ON o.bookId = b.bookId;
```

<br>

## Self JOIN
- 하나의 테이블(자신)을 대상으로 한 조인을 의미한다.
- 프로젝트 내의 공통 코드에서 자주 사용 된다.

<br>

## Code
```SQL
/*
예를 들어 employees 테이블이 있다고 가정해보자.
이 때 사원 번호(empId)와 직급과 해당 직원이 관리해야하는 (magId)가 하나의 행에 있다고 가정하는 것이다.
이 때 특정 사원의 이름을 가진 어떤 직원을 어떤 직원들이 관리하는 정보를 확인하고 싶다면, 직접 셀프 조인해야 한다.
*/
SELECT staff.*
  FROM employees staff, employees manager
  WHERE manager.name = '김춘자'
  AND staff.magId = manager.empId;

-- 즉, 1개의 테이블을 alias 를 이용하여 셀프 조인한 뒤에 매니저 이름이 김춘자고 이들을 관리하는 
-- staff.mgaId -> 관리하는 직원 id와 managerId가 같으면 요구사항을 충족하는 쿼리가 완성 되는 것이다.
```

<br>

## OUTER JOIN
- 외부조인이라고 정의하고, 특정 조건에 의해 출력 결과에는 포함되지 않지만 결과에 포함시키고 싶을 때 사용된다.
- 예를들어, "김춘배" 라는 고객은 책을 구매하지 않았지만, 쿼리 결과에 포함시키고 싶을 때 사용된다.

### OUTER JOIN 종류
- LEFT, RIGHT, FULL OUTER JOIN이 존재하지만, 해당 문서에서는 LEFT에 대해서만 기재한다.
  - LEFT가 자주 사용되기 때문

### LEFT OUTER JOIN
- 왼쪽 테이블 기준으로 했을 때 값이 관계가 없어도 출력 가능한 것으로 간주한다.

## Code
```SQL
-- 책을 구매한 유저들의 구매 총액을 출력하고자 할 때 책을 구매하지 않는 고객도 출력하고자 하는 경우
SELECT c.name, o.salePrice
  FROM Cutomer c LEFT OUTER JOIN Order o ON c.customerId = o.cutomerId;
  -- 여기서 "주문" 이란 것은 발생 했을 때 생기는 Data이기 때문에
  -- null값이 나오는 곳은 Customer Table이 되는 것이다.


-- 다른 예를 들어보자.
-- 휴가 테이블과 사원 테이블이 있을 때 휴가를 아직 가지 않은 사원을 조회하고자 할 때는?
SELECT e.name, v.isVaction
  FROM Employees e LEFT OUTER JOIN Vacation v ON e.emId = v.emId;
```

<br>

## Subquery
- 다른 테이블의 결과를 이용하여 다시 질의에 대한 결과를 만드는 것이다.
  - 부속 질의라고 정의한다.
- 메모리에 적재되기 때문에 보다 빠를 수 있지만, 일정 용량이 발생하면 메모리 자원 소비 때문에 메모리 공간 낭비가 발생한다.

## Code
```SQL
-- 책 중에서 가장 높은 금액인 책의 이름은?
-- Subquery로 표현
SELECT bookname
  FROM Book
  WHERE price IN (
              SELECT MAX(price) max_price
              FROM Book
 );

-- JOIN으로 표현
SELECT b1.bookname
  FROM Book b1
  JOIN ( 
    SELECT MAX(price) max_price
    FROM Book
  ) b2 ON b1.price = b2.max_price;


-- Subquery로 표현 2
SELECT *
  FROM Customer
  WHERE custid IN (
               SELECT custid FROM Orders WHERE bookid IN (
               SELECT bookid FROM Book WHERE publisher = '대한미디어'));

-- JOIN으로 표현 2
SELECT *
  FROM  Customer c, Orders o, Book b
  WHERE c.custiomerId = b.customerId
  AND   o.bookId = b.bookId
  AND   b.publisher = '대한미디어';


-- Subquery로 표현 3
SELECT b1.*
  FROM Book b1
  WHERE b1.price > (
            SELECT AVG(price) 
            FROM Book b2
            WHERE b1.publisher = b2.publisher
  );

-- JOIN으로 표현 3
SELECT b1.*
  FROM Book b1,
  JOIN (
        SELECT publisher, AVG(price) avg_price
        FROM Book 
        GROUP BY publisher
  ) avg_book
  ON b1.price > avg_book.avg_price
  AND b1.publisher = avg_book.publisher
```