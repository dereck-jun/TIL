## 등록
### 커넥션 획득
- `getConnection()`: 이전에 만들어둔 `DBConnectionUtil`를 통해서 데이터베이스 커넥션을 획득한다.

### save() - SQL 전달
- `String sql` : 데이터베이스에 전달할 SQL을 정의한다. 여기서는 데이터를 등록해야 하므로 `insert sql` 을 사용.
- `con.prepareStatement(sql)` : 데이터베이스에 전달할 SQL과 파라미터로 전달할 데이터들을 준비한다.
	- `String sql = insert into member(member_id, money) values(?, ?);`
		- `values(?, ?)` 를 사용해서 SQL Injection 공격을 방어한다. (`?` 안에 들어올 값을 확실히 명시한 것)
	- `ps.setString(1, member.getMemberId());` : SQL의 첫 번째 `?`에 값을 지정한다. 문자이므로 `setString()` 을 사용한다.
	- `ps.setInt(2, member.getMoney());` : SQL의 두 번째 `?` 에 값을 지정한다. `Int` 형 숫자이므로 `setInt()` 를 사용한다.
- `ps.executeUpdate()` : `Statement` 를 통해 준비된 SQL을 커넥션을 통해 실제 데이터베이스에 전달한다. 참고로 `executeUpdate()` 는 `int` 를 반환하는데 영향받은 DB row 수를 반환한다. 


> **주의**
> 
> 리소스 정리는 꼭! 해줘야 한다. 따라서 예외가 발생하든, 하지 않든 항상 수행되어야 하므로 `finally` 구문에 주의해서 작성해야 한다. 만약 이 부분을 놓치게 되면 커넥션이 끊어지지 않고 계속 유지되는 문제가 발생할 수 있다. 이런 것을 리소스 누수라고 하는데, 결과적으로 커넥션 부족으로 장애가 발생할 수 있다.


> **참고**
> 
> `PreparedStatement` 는 `Statement` 의 자식 타입인데, `?` 를 통한 파라미터 바인딩을 가능하게 해준다. 참고로 SQL Injection 공격을 예방하려면 `PreparedStatement` 를 통한 파라미터 바인딩 방식을 사용해야 한다.


## 조회

### findById() - 쿼리 실행

- `sql` : 데이터 조회를 위한 select SQL 사용
- `rs = ps.executeQuery()` : 데이터를 변경할 때와는 다르게 조회할 때는 `executeQuery()` 를 사용하여 결과를 `ResultSet` 에 담아서 반환한다.


###  ResultSet

- `ResultSet` 은 엑셀 테이블처럼 생긴 데이터 구조이다. 보통 `select` 쿼리의 결과가 순서대로 들어간다.
	- 예를 들어서 `select memvber_id, money` 라고 지정하면 `member_id` , `money` 라는 이름으로 데이터가 저장된다.
- `ResultSet` 내부에 있는 커서(`Cursor`)를 이동해서 다음 데이터를 조회할 수 있다.
- `rs.next()` : 이것을 호출하면 커서가 다음으로 이동한다. 최초의 커서는 데이터를 가리키고 있지 않기 때문에 해당 메서드를 최소 한 번은 호출해야 데이터를 조회할 수 있다.
	- 해당 메서드를 호출하지 않으면 데이터를 넣어야 하는 공간에 데이터가 아닌 속성을 넣으려고 하는 격
	- 해당 메서드의 결과가 `true` 면 커서의 이동 결과 데이터가 있다는 뜻
	- 해당 메서드의 결과가 `false` 면 더이상 커서의 이동 결과 데이터가 없다는 뜻
- `rs.getString("member_id")` : 현재 커서가 가리키고 있는 위치의 `member_id` 데이터를 `String` 타입으로 반환한다.
- `rs.getInt("money")` : 현재 커서가 가리키고 있는 위치의 `money` 데이터를 `int` 타입으로 반환한다.

> 이때 여러 데이터를 조회하려면 `while` 같은 반복문을 사용해야 하지만 현재 코드는 회원 한 명을 조회하는 것이 목적이므로 `if` 를 사용했다.

## 수정, 삭제

수정과 삭제는 등록과 비슷하다. 등록, 수정, 삭제처럼 데이터를 변경하는 쿼리는 `executeUpdate()` 를 사용하면 된다.

삭제의 경우 테스트 코드 작성 시 
1. 회원을 삭제한 다음 `findById()` 를 통해서 조회한다. 
2. 그럼 회원이 없기 때문에 `NoSuchElementException` 이 발생한다.  
	- `NoSuchElementException` 이 나오게 설정을 했다는 가정
3. `assertThatThrownBy`를 사용해서 검증한다. 
	- `assertThatThrownBy`는 해당 예외가 발생해야 검증에 성공


> **참고**
> 
> 마지막에 회원을 삭제하기 때문에 테스트가 정상 수행되면, 이제부터는 같은 테스트를 반복해서 실행할 수 있다. 물론 테스트 중간에 오류가 발생해서 삭제 로직을 수행할 수 없다면 테스트를 반복해서 실행할 수 없다. 
> 이와 같은 문제는 트랜잭션으로 해결할 수 있다.
