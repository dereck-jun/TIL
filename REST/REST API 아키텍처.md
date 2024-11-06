# REST란?

REST란 Representational State Transfer의 줄임말로 자원을 이름으로 구분하여 해당 자원의 상태를 주고 받는 모든 것을 의미한다. 클라이언트와 서버 간 데이터를 주고 받는 방식에 대한 아키텍처 스타일이다.

- HTTP URI를 통해 자원을 명시
- HTTP Method를 활용
- 자원에 대한 CRUD 작업을 적용하는 것을 의미

> 이런 REST의 특징을 준수하는 인터페이스를 RESTful API라고 한다.

## REST의 구성 요소

1. 자원(Resource): HTTP URI
2. 자원에 대한 행위(Verb): HTTP Method
3. 자원에 대한 행위의 내용(Representations): HTTP Message Payload

### HTTP URI

URI란 Uniform Resource Identifier의 줄임말로 직역하면 통합 자원 식별자라는 뜻을 가지고, 인터넷 상의 리소스 즉, 자원 자체를 식별하는 고유한 문자열 시퀀스를 뜻한다.

URL이란 Uniform Resource Locator의 줄임말로 네트워크 상에서 통합 자원의 위치를 나타내기 위한 규약이다.

URI 안에 URL의 개념이 들어간다. 그러니까 URL은 일종의 URI인 것이다. 하지만 모든 URI가 URL인 것은 아니다. URI에 위치(Locator)까지 정해지면 URL이 되는 것이다. 이러한 URL은 프로토콜과 결합한 형태이다.

이외에도 URN이 있지만 URN 이름 만으로 실제 리소스를 찾을 수 있는 방법이 보편화 되어있지 않다. 그렇기 때문에 URI를 URL과 같은 의미로 말하기도 한다.

### HTTP Method

CRUD(`Create`, `Read`, `Update`, `Delete`) 작업을 나타내며 기본적인 데이터 처리 기능을 의미한다.

HTTP Method와는 다음과 같이 연결된다.
- GET -> Read
- POST -> Create
- PUT, PATCH -> Update
- DELETE -> Delete

### HTTP Message Payload

![HTTP Message Payload.png](/media/REST/HTTP%20Message%20Payload.png)

Payload는 전송되는 데이터를 의미한다. 이때 함께 전달되는 헤더나 속성은 포함되지 않는다.

주로 JSON 형태로 데이터를 보내게 된다.

```json
{
	"status": "OK",
	"data": {
		"message": "Hello World!"
	}
}
```

## REST의 대표 특징 5가지

1. Server-Client (서버-클라이언트 구조)
	- 클라이언트와 서버는 서로 독립적이며, 클라이언트는 오직 URI만 알고 있다. (내부 로직에 대해서 알 필요가 없다)
	- 클라이언트와 서버 간 인터페이스가 변경되지 않는 한, 두 시스템은 독립적으로 개발 가능하다.
	- 어떤 서비스를 개발할 때 프론트엔더와 백엔더가 모여서 API 스펙을 먼저 논의하는 이유이기도 하다.
	- 논의를 하는 과정에서 커뮤니케이션이 발생하고, 서로를 잘 이해하기 위해선 프론트와 백 모두 알면 매우 좋다.
2. Stateless (무상태)
	- 클라이언트의 요청에는 해당 요청을 이해할 수 있는 모든 정보가 포함되어야 한다.
	- 서버는 해당 요청을 저장하거나 관리하지 않는다.
	- 클라이언트의 상황을 고려하지 않고, 요청에 대한 처리만 하기 때문에 구현이 간결해진다.
	- 세션, 인증, 인가 등에 대한 정보 또한 클라이언트에서 보관한다.
3. Cacheable (캐시 처리 기능)
	- 클라이언트는 API 요청을 캐싱하고 있기 때문에 동일한 요청이 발생하는 경우 서버로 요청을 발생시키지 않는다.
4. Layered System (계층화)
	- REST는 다중 계층 구조를 가질 수 있도록 허용한다.
	- API 서버, DB 서버, 인증 서버를 따로 두는 것이 가능해야 한다.
5. Uniform Interface (인터페이스 일관성)
	- HTTP 표준을 따른다면 어떤 기술이던지 접목하여 사용할 수 있기 떄문에 플랫폼과 언어의 제약이 없다.

## REST의 장단점
### 장점
- HTTP 프로토콜의 인프라를 그대로 사용하기 때문에 REST API 사용을 위한 별도의 인프라를 구축할 필요가 없다.
- HTTP 프로토콜을 활용하는 모든 플랫폼에 적용 가능하다.
- REAT API 메시지는 의도하는 바를 명확하게 나타내므로 의도하는 바를 쉽게 파악할 수 있다.
- 서버의 클라이언트의 역할을 명확하게 분리한다.

### 단점
- REST를 정의하는 정식 표준이 존재하지 않는다.
- HTTP Method 형태가 제한적이다.

## REST API Best Practice
### URI는 동사보다 명사를 사용

`http://localhost:8080/studying` -> `http://localhost:8080/study`

### 대문자보다 소문자를 활용

`http://localhost:8080/Study/Physics` -> `http://localhost:8080/study/physics`

### URI 마지막에는 슬래시(/)를 포함하지 않음

`http://localhost:8080/study/physics/` -> `http://localhost:8080/study/physics`

### 언더바(\_) 대신 하이픈(-) 사용

`http://localhost:8080/study_physics` -> `http://localhost:8080/study-physics`

### 파일 확장자는 URI에 포함시키지 않음

`http://localhost:8080/study-physics.txt` -> `http://localhost:8080/study-physics`

### 행위를 포함시키지 않음

이때 행위는 조회, 생성, 수정 따위를 말한다. 이런 행위는 HTTP Method를 통해 전달하기 때문에 포함하지 않는다.

`http://localhost:8080/delete-file` -> `http://localhost:8080/file`
`http://localhost:8080/create-user` -> `http://localhost:8080/user`
`http://localhost:8080/get/user/1` -> `http://localhost:8080/user/1`

### 영어는 복수형으로 작성

`http://localhost:8080/file` -> `http://localhost:8080/files`
`http://localhost:8080/user/1` -> `http://localhost:8080/users/1`