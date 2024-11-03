## JDBC 반복 문제

JDBC Template을 보기 전에 JDBC 반복 문제를 보자.

**JDBC 반복 문제**
- 커넥션 조회, 커넥션 동기화
- `PreparedStatement` 생성 및 파라미터 바인딩
- 쿼리 실행
- 결과 바인딩
- 예외 발생 시 스프링 예외 변환기 실행
- 리소스 종료

JDBC로 코드를 만들면 각각의 메서드에 상당히 많은 부분이 반복된다. 이런 반복을 효과적으로 처리하는 방법이 바로 **템플릿 콜백 패턴**이다.

**Repository**에서 `JdbcTemplate`을 사용하면 된다.
- `save()`의 경우 `template.update(sql, param1, param2, ...)`로 바인딩까지하고, 객체를 반환하면 된다. (`return member;`)
- `findById()`의 경우 `RowMapper<Object>`를 사용해서 `ResultSet`에 들어가 있는 정보를 토대로 `new Member()`를 만들게 된다.

이렇게 완성된 코드는 다음과 같은 특징이 있다.

1. 서비스 계층의 순수성
	- 트랜잭션 추상화 + 트랜잭션 AOP 덕분에 서비스 계층의 순수성을 최대한 유지하면서 서비스 계층에서 트랜잭션을 사용할 수 있다.
	- 스프링이 제공하는 예외 추상화의 예외 변환기 덕분에, 데이터 접근 기술이 변경되어도 서비스 계층의 순수성을 유지하면서 예외도 사용할 수 있다.
	- `Service` 계층이 `Repository` 인터페이스에 의존한 덕분에 향후 `Repository`가 다른 구현 기술로 변경되어도 `Service` 계층을 순수하게 유지할 수 있다.
 2. `Repository`에서 JDBC를 사용하는 반복 코드가 `JDBC Template`으로 대부분 제거되었다.
