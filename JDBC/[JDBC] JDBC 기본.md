## JDBC란?

JDBC(Java DataBase Connectivity)는 자바에서 데이터베이스에 접속할 수 있도록 하는 자바 API이다. JDBC는 데이터베이스에서 자료를 쿼리하거나 업데이트하는 방법을 제공한다.


## JDBC 동작 흐름

자바 어플리케이션에서 JDBC API를 활용한다.

JDBC API를 활용하기 위해서는 JDBC 드라이버를 로딩하여 데이터베이스에 연결해야 한다.

JDBC 드라이버는 데이터베이스와 통신을 담당하는 인터페이스이고, 다양한 데이터베이스에 알맞는 드라이버를 제공한다.

![[fc-jdbc 동작 흐름.png]]


## JDBC API 사용 흐름

1. JDBC driver loading
	- 데이터베이스 벤더(MySQL, Oracle ...)에 맞는 드라이버 로딩
2. Connection
	- URL, 계정 정보를 기반으로 데이터베이스와 커넥션을 맺음
3. Statement, PreparedStatement
	- 실행할 쿼리 문을 정의
4. executeUpdate(), executeQuery()
	- 쿼리 문을 실행
5. ResultSet
	- 결과에 대한 데이터셋
6. close
	- ResultSet, Statement, Connection close