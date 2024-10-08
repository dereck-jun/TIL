기본 키 매핑 어노테이션에는 `@Id`와 `@GeneratedValue`가 있다.

## 기본 키 매핑 방법

기본 키 매핑 방법에는 개발자가 PK 값을 직접 할당하는 **직접 할당**과 원하는 키 생성 전략을 선택해서 데이터베이스가 자동으로 PK 매핑을 하는 **자동 생성**이 있다.

### 직접 할당

`@Id`만을 사용한다. 위에서 설명했던 것처럼 개발자가 직접 PK 값을 설정해서 저장하고 싶은 경우에 사용한다.


### 자동 생성

`@Id`와 `@GeneratedValue`를 사용한다. 이때 각 데이터베이스마다 PK 값을 자동 생성하는 전략이 다르기 때문에 몇몇 옵션을 가지고 있다.


## 자동 생성 전략

자동 생성 전략에는 `IDENTITY`, `SEQUENCE`, `TABLE`, `AUTO`가 있다.

### IDENTITY 전략

- 기본 키 생성 방법을 데이터베이스에 위임하는 것이다.
	- 영속성 컨텍스트에서 데이터베이스로 `sql`을 넘길 때 id 값을 `null`로 넘긴다.
- 주로 `MySQL`, `PostgreSQL`, `SQL Server`, `DB2`에서 사용한다.
	- ex) `MySQL` - `AUTO_INCREMENT`


JPA는 보통 트랜잭션 커밋 시점에 `insert sql`을 실행한다. 하지만 `IDENTITY` 전략의 경우 데이터베이스에서 값을 설정하기 때문에 쿼리 실행 전까지 id 값이 `null`이 된다. 이 말은 영속성 컨텍스트의 1차 캐시에 저장할 수 없다는 말이 된다. 

따라서 `IDENTITY` 전략의 경우 `em.persist()` 시점에 즉시 `insert sql`을 실행하고, JPA에서 내부적으로 해당 id 값을 데이터베이스에서 조회해서 가지고 온다. 그래서 영속성 컨텍스트에 저장이 된다.

결론적으로 데이터베이스에 `insert`하는 시점에 이 값을 바로 알 수 있다.

### SEQUENCE 전략

- 데이터베이스 시퀀스는 유일한 값을 순서대로 생성하는 특별한 데이터베이스 오브젝트이다.
- 주로 `Oracle`, `PostgreSQL`, `DB2`, `H2` 데이터베이스에서 사용한다.
- `@SequenceGenerator`와 함께 사용할 수 있다.

`@SequenceGenerator` 속성
- name
	- 실제 `@Id` 필드에서 참조할 이름
	- 필수로 입력해야 한다.
- sequenceName
	- 실제 데이터베이스에 생성되는 시퀀스 객체 이름
	- 기본 값은 `hibernate_sequence`이고, 각 테이블마다 시퀀스를 따로 관리할 수 있음
- allocationsSize
	- 데이터베이스에서 가져오는 시퀀스 호출에 증가하는 값의 크기 (성능 최적화)
		- 여러 웹 서버가 있어도 동시성 문제 없이 다양한 문제 해결 가능
		- 웹 서버를 내리는 시점에 가지고 있던 값이 날라간다.
	- 기본 값이 50이기 때문에 시퀀스 값이 하나씩 증가하도록 설정되어 있으면 이 값을 반드시 1로 설정해야 한다.
- initialValue
	- DDL 생성 시에만 사용된다. DDL을 생성할 때 처음 시작하는 수를 지정
	- 기본 값은 1

`SEQUENCE` 전략 또한 `IDENTITY` 전략과 마찬가지로 id 값을 따로 설정하지 않는다. 하지만 `IDENTITY` 전략과는 다르게 `flush`가 바로 실행되지는 않는다. 

대신 설정한 시퀀스에서 초기 값 또는 `next_val`의 값을 가져와서 해당 객체에 id 값을 넣은 뒤 영속성 컨텍스트에 저장한다.

결론적으로 PK 값만 얻어온 뒤에 실제로 `flush`는 커밋 직전에 수행되는 것이다. 


> [!info] SEQUENCE 전략의 최적화
> `SEQUENCE` 전략을 사용하고 `initialValue = 1`, `allocationsSize = 50` 인 경우 데이터베이스에서 시퀀스를 보면 `현재 값: -49`, `증가: 50`으로 되어있는 것을 알 수 있다.
> 
> 그 이유는 보통 `call next value`를 한 번 호출해서 나온 값을 사용하는데, 처음 호출했을 때 1이 되기를 기대하기 때문이다. 이후 한번 더 호출을 해서 DB의 SEQ 값을 51로 맞추게 된다. (처음은 더미를 호출한 것이라고 생각)
> 
> SEQ 값을 51로 맞춘 이후부터는 DB에서 SEQ 값을 호출하지 않고, DB로부터 받아온 50개의 SEQ 값을 메모리에 캐싱받아 작동하게 된다. (메모리에서 작동한다는 말)
> 
> 대신 51번을 만나는 순간 그 다음 50개를 확보해야 하기 때문에 `call next value`가 한번 호출된다.
> 
> 값을 크게 잡아도 상관 없지만 웹 서버를 내리는 시점에 확보한 SEQ 값이 날라가기 때문에 그 숫자만큼 공백이 생기고, 다 사용하지 못하고 내려간 시점에서 낭비가 발생한 것이 되게 된다.



### TABLE 전략

- 키 생성 전용 테이블을 하나 만들어서 데이터베이스 시퀀스를 흉내내는 전략
- 특정 데이터베이스에 의존적이지 않다.
	- 어느 데이터베이스에나 사용할 수 있다.
	- 성능 이슈가 존재한다.
- 해당 전략 사용 시 `jpa-ddl-auto` 설정이 되어있지 않다면 시퀀스 테이블 생성이 선행돼야 한다.
- `@TableGenerator`와 함께 사용할 수 있다.

`@TableGenerator` 속성
- name
	- 실제 `@Id` 필드에서 참조할 이름
	- 필수로 입력해야 한다.
- table
	- 시퀀스 생성 용 테이블 이름을 설정
- pkColumnName
	- 시퀀스 컬럼 이름을 설정
	- 기본 값은 `sequence_name`
- valueColumnName
	- 시퀀스 값 컬럼 이름을 설정
	- 기본값은 `next_val`
- pkColumnValue
	- 키로 사용할 값의 이름을 설정
	- 기본 값은 엔티티 이름
- initialValue
	- 초기 값, 마지막으로 생성된 값이 기준
	- 기본 값은 0
- allocationSize
	- 데이터베이스에서 가져오는 시퀀스 호출에 증가하는 값의 크기 (성능 최적화)
	- 기본 값이 50이기 때문에 시퀀스 값이 하나씩 증가하도록 설정되어 있으면 이 값을 반드시 1로 설정해야 한다.
- catalog
	- 데이터베이스 `catalog` 이름
- scheme
	- 데이터베이스 `scheme` 이름
- uniqueConstraints (DDL)
	- 유니크 제약 조건을 지정할 수 있음

### AUTO 전략

데이터베이스에 따라 자동으로 위의 3가지 전략 중 하나를 선택한다. 데이터베이스를 바꿔도 수정할 필요는 없지만 `SEQUENCE`나 `TABLE`의 경우 시퀀스나 키 테이블을 미리 생성해야 하는 것에 주의가 필요하다. 

`jpa-ddl-auto` 설정이 되어있다면 `hibernate`가 알아서 생성해 준다.



## 권장하는 식별자 전략

기본 키 제약 조건에 대해서 생각을 먼저 해보는 것이 필요하다. 기본 키 제약 조건은 `null`이면 안되고, 변하면 안된다. (유일성)

하지만 이 조건을 만족하는 자연 키는 찾기 어렵기 때문에 대리 키(대체 키)를 사용하는 것이 좋다.
- 주민등록번호 역시 기본 키로는 적절하지 않다.
- 전화번호 역시 마찬가지

권장하는 값은 "**Long 타입 + 대체 키 + 키 생성 전략 사용**"이다. 이때 `long` 타입이 아니라 참조 타입인 `Long`인 것에 주의
- 데이터가 생성되는 시점에서 해당 값이 할당된다는 것이며, 도메인 객체의 id는 특정 시점에 존재할 수도 있고 존재하지 않을 수도 있다.  
- 그렇기 때문에 `long`이 아닌 `Long`을 사용하며, 다만 이 변수가 `not null` 이 보장된다면 `long`을 사용하는 것이 더 좋다.