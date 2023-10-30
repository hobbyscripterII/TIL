### IN 연산자

- `OR`과 같은 역할을 한다.

```sql
CREATE TABLE PERSON (
	PERSON_ID SMALLINT UNSIGNED PRIMARY KEY,
	FNAME VARCHAR(20), -- `20자`까지 들어갈 수 있다.
	LNAME VARCHAR(20),
	EYE_COLOR CHAR(2) CHECK(EYE_COLOR IN ('BR' ,'BL', 'GR')),
	...
);
```

```sql
-- OR: 하나만 만족해도 값을 가져온다.
-- AND: 조건문이 모두 만족할 때 값을 가져온다.
SELECT * FROM address WHERE CITY_ID = 1 OR CITY_ID = 2;
SELECT * FROM address WHERE CITY_ID IN(1, 2); -- `IN`은 `OR`과 같은 효과를 지닌다.
```

### ORDER BY - 정렬

```sql
SELECT * FROM person ORDER BY PERSON_ID DESC; -- DESC: 내림차순 ASC: 오름차순(기본값)
```

- `DESC` : 내림차순
- `ASC` : 오름차순(ORDER BY절에 명시하지 않으면 기본으로 `ASC` 가 적용된다)

### DATE_FORMAT - 날짜 형식 포맷 함수

```sql
SELECT STR_TO_DATE('dec-21-1980', '%b-%d-%y'); -- 2019-12-21
SELECT NOW(); -- 2023-10-30 12:05:22

SELECT BIRTH_DATE, DATE_FORMAT(BIRTH_DATE, '%Y/%m/%d') FROM person;
```

⛔ 단일 함수를 사용할 때 함수명 옆에 `()` 를 띄워쓰면 에러가 발생한다.

### 쿼리문 작성 순서

- SELECT -> FROM -> WHERE -> GROUP BY -> HAVING -> ORDER BY…

- ORDER BY절은 WHERE절 앞에 올 수 없다. 와 같은 맥락…

```sql
SELECT *
FROM customer
WHERE ACTIVE = 1
GROUP BY STORE_ID
HAVING COUNT(*) >= 300
ORDER BY CUSTOMER_ID DESC;
```

### SELECT

```sql
SELECT * FROM customer; -- *: 와일드 카드, 모든 컬럼을 포함한다.
SELECT FIRST_NAME, LAST_NAME, STORE_ID FROM CUSTOMER;
```

### AS - 별칭 지정

```sql
SELECT LANGUAGE_ID, NAME AS 'MY_NAME', 'COMMON' AS 'TYPE', 1 AS 'NUM' FROM language;
```

- `COMMON` 이라는 데이터가 각 행에 표시된다.
- `AS`: 임시로 컬럼명을 바꾼다.(생략 가능)
- `서브쿼리`에 별칭을 지정했을 경우 SELECT문에 별칭을 붙여야 한다.

### UPPER(대문자), LOWER(소문자) 변환

```sql
SELECT LANGUAGE_ID, NAME AS 'MY_NAME', 'COMMON' AS 'TYPE', 1 AS 'NUM', UPPER(NAME), LOWER(NAME) FROM language;
```

- UPPER: 문자열을 대문자로 변환한다.(한글 불가능)
- LOWER: 문자열을 소문자로 변환한다.(한글 불가능)

### DISTINCT - 데이터 중복 제거

```sql
SELECT DISTINCT LAST_NAME FROM ACTOR WHERE LAST_NAME = 'AKROYD';
```

- 현재 `출력`된 컬럼에서 중복된 데이터만 제거한다.

### FROM

- `FROM` 다음에는 `테이블명` 을 입력한다.

### SUB QUERY - 서브 쿼리, 파생 테이블

```sql
SELECT CONCAT(A.FIRST_NAME, ' ', A.LAST_NAME)
FROM (SELECT FIRST_NAME, LAST_NAME, EMAIL
		  FROM customer
		  WHERE FIRST_NAME = 'JESSIE'
		  ) A;
```

- 서브 쿼리는 속도가 느리기 때문에 선택 가능하다면 사용을 안하는 편이 좋다.

### CONCAT - 문자열 합치기

```sql
SELECT CONCAT(A.FIRST_NAME, ' ', A.LAST_NAME) AS FULL_NAME
FROM (SELECT FIRST_NAME, LAST_NAME, EMAIL
		  FROM customer
		  WHERE FIRST_NAME = 'JESSIE'
		  ) A;
```

### TEMPORARY TABLE - 임시 테이블

```sql
CREATE TEMPORARY TABLE ACTORS_J
(
	ACTOR_ID SMALLINT(5),
	FIRST_NAME VARCHAR(45),
	LAST_NAME VARCHAR(45)
);

INSERT INTO actors_j SELECT ACTOR_ID, FIRST_NAME, LAST_NAME FROM actor;

SELECT * FROM actors_j;
```

- INSERT에 SELECT문도 사용이 가능하다.
- `깊은 복사`와 맥락이 같다.(다른 공간이지만 같은 내용을 복사해 `INSERT`한다)
- 연결 해제 후 다시 접속하면 테이블이 삭제된다.

### VIEW - 가상 테이블

```sql
CREATE VIEW VIEW_CUSTOMER_1 AS
SELECT CUSTOMER_ID, FIRST_NAME, LAST_NAME, ACTIVE
FROM customer;

SELECT * FROM VIEW_CUSTOMER_1;
```

- 가상 테이블
- 데이터 딕셔너리에 저장된 쿼리
- 테이블처럼 동작하지만 뷰에 저장된 데이터가 존재하지는 않다.
- 뷰에 대해 쿼리를 실행하면 쿼리가 뷰 정의와 합쳐져서 최종 쿼리를 만들어낸다.

```sql
-- CREATE VIEW VIEW_CUSTOMER_1 AS
SELECT CUSTOMER_ID, FIRST_NAME, LAST_NAME, ACTIVE
FROM customer
WHERE CUSTOMER_ID < 10;

SELECT * FROM VIEW_CUSTOMER_1
WHERE CUSTOMER_ID < 10;
```

결과물이 똑같다.

### JOIN - 테이블 연결

```sql
SELECT A.FIRST_NAME, A.LAST_NAME, FA.ACTOR_ID, FA.FILM_ID, F.TITLE
FROM film_actor FA
JOIN actor A
ON FA.actor_id = A.ACTOR_ID
JOIN film F
ON FA.FILM_ID = F.FILM_ID
ORDER BY FA.ACTOR_ID;
```
