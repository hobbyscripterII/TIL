# CRUD

### CREATE TABLE BOARD

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

### BoardEntity

```java
public class BoardEntity {
    private int iboard;
    private String title;
    private String ctnts;
    private String writer;
    private String createdAt;
    private String updatedAt;

    public int getIboard() {
        return iboard;
    }

    public void setIboard(int iboard) {
        this.iboard = iboard;
    }

    public String getTitle() {
        return title;
    }

    public void setTitle(String title) {
        this.title = title;
    }

    public String getCtnts() {
        return ctnts;
    }

    public void setCtnts(String ctnts) {
        this.ctnts = ctnts;
    }

    public String getWriter() {
        return writer;
    }

    public void setWriter(String writer) {
        this.writer = writer;
    }

    public String getCreatedAt() {
        return createdAt;
    }

    public void setCreatedAt(String createdAt) {
        this.createdAt = createdAt;
    }

    public String getUpdatedAt() {
        return updatedAt;
    }

    public void setUpdatedAt(String updatedAt) {
        this.updatedAt = updatedAt;
    }
}
```

### Connection Class

```java
public class MyConnection {
    private static final String URL = "jdbc:mariadb://localhost:3306/board_ver1";
    private static final String USER = "";
    private static final String PASSWORD = "";

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

## INSERT

### main

```java
public static void main(String[] args) {
        BoardEntity entity = new BoardEntity();
        entity.setTitle("INSERT TEST");
        entity.setCtnts("INSERT TEST입니다.");
        entity.setWriter("이주영");
        BoardDao.insBoard(entity);
    }
```

board의 정보를 담을 BoardEntity 객체를 생성한다. 해당 참조변수를 통해 setter 메소드를 호출한다. setter 메소드에 제목, 내용, 작성자를 입력한다. insert 쿼리문을 실행할 insBoard(entity) 메소드를 호출한다.

### DAO

```sql
// data access object
public class BoardDao {
    public static int insBoard(BoardEntity entity) {
        int result = 0;
        // 방법 1
//        String sql = String.format("INSERT INTO BOARD(TITLE, CTNTS, WRITER) VALUES('%s', '%s', '%s')", entity.getTitle(), entity.getCtnts(), entity.getWriter());
        // 방법 2
        String sql = "INSERT INTO BOARD(TITLE, CTNTS, WRITER) VALUES(?, ?, ?)";
        Connection c = null;
        PreparedStatement ps = null;
        try {
            c = MyConnection.getConnection();
            ps = c.prepareStatement(sql);
            ps.setString(1, entity.getTitle());
            ps.setString(2, entity.getCtnts());
            ps.setString(3, entity.getWriter());
            result = ps.executeUpdate();
        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            MyConnection.close(c, ps);
        }
        return result;
    }
}
```

Connection 클래스에 데이터베이스 연결을 위한 커넥션 클래스 메소드의 반환 값을 넣는다. PreparedStatement는 쿼리문을 실행시키는 기능을 갖고 있으며 Statement를 상속받은 인터페이스이다. executeUpdate는 select문을 제외한 insert, update, delete문에 사용된다. select문을 실행할 경우 executeUpdate가 아닌 executeQuery 메소드를 호출해야한다.

쿼리문을 실행하기 위해 prepareStatement의 인자 값에 쿼리문이 담긴 변수를 담고 각 `?` 에 ps.setString을 사용해 1번째 `?` 부터 값을 넣는다. 이후 executeUpdate를 호출해 쿼리문을 실행한다.

예외가 발생할 경우 catch문에서 해당 예외가 무엇인지 콘솔에 출력한다.

finally는 try문이 실행되든 catch문이 실행되든 무조건 실행되는 구문으로 해당 구문 내에 자원을 닫아주는 close를 호출한다. Connection, PrepareStatement와 같이 자원을 사용할 경우 close를 통해 닫아주면 리소스가 절약된다. 만약 close를 호출하지 않으면 쿼리문을 여러번 실행했을 경우 쓰레드가 넘쳐 예외가 발생하게 된다.(이 부분은 자습 시간에 구글을 통해 잠깐 확인 했었는데 ‘아 그렇구나’ 하고 넘기긴 했다. 대충 어떤 느낌인지만 알고있다)

### DELETE

### main

```java
public static void main(String[] args) {
        BoardEntity entity = new BoardEntity();
        entity.setIboard(2);
        System.out.printf("row = {%d}", BoardDao.delBoard(entity));
    }
```

### DAO

```java
public static int delBoard(BoardEntity entity) {
        int result = 0;
        String sql = "DELETE FROM BOARD WHERE IBOARD = ?";
        Connection c = null;
        PreparedStatement ps = null;
        try {
            c = MyConnection.getConnection();
            ps = c.prepareStatement(sql);
            ps.setInt(1, entity.getIboard());
            result = ps.executeUpdate();
        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            MyConnection.close(c, ps);
        }
        return result;
    }
```

### UPDATE

### main

```java
public static void main(String[] args) {
        BoardEntity entity = new BoardEntity();
        entity.setIboard(3);
        entity.setTitle("매운 돈코츠 라멘");
        entity.setCtnts("매운 돈코츠 라멘");
        entity.setWriter("큐산");
        System.out.printf("row = {%d}", BoardDao.updBoard(entity));
    }
```

### DAO

```java
public static int updBoard(BoardEntity entity) {
        int result = 0;
        String sql = "UPDATE BOARD SET TITLE = ?, CTNTS = ?, WRITER = ?, UPDATED_AT = NOW() WHERE IBOARD = ?";
        Connection c = null;
        PreparedStatement ps = null;
        try {
            c = MyConnection.getConnection();
            ps = c.prepareStatement(sql);
            ps.setString(1, entity.getTitle());
            ps.setString(2, entity.getCtnts());
            ps.setString(3, entity.getWriter());
            ps.setInt(4, entity.getIboard());
            result = ps.executeUpdate();
        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            MyConnection.close(c, ps);
        }
        return result;
    }
```

### SELECT(GET)

소제목에 `GET` 을 적은 이유는 전체를 가져온다는 뜻으로 적긴 했다. 아래에 `SELECT` 는 하나만 선택한다는 뜻으로 적었다.

### main

```java
public static void main(String[] args) {
        List<BoardEntity> list = BoardDao.selBoardList();
        System.out.printf("%s | %s | %s | %s\n", "글 번호", "제목", "글쓴이", "작성일");
        for (BoardEntity entity : list) {
            System.out.println(entity);
        }
    }
```

여기 forEach문에 entity를 호출하면 글 번호, 제목, 글쓴이, 작성일이 출력되는데 이것은 BoardEntity에서 `toString` 메소드를 호출해 멤버 필드의 값을 다음과 같이 작성한 것이다.(어떻게 출력되는지도 같이 첨부하면 좋을 것 같은데 내일 학원가면 수정할 것이다)

### toString()

```java
public class BoardEntity {
    private int iboard;
    private String title;
    private String ctnts;
    private String writer;
    private String createdAt;
    private String updatedAt;

		// GETTER , SETTER...

    @Override
    public String toString() {
        return iboard + " | " + title + " | " + ctnts + " | " + writer + " | " + createdAt + " | " + updatedAt;
    }
}
```

### DAO

```java
public static List<BoardEntity> selBoardList() {
        List<BoardEntity> list = new ArrayList();
        String sql = "SELECT IBOARD, TITLE, WRITER, CREATED_AT FROM BOARD";
        Connection c = null;
        PreparedStatement ps = null;
        ResultSet rs = null;
        try {
            c = MyConnection.getConnection();
            ps = c.prepareStatement(sql);
            rs = ps.executeQuery();
            while (rs.next()) { // 레코드를 가리킨다.
                int iBoard = rs.getInt("iboard");
                String title = rs.getString("title");
                String writer = rs.getString("writer");
                String createdAt = rs.getString("created_at");
                BoardEntity entity = new BoardEntity();
                entity.setIboard(iBoard);
                entity.setTitle(title);
                entity.setWriter(writer);
                entity.setCreatedAt(createdAt);
                list.add(entity);
            }
        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            MyConnection.close(c, ps, rs);
        }
        return list;
    }
```

### SELECT(SELECT)

### main

```java
public static void main(String[] args) {
        BoardEntity result = BoardDao.selBoardById(3);
        System.out.println(result);
    }
```

### DAO

```java
public static BoardEntity selBoardById(int iboard) {
        BoardEntity entity = new BoardEntity();
        String sql = "SELECT * FROM BOARD WHERE IBOARD = ?";
        Connection c = null;
        PreparedStatement ps = null;
        ResultSet rs = null;
        try {
            c = MyConnection.getConnection();
            ps = c.prepareStatement(sql);
            ps.setInt(1, iboard);
            rs = ps.executeQuery();
            if (rs.next()) {
                entity.setIboard(rs.getInt("iboard"));
                entity.setTitle(rs.getString("title"));
                entity.setCtnts(rs.getString("ctnts"));
                entity.setWriter(rs.getString("writer"));
                entity.setCreatedAt(rs.getString("created_at"));
                entity.setUpdatedAt(rs.getString("updated_at"));
            }
        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            MyConnection.close(c, ps, rs);
        }
        return entity;
    }
```

# close

### MyConnection.close(Connection c, PreparedStatement ps)

```sql
    // 자원을 안닫아주면 리소스를 많이 잡아먹는다.
    public static void close(Connection c, PreparedStatement ps) {
        if (ps != null) {
            try {
                ps.close();
            } catch (Exception e) {
                e.printStackTrace();
            }
        }

        if (c != null) {
            try {
                c.close();
            } catch (Exception e) {
                e.printStackTrace();
            }
        }
    }
```

위의 close 메소드는 select문을 제외한 insert, delete, update문을 실행할 때 사용되는 메소드이다. 만약 select문을 실행해야 하는 경우에는 `ResultSet` 에 쿼리문을 실행한 결과 값(데이터)를 담아야 하기 때문에 `ResultSet` 역시 닫아줘야 하는 자원이 된다. 따라서 이 때는 close 메소드에 파라미터가 1개 더 추가되므로 아래와 같이 작성할 수 있다.

```java
public static void close(Connection c, PreparedStatement ps, ResultSet rs) {
        if (rs != null) {
            try {
                rs.close();
            } catch (Exception e) {
                e.printStackTrace();
            }
        }
        close(c, ps);
    }
```
