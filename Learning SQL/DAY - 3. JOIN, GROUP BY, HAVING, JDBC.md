# JOIN, GROUP BY, HAVING

### JOIN - 테이블 연결

```sql
SELECT A.FIRST_NAME, A.LAST_NAME, FA.ACTOR_ID, FA.FILM_ID, F.TITLE
FROM film_actor FA -- 기준이 되는 테이블
JOIN actor A -- `JOIN`만 작성할 경우 자동적으로 `INNER JOIN`이 된다.
ON FA.actor_id = A.ACTOR_ID
JOIN film F
ON FA.FILM_ID = F.FILM_ID
ORDER BY FA.ACTOR_ID;
```

`INNER JOIN`(교집합): 서로 갖고있는 데이터만 출력한다.

`LEFT JOIN`(): 왼쪽에 있는 데이터는 출력이 보장된다.

### M:N (다대다 관계)

테이블이 3개 나와야한다.

배우 정보 테이블 1개, 영화 정보 테이블 1개..

배우 입장: 배우 1명은 여러 영화에 출연할 수 있다.

영화 입장: 영화는 여러 배우가 출연할 수 있다.

### MISSION

```sql
-- MISION: 영화에 출연한 모든 배우 200명을 출력해라.
-- ANSWER: FILM_ACTOR 테이블에 배우 아이디를 중복 제거한다.
SELECT DISTINCT ACTOR_ID FROM film_actor;
```

### PK의 의도

```sql
INSERT INTO FAVORITE_FOOD(PERSON_ID, FOOD) VALUES(1, '닭발');
```

- PERSON_ID: 한 사람은 하나의 음식만 선택할 수 있다.
- FOOD: 음식 하나를 한 사람이 독점할 수 있다.(닭발을 먹고 싶어도 다른 사람이 선택했으면 이후에 다른 사람은 닭발을 선택할 수 없다)

### MISSION

```sql
-- MISSION1: 영화 RATING 값이 'G'면서RENTAL_DURATION 7일 이상인 영화 리스트를 출력하시오.
-- MISSION2: 영화 제목만 나타내시오.
SELECT TITLE
FROM film
WHERE RATING = 'G' AND  RENTAL_DURATION >= 7;

-- MISSION3
-- 영화 RATING 값이 'G'면서RENTAL_DURATION 7일 이상인 영화와
-- RATING이 'PG-13'이면서 RENTAL_DURATION이 4일 미만인 영화 리스트를
-- TITLE, RATING, RENTAL_DURATION만 출력하시오.
SELECT TITLE, RATING, RENTAL_DURATION
FROM film
WHERE (RATING = 'G' AND  RENTAL_DURATION >= 7) OR (RATING = 'PG-13' AND RENTAL_DURATION < 4);
```

### GROUP BY

- 특정 컬럼을 `그룹화` 한다.

### HAVING

- 특정 컬럼을 그룹화한 결과에 `조건` 을 건다.

```sql
-- 그룹 함수 종류: SUM, MIN, MAX, COUNT, AVG...
-- SUM(AMOUNT): AMOUNT의 총계를 출력한다.
-- MAX(AMOUNT): AMOUNT의 가장 높은 값
-- MIN(AMOUNT): AMOUNT의 가장 낮은 값
-- COUNT(*): 그룹 개수, 전체 레코드의 개수((예) COUNT(PAYMENT_ID) 보통 `PK`를 작성한다.)
SELECT SUM(AMOUNT), MAX(AMOUNT), MIN(AMOUNT), COUNT(*), AVG(AMOUNT), SUM(AMOUNT) / COUNT(*) FROM payment;
```

```sql
-- (예) 고객마다 사용한 금액이 궁금하다.
-- GROUP BY: 한 그룹으로 출력한다.
SELECT CUSTOMER_ID 고객_아이디, SUM(AMOUNT) 금액_합계
FROM payment
GROUP BY CUSTOMER_ID -- 그룹(사용자)마다 사용한 AMOUNT가 출력된다.
ORDER BY 금액_합계 DESC;
```

```sql
-- (예) 고객마다 사용한 금액 중 사용 금액이 `100달러` 이상인 고객만 보고싶다.
SELECT CUSTOMER_ID 고객_아이디, SUM(AMOUNT) 금액_합계
FROM payment
GROUP BY CUSTOMER_ID
HAVING 금액_합계 >= 100
ORDER BY 금액_합계;
```

### MISSION

```sql
-- MISSION3
-- 각 스탭의 매출 금액 합계와 매출 금액 평균을 출력하시오.
SELECT STAFF_ID 스탭_아이디, SUM(AMOUNT), AVG(AMOUNT)
FROM payment
GROUP BY STAFF_ID;
```

```sql
-- MISSION4
-- 2005년 6월 14일에 대여한 회원의 FIRST_NAME, LAST_NAME, RENTAL_TIME을 출력하시오.
SELECT C.first_name, C.LAST_NAME, TIME(R.RENTAL_DATE)
FROM rental R
JOIN customer C
ON R.CUSTOMER_ID = C.CUSTOMER_ID
WHERE DATE(R.RENTAL_DATE) = '2005-06-14';
```

### 학습 점검

```sql
-- Q1(P.101)
SELECT ACTOR_ID, FIRST_NAME, LAST_NAME
FROM ACTOR
ORDER BY LAST_NAME, FIRST_NAME DESC;

-- Q2
SELECT ACTOR_ID, FIRST_NAME, LAST_NAME
FROM ACTOR
WHERE LAST_NAME = 'WILLIAMS' OR LAST_NAME = 'DAVIS';

-- Q3
SELECT C.EMAIL, R.RETURN_DATE
FROM customer C
JOIN rental R
ON C.CUSTOMER_ID = R.CUSTOMER_ID
WHERE DATE(R.RENTAL_DATE) = '2005-06-14'
ORDER BY R.RETURN_DATE DESC;
```

# JDBC

### 1. Maria DB 의존성 설정하기(pom.xml)

```xml
        <dependency>
            <groupId>org.mariadb.jdbc</groupId>
            <artifactId>mariadb-java-client</artifactId>
            <version>3.2.0</version>
        </dependency>
```

### 2. board_ver1 데이터베이스 생성

### 3. BOARD 테이블 생성

```sql
CREATE TABLE BOARD
(
	IBOARD INT UNSIGNED AUTO_INCREMENT PRIMARY KEY,
	TITLE VARCHAR(100) NOT NULL,
	CTNTS VARCHAR(100) NOT NULL,
	WRITER VARCHAR(30) NOT NULL,board
	CREATED_AT DATETIME DEFAULT NOW(), -- 기본값으로 현재 시간이 설정된다.
	UPDATED_AT DATETIME DEFAULT NOW()
);
```

### 4. Database Connection Class

```java
public class MyConnection {
    private static final String URL = "jdbc:mariadb://localhost:3306/sakila";
    private static final String USER = "root";
    private static final String PASSWORD = "green502";

    public static Connection getConnection() {
        Connection c = null;
        try {
            Class.forName("org.mariadb.jdbc.Driver");
            c = DriverManager.getConnection(URL, USER, PASSWORD);
        } catch (Exception e) {
            e.printStackTrace();
        }
        return c;
    }
}
```

- `Connection` : 데이터베이스와 연결하는 객체
- `Class.forName` : 해당 Driver Class를 로드한다.
- `DriverManager.getConnection` : Connection 객체를 생성해 데이터베이스 연결을 얻는다.
- (org.mariadb.jdbc는 패키지이다)

### 5. Database Connection Test

```java
public class MyConnectionTest {
    public static void main(String[] args) {
        try {
            Connection c = MyConnection.getConnection();
            String sql = "INSERT INTO COUNTRY(COUNTRY) VALUES('경현 나라')";
            PreparedStatement ps = c.prepareStatement(sql);
            int result = ps.executeUpdate();
            System.out.printf("result = {%d}", result);
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
}
```

- `PreparedStatement` : 쿼리문을 실행한다.
- `executeUpdate` : 쿼리를 실행했을 때 적용된 행의 개수를 리턴(int)한다.

### INSERT

1. `DAO` 작성
    
    ```java
    // data access object
    public class BoardDao {
        public static int insBoard(BoardEntity entity) {
            int result = 0;
            // 방법 1
    //        String sql = String.format("INSERT INTO BOARD(TITLE, CTNTS, WRITER) VALUES('%s', '%s', '%s')", entity.getTitle(), entity.getCtnts(), entity.getWriter());
            // 방법 2
            String sql = "INSERT INTO BOARD(TITLE, CTNTS, WRITER) VALUES(?, ?, ?)";
            try {
                Connection c = MyConnection.getConnection();
                PreparedStatement ps = c.prepareStatement(sql);
                ps.setString(1, entity.getTitle());
                ps.setString(2, entity.getCtnts());
                ps.setString(3, entity.getWriter());
                result = ps.executeUpdate();
            } catch (Exception e) {
                e.printStackTrace();
            }
            return result;
        }
    }
    ```
    
2. insBoard(BoardEntity) 호출
    
    ```java
    class MyConnectionTest2 {
        public static void main(String[] args) {
            BoardEntity entity = new BoardEntity();
            entity.setTitle("두번째 작성");
            entity.setCtnts("두번째 작성하는 내용입니다.");
            entity.setWriter("남길동");
            BoardDao.insBoard(entity);
        }
    }
    ```
