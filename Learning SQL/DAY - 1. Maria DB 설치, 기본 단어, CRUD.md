### Maria DB 설치하기

[Maria DB 다운로드](https://mariadb.org/download/?t=mariadb&p=mariadb&r=11.2.1&os=windows&cpu=x86_64&pkg=msi&m=blendbyte)

로그인: mysql -uroot -p

데이터베이스 확인: show databases;

세션 생성: 신규 - 세션 이름 입력 - 암호 입력 - 저장

데이터베이스 생성: 세션 이름 오른쪽 클릭 - 새로 생성 - 데이터베이스

d: - 'cd sakila-db' - 'mysql -uroot -p' - 'source sakila-schema.sql'

### 데이터베이스

- 중앙집중적으로 데이터를 저장하는 공간
- 관계형 데이터베이스(RDB)

### 기본 단어

| 용어 | 정의 |
| --- | --- |
| 엔티티 | 데이터베이스에서 사용자들이 관심을 갖는 모든 요소(고객, 부품, 지리적 위치정보 등) |
| 열 | 테이블에 저장된 개별적인 데이터 조각 |
| 행(레코드, 튜플, 로우) | 엔터티 혹은 엔터티에 대한 작업 시 함께 동작하는 열의 집합 |
| 결과셋 | 비 지속적 테이블의 다른 이름으로 보통 SQL 쿼리의 결과물을 의미한다. |
| 기본 키, PK | 유니크(unique), 중복되지 않는, 유일한…, 테이블의 각 행에 대한 고유 식별자로 사용할 수 있는 하나 이상의 열, 레코드 를 구분하기 좋다. |
| 외래 키, FK | 다른 테이블에 있는 값을 참조하다, 다른 테이블에서 단일 행을 식별하는 데 사용할 수 있는 하나 이상의 열, 다른 테이블의 컬럼을 참조할 때 무조건 해당 컬럼의 데이터 타입이 일치해야 한다. ⭐ |
| 복합 키 | 2개 이상의 열이 결합하여 고유한 값을 가지는 기본 키, PK + PK |
| 자연 키 | 엔티티의 정보 중 고유한 값을 가져서 각 행마다 식별할 수 있는 의미를 가지는 열, 의미를 가지는 키 |
| 대리 키 | 엔터티에서 파생된 정보가 아닌 임의의 고유 식별자로 일련번호와 같은 가상의 값으로 기본 키의 역할을 하는 열, 아무 의미없는 중복되지 않는 키 |

### SQL

**S**tructured

**Q**uery

**L**anguage

| 종류 | 명령어 | 설명 |
| --- | --- | --- |
| DDL(정의어) | CREATE, ALTER, DROP | 테이블과 같은 데이터 구조를 정의한다. |
| DML(조작어) | SELECT, INSERT, UPDATE, DELETE | 테이블에 있는 데이터의 변형을 가하는 종류의 명령어이다. |
| DCL(제어어) | GRANT, ROLLBACK, SAVEPOINT | 트랜잭션의 시작과 종료 및 롤백에 사용되는 명령어이다. |

### Transaction(트랜잭션)

여러 작업을 하나의 작업으로 처리한다.

### 정규화

- 제 1정규화: 한 컬럼에는 두가지 값이 들어갈 수 없다.(농구, 야구…)
- 제 2정규화:
- 제 3정규화:

정규화를 하면 할수록 나중에 `JOIN` 할 때 리소스를 많이 잡아먹는다. 이 때, `역정규화` 를 실시하면 속도가 빨라진다.

### TABLE 생성하기

테이블 생성 `Ctrl + Shift + F9` - 새로고침 `F5` 한 이후에 `TABLE`을 확인할 수 있다.

```sql
CREATE TABLE CORPORATION (
	CORP_ID SMALLINT,
	NAME VARCHAR(30),
	CONSTRAINT PK_CORPORATION PRIMARY KEY (CORP_ID) -- PK_CORPORATION: 제약조건 이름
);
```

- `VARCHAR`
    - VARCHAR(30): 글자 저장 개수는 `30` 이다.
    - 메모리를 동적으로 사용한다.
- `CHAR`
    - 값을 넣지 않아도 데이터 타입 옆에 명시한 크기를 모두 사용한다.
    - 때문에 무조건 같은 길이를 가져야 할 때 유용하다.
    - 사용하는 수가 동일할 경우 CHAR을 사용한다.
    - 속도가 빠르다.
- `CONSTRAINT`
    - 제약사항(잘못된 값이 저장되는 것을 `방지`한다)
    - `PK` 생성 방법 3가지
        
        ```sql
        1. CONSTRAINT PK_CORPORATION PRIMARY KEY (CORP_ID)
        2. PRIMARY KEY (CORP_ID)
        3. CORP_ID SMALLINT PRIMARY KEY
        ```
        
        `PK` 를 생성할 때는 `PRIMARY KEY (CORP_ID)` 와 같이 사용하는 게 더 편리하다.
        

### TABLE 삭제하기

```sql
DROP TABLE CORPORATION;
```

### INSERT

```sql
INSERT INTO corporation(CORP_ID, NAME) VALUES(1, '곽경록');
```

### + 추가

다중으로 INSERT 할 때 다음과 같이 가능하다.

```sql
INSERT INTO favorite_food (PERSON_ID, FOOD) VALUES
(1, 'PIZZA'),
(1, 'COOKIES'),
(1, 'NACHOS');
```

### UPDATE

```sql
UPDATE corporation SET NAME = '레이첼' WHERE CORP_ID = 5;
```

### DELETE

```sql
DELETE FROM corporation WHERE CORP_ID = 5;
```

### 텍스트 데이터

| 자료형 | 최대 바이트 크기 |
| --- | --- |
| tinytext | 255 |
| text | 65,535 |
| mediumtext | 16,777,215 |
| longtext | 4,294,967,295 |

### 숫자 데이터

| 자료형 | 부호있는 정수 저장값의 범위 | 부호없는 정수 저장값의 범위 |
| --- | --- | --- |
| tinyint | -128 ~ 127 | 0 ~ 255 |
| smallint | -32,768 ~ 32,767 | 0 ~ 65,535 |
| mediumint | -8,388,608 ~ 8,388,607 | 0 ~ 16,777,215 |
| int | -2,147,483,648 ~ 2,147,483,647 | 0 ~ 4,294,967,295 |
| bigint | -2^{53} ~ 2^{53} - 1 | 0 ~ 2^{64} - 1 |

### 시간 자료형

| 자료형 | 기본 형식 | 저장 데이터 타입 |  |
| --- | --- | --- | --- |
| date | YYYY-MM-DD | 문자 | 날짜만 저장 가능하다. |
| datetime | YYYY-MM-DD HH:MI:SS | 문자 | 날짜 & 시간 둘 다 저장 가능하다. |
| timestamp | YYYY-MM-DD HH:MI:SS | 정수 | 날짜 & 시간을 정수 타입으로 저장한다. |
| year | YYYY | 문자 | 년도만 저장 가능하다. |
| time | HHH:MI:SS | 문자 | 시간만 저장 가능하다. |

### 체크 제약조건

```sql
EYE_COLOR CHAR(2) CHECK(EYE_COLOR IN ('BR' ,'BL', 'GR'))
```

특정 열에 대해 허용 가능한 값을 표시한다.

따라서, 다음과 같이 체크 제약조건에 위배되는 데이터를 입력했을 경우 에러가 발생한다.

```sql
INSERT INTO PERSON(PERSON_ID, FNAME, LNAME, EYE_COLOR, BIRTH_DATE) VALUES(1, 'WILLIAM', 'TURNER', 'RD', '1972-05-27')
```

### PRIMARY KEY

- 중복되지 않는, 유일한 값을 가진 키로 테이블의 각 행에 대한 `고유 식별자`로 사용할 수 있는 하나 이상의 열을 의마한다.

### FOREIGN KEY

- 특정 테이블에 있는 컬럼 중 선택한 컬럼의 값만 들어갈 수 있게 제약사항을 설정한다.
- FOREIGN KEY는 PRIMARY KEY, UNIQUE KEY만 적용 가능하다.

```sql
FOREIGN KEY (PERSON_ID) REFERENCES PERSON(PERSON_ID)
```

```sql
INSERT INTO PERSON(PERSON_ID, FNAME, LNAME, EYE_COLOR, BIRTH_DATE) VALUES(1, 'WILLIAM', 'TURNER', 'BL', '1972-05-27');
INSERT INTO PERSON(PERSON_ID, FNAME, LNAME, EYE_COLOR, BIRTH_DATE, STREET, CITY, STATE, COUNTRY, POSTAL_CODE) VALUES(2, 'SUSAN', 'SMITH', 'BR', '1975-11-02', '23 MAPLE ST.', ' ARLINGTON', 'VA', 'USA', '20220');
INSERT INTO FAVORITE_FOOD(PERSON_ID, FOOD) VALUES(3, '햄버거'); -- FK 위반
```

FAVORITE_FOOD 테이블의 PERSON_ID 컬럼은 PERSON 테이블의 PERSON_ID 컬럼에 있는 데이터만 넣을 수 있다. 만약 PERSON 테이블의 PERSON_ID에 없는 값을 넣으려고 할 경우 에러가 발생한다.

### 복합 키

```sql
PRIMARY KEY(PERSON_ID, FOOD)
```

```sql
INSERT INTO FAVORITE_FOOD(PERSON_ID, FOOD) VALUES(1, '떡볶이');
INSERT INTO FAVORITE_FOOD(PERSON_ID, FOOD) VALUES(1, '닭발');
INSERT INTO FAVORITE_FOOD(PERSON_ID, FOOD) VALUES(1, '닭발'); -- PK 위반
```

한 열에 PERSON_ID, FOOD의 값이 겹치더라도 행으로 봤을 때 PERSON_ID, FOOD의 값이 겹치지 않을 경우 데이터 등록이 가능하다.

### UPDATE

```sql
UPDATE PERSON SET STREET = '1225 TREMONT ST.', CITY = 'BOSTON', STATE = 'MA', COUNTRY = 'USA', POSTAL_CODE = '02138' WHERE PERSON_ID = 1;
```

- `WHERE` 절이 없을 경우 해당 테이블의 모든 컬럼의 값이 변경된다.

- `WHERE PERSON_ID = 1` : 해당 테이블의 `PK` 가 `1` 인 컬럼의 값을 변경한다.
