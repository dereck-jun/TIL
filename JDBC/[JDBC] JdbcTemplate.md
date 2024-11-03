## JdbcTemplate 이란?

JdbcTemplate은 JDBC 코어 패키지의 중앙 클래스로 JDBC의 사용을 단순화하고 일반적인 오류를 방지하는데 도움이 된다.

- JdbcTemplate
	- 순서 기반 파라미터 바인딩을 지원한다.
- NamedParameterJdbcTemplate
	- 이름 기반 파라미터 바인딩을 지원한다. (권장)
- SimpleJdbcInsert
	- insert SQL을 편리하게 사용할 수 있다.
- SimpleJdbcCall
	- 스토어드 프로시저를 편리하게 호출할 수 있다.

### 장점
- 설정의 편리함
	- JdbcTemplate은 `spring-data-jdbc` 라이브러리에 포함되어 있는데, 이 라이브러리는 스프링으로 JDBC를 사용할 때 기본으로 사용되는 라이브러리이다. 그리고 별도의 복잡한 설정 없이 바로 사용할 수 있다.
- 반복 문제 해결
	- JdbcTemplate은 템플릿 콜백 패턴을 사용해서, JDBC를 직접 사용할 때 발생하는 대부분의 반복 작업을 대신 처리해준다.
	- 개발자는 SQL을 작성하고, 전달할 파라미터를 정의하고, 응답 값을 매핑하기만 하면 된다.
	- 개발자가 생각할 수 있는 대부분의 반복 작업을 대신 처리해준다.
		- 커넥션 획득
		- `statement`를 준비하고 실행
		- 결과를 반복하도록 루프를 실행
		- 커넥션 종료, `statement`, `resultset` 종료
		- 트랜잭션을 다루기 위한 커넥션 동기화
		- 예외 발생 시 스프링 예외 변환기 실행

### 단점
- 동적 SQL을 해결하기 어렵다.

## 주요 기능
1. [조회](#조회)
2. [변경](#변경)
3. [기타 기능](#기타-기능)

### 조회
데이터를 조회할 땐 `jdbcTemplate.queryForObject()` 또는 `jdbcTemplate.query()`를 사용하면 된다.
- queryForObject(): 단건 조회
- query(): 목록 조회

#### 단건 조회 - 숫자 조회
```java
int rowCount = jdbcTemplate.queryForObject("select count(*) from t_actor", Integer.class());
```

하나의 로우를 조회할 때는 `queryForObject()`를 사용하면 된다. 조회 대상이 객체가 아니라 단순 데이터 하나라면 타입을 `Integer.class`, `String.class`와 같이 지정해주면 된다.

#### 단건 조회 - 숫자 조회, 파라미터 바인딩
숫자 하나와 파라미터 바인딩 예시
```java
int countOfActorsNamedJoe = jdbcTemplate.queryForObject(
	"select count(*) from t_actor where first_name=?",
	Integer.class, "Joe"
);
```

#### 단건 조회 - 문자 조회
문자 하나와 파라미터 바인딩 예시
```java
String lastName = jdbcTemplate.queryForObject(
	"select last_name from t_actor where id=?",
	String.class, 1212L
);
```

#### 단건 조회 - 객체 조회
```java
Actor actor = jdbcTemplate.queryForObject(
	"select first_name, last_name from t_actor where id=?",
	(rs, rowNum) -> {
		Actor newActor = new Actor();
		newActor.setFirstName(rs.getString("first_name"));
		newActor.setLastName(rs.getString("last_name"));
		return newActor;
	}, 1212L
);
```

객체 하나를 조회한다. 이때 결과를 객체로 매핑해야 하므로 `RowMapper`를 사용해야 한다. 여기서를 [[Lambda]]를 사용했다.

#### 목록 조회 - 객체 1
```java
List<Actor> actors = jdbcTemplate.query(
	"select first_name, last_name from t_actor",
	(rs, rowNum) -> {
		Actor actor = new Actor();
		actor.setFirstName(rs.getString("first_name"));
		actor.setLastName(rs.getString("last_name"));
		return actor;
	}
);
```

여러 row를 조회할 때는 `query()`를 사용하면 된다. 결과를 리스트로 반환한다. 결과를 객체로 매핑해야 하므로 `RowMapper`를 사용해야 한다. 여기서는 lambda를 사용했다.

#### 목록 조회 - 객체 2
```java
private final RowMapper<Actor> actorRM = (rs, rowNum) -> {
	Actor actor = new Actor();
	actor.setFirstName(rs.getString("first_name"));
	actor.setLastName(rs.getString("last_name"));
	return actor;
};

public List<Actor> findAllActors() {
	return this.jdbcTemplate.query("select first_name, last_name from t_actor", actorRM);
}
```

위와 똑같이 여러 row를 조회하는 코드이다. 하지만 여기서는 `RowMapper`를 분리해서 여러 곳에서 재사용이 가능하게 만들었다.

### 변경
데이터를 변경할 땐 `jdbcTemplate.update()`를 사용하면 된다. 참고로 `int` 반환 값을 반환하는데, 이는 SQL 실행 결과에 영향을 받은 row 수 를 반환한다.

#### 등록
```java
jdbcTemplate.update(
	"insert into t_actor (first_name, last_name) values (?, ?)", "Leonor", "Watling"
);
```

#### 수정
```java
jdbcTemplate.update(
	"update t_actor set last_name=? where id=?", 
	"Banjo", 5276L
);
```

#### 삭제
```java
jdbcTemplate.update(
	"delete from t_actor where id=?", 
	Long.valueOf(actorId)
);
```

### 기타 기능
임의의 SQL을 실행할 때는 `execute()`를 사용하면 된다. 테이블을 생성하는 DDL에 사용할 수 있다.

#### DDL
```java
jdbcTemplate.execute(
	"create table mytable (id integer, name varchar(30))"
);
```

#### 스토어드 프로시저 호출
```java
jdbcTemplate.update(
	"call SUPPORT.REFRESH_ACTORS_SUMMARY(?)",
	 Long.valueOf(unionId)
);
```

> **참고**
> 
> 스토어드 프로시저를 사용하기 위한 `SimpleJdbcCall` 에 대한 자세한 내용은 다음 [스프링 공식 메뉴얼](https://docs.spring.io/spring-framework/reference/data-access/jdbc/core.html#jdbc-JdbcTemplate)을 참고 하자.