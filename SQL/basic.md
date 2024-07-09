# SQL Basic
- SQL 관련 기본 내용에 대해 기재한다.

<br>

## SQL이란?
- 기능에 따라 DDL, DML, DCL로 분리할 수 있다.

### DDL (Data Definition Language)
- 데이터 정의 언어로 CREATE, DROP, ALTER 등 스키마의 관계와 구조를 생성하는 언어이다.

### DML (Data Manipulation Language)
- 데이터 모델 언어로 SELECT, WHERE, INSERT 등 검색, 삭제, 조회하는데 필요한 언어이다.
- 여기서 특히 SELECT 문은 질의어라고 표현된다.

### DCL (Data Contorl Language)
- 데이터 사용 권한을 관리할 수 있는 언어이다. 
  - REVOKE, GRANT ..

<br>

> 해당 문서는 DML에 초점을 맞추어 글을 작성한다.

<br>

## SELECT / FROM / WHERE
- 기본 명령어 3가지에 대해 설명한다.

<br>

### SELECT
- 질의 결과가 추출 되는 컬럼 리스트를 열거한다.

### WHERE
- 어떤 테이블을 사용할지 열거한다.

### WHERE
- 질의의 조건을 작성한다.

<br>

## Code
```SQL
-- Customer Table의 모든 컬럼을 조회한다.
SELECT *
  FROM Customer;


-- Book Table의 bookname 컬럼만 조회한다.
SELECT bookname
  FROM Book;


-- Book Table의 publisher의 컬럼에 중복 값을 제외한다.
SELECT DISTINCT publisher
  FROM Book;


-- Book Table에서 가격이 20000이하인 책들만 조회한다.
SELECT *
  FROM Book
  WHERE price < 20000;


-- Book Table에서 가격이 20000이하인 책과 출판사가 굿스포츠인 곳만 조회한다.
SELECT *
  FROM Book
  WHERE price < 20000 AND publisher = '굿스포츠';
```

<br>

## BETWEEN / IN, NOT IN / LIKE / ORDER BY

<br>

### BETWEEN
- 피연산자 2개의 사이 값을 추출할 수 있다.

### IN, NOT IN
- 2개 이상의 값을 비교할 때 사용되며, 집합의 원소인지 판단하는 연산자이다.

### LIKE
- 문자열 패턴을 검색할 수 있다. % 를 통해서 비교 가능하다.

### ORDER BY
- 특정 컬럼을 기준으로 정렬할 수 있다. 오름차순(ASC)가 Defalut이며, DESC로 설정 가능하다.

<br>

```SQL
-- 책 테이블에 가격 1000원 이상 2000원 이하
SELECT *
  FROM Book
  WHERE price BETWEEN 1000 AND 2000;


-- 책 테이블에 카테고리가 인문, 경제만 포함된 책
SELECT *
  FROM Book
  WHERE category IN ('인문', '경제');


-- 책 테이블에 카테고리가 인문, 경제를 제외한 모든 책
SELECT *
  FROM Book
  WHERE category NOT IN ('인문', '경제');


-- % 규칙
SELECT *
  FROM Book
  WHERE bookName LIKE '%사랑' -- 앞에 뭐가 오든 사랑으로 끝나는 단어
                 LIKE '사랑%' -- 사랑으로 시작하고 뒤에 뭐가오든 상관없는 단어
                 LIKE '%사랑%' -- 사랑이 포함되어 있는 단어


-- 출판일 기준으로 오름차순 정렬
SELECT *
  FROM Book
  ORDER BY publishDate;
  -- 내림차순
  ORDER BY publishDate DESC;
  -- 출판일이 같을 경우, 책 이름으로 오름차순 정렬
  ORDER BY publishDate DESC, bookName
```

<br>

## 집계 함수 / GROUP BY / HAVING

<br>

### 집계 함수
- 어떤 테이블의 특정 컬럼의 값을 계산하는 함수이다.
- SUM, AVG, MAX, MIN, COUNT이 존재한다.

```SQL
-- 주문 테이블의 총합 매출액
SELECT SUM(salePrice) as 총매출
  FROM Orders;


-- 주문 테이블의 2번 Id 고객의 매출 평균
SELECT AVG(salePrice)
  FROM Orders
  WHERE cutomerId = 2;
```

<br>

### GROUP BY
- 속성 값이 같은 값끼리 그룹을 만들 수 있다.
- 즉, 특정 값이 같은 것끼리 그룹을 묶어 SELECT 절로 표현할 수 있다.

```SQL
-- 고객 별로 주문한 도서의 총수량과 총판매액
SELECT customerId, Count(*) as 수량
       SUM(salePrice) * 총합
  FROM Orders
  GROUP BY customerId; -- GRUOP BY로 지정한 속성 값만 SELECT 절에서 사용 가능하다.


-- 고객 Id가 1,2 인 주문 횟 수
SELECT cutomerId, COUNT(*)
  FROM Orders
  WHERE customerId IN (1, 2)
  GROUP BY cutomerId;
```

<br>

### HAVING
- GROUP BY 결과에 나타나는 그룹에 대해 조건을 다시 제한하는 역할을 한다.

```SQL
-- 고객 별로 책을 8000원 이상 구매한 횟 수를 구하는데, 구매 횟수가 2이상인 고객 리스트
SELECT customerId, COUNT(*)
  FROM Orders
  WHERE salePrice >= 8000
  GROUP BY customerId
  HAVING COUNT(*) >= 2;`


 -- 책이름에 사랑이 포함되어 있고, 각 고객의 총구매액이 30000이상인 Count를 출력하고, 내림차순 정렬
SELECT customerId, COUNT(*)
  FROM Orders
  WHERE bookName LIKE '%사랑%'
  GROUP BY customerId
  HAVING SUM(salePrice) >= 30000
  ORDER BY customerId DESC;
```