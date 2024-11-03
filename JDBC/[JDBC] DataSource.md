## DataSource란?

**데이터베이스와 관계된 커넥션 정보**를 담고 있으며 Bean으로 등록하여 인자로 넘겨준다. 해당 과정을 통해 **Spring은 DataSource로 데이터베이스와의 연결을 획득**한다.
- DB 서버와의 연결을 해준다.
- DB Connection pooling 기능 

DataSource를 사용하지 않으면 매번 데이터베이스 설정 정보(url, username, password 등)을 전달해야 커넥션을 가져올 수 있다.

## DataSource 이해
### DriverManager -> Connection Pool 변경 시 문제

애플리케이션 로직에서 `DriverManager` 를 사용해서 커넥션을 획득하다가 `HikariCP` 같은 커넥션 풀을 사용하도록 변경하면 커넥션을 획득하는 애플리케이션 코드도 함께 변경해야 한다.
- 의존관계가 `DriverManager` -> `HikariCP` 로 바뀌기 때문

### 커넥션을 획득하는 방법을 추상화

`DriverManager` 는 커넥션을 획득할 때마다 파라미터들을 계속 전달해야 했다. 

반면에 `DataSource` 를 사용하는 방식은 처음 객체를 생성할 때만 필요한 파라미터를 넘겨두고, 커넥션을 획득할 때는 단순히 `getConnection()` 만 호출하면 된다.

결론적으로 위의 문제를 해결하는 방법은 `DataSource` 에 있는 `getConnection()` 을 **추상화해서 설정과 사용을 분리하는 것**이다.
- 자바에서는 이런 문제 해결을 위해 `javax.sql.DataSource` 인터페이스를 제공한다.
- `DataSource` 는 **커넥션을 획득하는 방법을 추상화**하는 인터페이스이다.
- 해당 인터페이스의 핵심 기능은 **커넥션 조회** 하나이다. (다른 일부 기능도 존재는 하나 중요 X)

## 정리

- 대부분의 커넥션 풀은 `DataSource` 인터페이스를 이미 구현해두었다. 따라서 **개발자는 커넥션 풀의 코드**를 직접 의존이 아닌 `DataSource` **인터페이스에만 의존하도록 애플리케이션 로직을 작성**하면 된다.
- 커넥션 풀 구현 기술을 변경하고 싶으면 해당 구현체로 갈아끼우기만 하면 된다. (**추상화**)
- `DriverManager` 는 `DataSource` 인터페이스를 사용하지 않는다. 하지만 스프링은 `DriverManager` 도 해당 기능을 사용할 수 있게 **`DriverManagerDataSource` 라는 `DataSource` 인터페이스를 구현한 클래스를 제공**한다.
