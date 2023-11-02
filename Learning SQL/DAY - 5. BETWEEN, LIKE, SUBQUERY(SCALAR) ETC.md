### AND & OR

```sql
SELECT *
FROM customer
WHERE FIRST_NAME = 'JAMIE' AND STORE_ID = 1; -- AND: 둘 다 만족해야한다.

SELECT *
FROM customer
WHERE FIRST_NAME = 'JAMIE' OR STORE_ID = 1; -- OR: 둘 중에 하나라도 만족하면 된다.
```

### <>은 ‘≠’과 같다.

```sql
SELECT *
FROM customer
-- WHERE (FIRST_NAME = 'STEVEN' OR LAST_NAME = 'YOUNG') -- <>은 '!='과 같다.(NOT)
WHERE (FIRST_NAME = 'STEVEN' OR LAST_NAME = 'YOUNG')
AND CREATE_DATE > '2006-01-01';
```

### MISSION

```sql
-- MISSION.1 CUSTOMER에서 동명이인(FIRST_NAME)이 있는 사람만 출력하시오.
SELECT FIRST_NAME, COUNT(FIRST_NAME) AS CNT -- FIRST_NAME의 카운트를 센다.
FROM customer
GROUP BY FIRST_NAME -- FIRST_NAME을 그룹화한다.
HAVING CNT > 1; -- CNT가 `1`보다 클 경우 동명이인이기 때문에 HAVING절을 통해 동명이인을 출력한다.

-- MISSION.2 RENTAL 테이블에서 렌탈 년도가 `2004`년도인 렌탈 정보를 출력하시오.
SELECT RENTAL_DATE, YEAR(RENTAL_DATE)
FROM rental
WHERE YEAR(RENTAL_DATE) = 2004;

-- MISSION.3 RENTAL 테이블에서 렌탈 년도가 `2005`년도가 아니고 `2006`년도가 아닌 레코드를 출력하시오.
SELECT RENTAL_DATE, YEAR(RENTAL_DATE)
FROM rental
WHERE YEAR(RENTAL_DATE) != 2005 AND YEAR(RENTAL_DATE) != 2006;

-- MISSION.5 위의 문제를 `IN` 연산자로 변경하시오.
SELECT *
FROM rental
WHERE !(YEAR(RENTAL_DATE) IN (2005, 2006));

SELECT *
FROM rental
WHERE YEAR(RENTAL_DATE) NOT IN(2005, 2006);

-- MISSION.4 RENTAL 테이블에서 `2005-05-25` 레코드만 출력하시오.
SELECT *
FROM rental
WHERE RENTAL_DATE = '2005-05-25';

-- MISSION.6 RENTAL 테이블에서 `2005-06-14 ~ 2005-06-15`만 출력하시오.
SELECT *
FROM rental
WHERE DATE(RENTAL_DATE) >= '2005-06-14' AND DATE(RENTAL_DATE) <= '2005-06-15';
```

### BETWEEN

- **WHERE ‘컬럼명’ `BEETWEN` ’MIN VALUE’ `AND` ’MAX VALUE’**

```sql
SELECT *
FROM rental
-- WHERE RENTAL_DATE BETWEEN '2005-06-14' AND '2005-06-16'; -- `2005-06-16 00:00:00`까지는 포함된다. 따라서 아래와 같이 작성하는 게 좋다.
WHERE DATE(RENTAL_DATE) BETWEEN '2005-06-14' AND '2005-06-16';
```

### MISSION

```sql
SELECT *
FROM PAYMENT
WHERE AMOUNT BETWEEN 10.0 AND 11.99;

SELECT *
FROM customer
WHERE LAST_NAME BETWEEN 'FA' AND 'FR';
```

### LIKE

- `=` : 완전 일치해야 한다.
- `LIKE`
    - ~와 같은, `%` 을 사용한다.
    - PET% : PET으로 시작하는
    - %PET: PET으로 끝나는
    - %PET%: PET이 포함된

```sql
SELECT RATING
FROM film
WHERE TITLE LIKE '%PET%';

SELECT TITLE, RATING
FROM film
WHERE RATING IN(SELECT RATING FROM film WHERE TITLE LIKE '%PET%');
```

### 서브쿼리 & 스칼라 서브쿼리

```java
-- SUBQUERY
SELECT TITLE, RATING
FROM film
WHERE RATING IN(SELECT DISTINCT RATING FROM film WHERE TITLE LIKE '%PET%');

-- SCALAR SUBQUERY
SELECT FILM_ID, TITLE, RATING
FROM film
WHERE RATING = (SELECT DISTINCT RATING FROM film WHERE TITLE LIKE 'PET%');

-- 위와 같은 출력 결과가 나온다.
SELECT TITLE, RATING
FROM film
WHERE RATING IN ('G', 'PG');
```

- `SUBQUERY(서브쿼리)` : 서브쿼리는 WHERE절에서 사용된다. 서브쿼리의 결과가 단일 행이면 단일행 서브쿼리, 복수행이면 복수행 서브쿼리라 부른다.
- `SCALAR SUBQUERY(스칼라 서브쿼리)` : 스칼라 서브쿼리는 SELECT절에서 사용된다. SCALAR는 `한번에 한가지만 처리하는` 이란 뜻을 가지고 있다. 즉, 스칼라 서브쿼리에 의해 나오는 결과는 무조건 `하나의 행` 이여야 한다.

출저: [SUBQUERY 종류 - 스칼라 서브쿼리, 인라인 뷰, 서브쿼리](https://m.blog.naver.com/writer0713/222277186069)

### 문자열 함수(LEFT, RIGHT, MID)

```sql
SELECT LAST_NAME, LEFT(LAST_NAME, 2), RIGHT(LAST_NAME, 2), MID(LAST_NAME, 3, 2)
FROM customer;
```

- `LEFT`: 왼쪽 문자열부터 자른다. 1이면 첫번째 문자만 출력된다.
- `RIGHT`: 오른쪽 문자열부터 자른다. 1이면 첫번째 문자만 출력된다.
- `MID`: 지정한 길이부터 문자열을 자른다. 3, 2이면 세번째 자리에서 2글자만 출력된다.

### MISSION

```sql
-- MISSION.8 고객 성(LAST_NAME) 중에 `Q`로 시작하는 사람의 레코드를 출력하시오.
SELECT *
FROM customer
WHERE LAST_NAME LIKE 'Q%';
```

### 와일드 카드(_, %)

- `_`: 한자리를 차지한다.
- `%`: 0 ~ 무한대 자리를 차지한다.

```sql
SELECT *
FROM customer
WHERE LAST_NAME LIKE '_A_T%S';
```

- 두번째 자리와 네번째 자리에는 무조건 `A`와 `T`가 와야한다.
- 이후엔 상관없으며 마지막 글자는 무조건 `S`여야한다.

참고로 `F` 일 경우 `F%` 는 true, `F_` 는 false이다. `_` 는 무조건 자리를 차지해야 하기 때문이다.

### NULL, IS NOT NULL

```sql
SELECT * FROM rental
WHERE RETURN_DATE IS NULL;
```

- `IS NULL`: `NULL`을 찾는다. NULL에만 허용된다.

```sql
SELECT * FROM rental
WHERE RETURN_DATE IS NOT NULL;
```

- `IS NOT NULL`: `NULL`이 아닌 값을 찾는다. NULL에만 허용된다.

### MISSION

```sql
-- MISSION.8 반납하지 않은 고객의 PK, 풀네임, 빌려간 날짜를 출력하시오.
-- 방법.1
SELECT R.RENTAL_ID, R.customer_id, CONCAT(C.FIRST_NAME, " ", C.LAST_NAME), R.RENTAL_DATE
FROM rental R
JOIN customer C
ON R.CUSTOMER_ID = C.CUSTOMER_ID
WHERE R.return_date IS NULL;
-- 방법.2
SELECT R.RENTAL_ID,R.customer_id, CONCAT(C.FIRST_NAME, " ", C.LAST_NAME), R.RENTAL_DATE
FROM rental R, customer C
WHERE R.CUSTOMER_ID = C.CUSTOMER_ID AND R.return_date IS NULL;
```

### 실습(P.126)

```sql
-- 1. 101, 107
SELECT *
FROM payment
WHERE PAYMENT_ID BETWEEN 101 AND 120 AND CUSTOMER_ID != 5 AND (AMOUNT > 8 OR DATE(PAYMENT_DATE) = '2005-08-23');

-- 2. 13
SELECT *
FROM payment
WHERE PAYMENT_ID BETWEEN 101 AND 120 AND CUSTOMER_ID = 5 AND NOT (AMOUNT > 6 OR DATE(PAYMENT_DATE) = '2005-06-19');

-- 3. 53, 60, 107, 155, 163, 267, 354
SELECT *
FROM payment
WHERE AMOUNT IN(1.98, 7.98, 9.98);

-- 4.
SELECT *
FROM customer
WHERE LAST_NAME LIKE('_AW%');
```

### 데카르트 곱

```sql
SELECT COUNT(*) FROM customer; -- 599
SELECT COUNT(*) FROM address; -- 603
SELECT COUNT(*)
FROM customer C
JOIN address A; -- 361,197
```

실무에서 사용하지도 않을 뿐더러 이렇게 쓰면 안된다는 것을 알려주셨다.

### 다중 테이블 쿼리

```sql
SELECT A.CUSTOMER_ID, B.ADDRESS_ID
FROM customer A
JOIN address B -- 교집합
ON A.ADDRESS_ID = B.ADDRESS_ID;

CREATE TABLE T_ADDRESS
(
	IADDRESS INT UNSIGNED AUTO_INCREMENT PRIMARY KEY,
	NAME VARCHAR(10) NOT NULL
);

INSERT INTO T_ADDRESS(NAME) VALUES('대도시'), ('도시'), ('시골');

CREATE TABLE T_CUSTOMER
(
	ICUSTOMER INT UNSIGNED AUTO_INCREMENT PRIMARY KEY,
	NAME VARCHAR(10) NOT NULL,
	MSG VARCHAR(30),
	IADDRESS INT UNSIGNED NOT NULL,
	FOREIGN KEY(IADDRESS) REFERENCES T_ADDRESS(IADDRESS)
);

CREATE TABLE T_CUSTOMER2
(
	ICUSTOMER INT UNSIGNED AUTO_INCREMENT PRIMARY KEY,
	NAME VARCHAR(10) NOT NULL,
	MSG VARCHAR(30),
	IADDRESS INT UNSIGNED NOT NULL
);

INSERT INTO T_CUSTOMER(NAME, IADDRESS) VALUES('곽경록', 1), ('김경현', 2), ('김동하', 3), ('김태형', 1), ('김현빈', 2);
INSERT INTO T_CUSTOMER2(NAME, IADDRESS) VALUES('곽경록', 1), ('김경현', 2), ('김동하', 3), ('김태형', 1), ('김현빈', 2), ('김현수', 4), ('김현일', 5);

DELETE FROM T_CUSTOMER WHERE ICUSTOMER > 5;
DROP TABLE T_CUSTOMER2;

-- `PK - FK` JOIN
SELECT *
FROM T_CUSTOMER C
JOIN T_ADDRESS A
ON C.IADDRESS = A.IADDRESS;

-- `PK - 일반 컬럼` JOIN
-- IADDRESS에 `4`, `5`번이 없으므로 `4`, `5`를 갖고있는 컬럼은 출력되지 않는다.
SELECT *
FROM T_CUSTOMER2 C
JOIN T_ADDRESS A
ON C.IADDRESS = A.IADDRESS;

-- 왼쪽 테이블에 있는 T_ADDRESS의 데이터는 모두 출력된다.(값이 없다면 `NULL`로 출력된다)
-- LEFT JOIN
SELECT *
FROM T_CUSTOMER2 C
LEFT JOIN T_ADDRESS A
ON C.IADDRESS = A.IADDRESS;

SELECT *, IFNULL(A.NAME, '') AS NAME
FROM T_CUSTOMER2 C
LEFT JOIN T_ADDRESS A
ON A.IADDRESS = C.IADDRESS;
```

**📌 `FK`랑 `JOIN`이랑 관계가 있지만 FK가 안걸려있다고 해서 JOIN이 안되는 건 아니다.**
