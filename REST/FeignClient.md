# FeignClient 사용하기
## 의존성

```groovy
dependencies {
	implementation 'org.springframework.cloud:spring-cloud-starter-openfeign:<version>'
}
```

## @EnableFeignClients 추가

실행 클래스에 `@EnableFeignClients`를 추가하면 @FeignClient 어노테이션이 붙은 클래스를 찾아서 설정을 해준다.

## 인터페이스 생성

`@FeignClient` 어노테이션을 활용하여 생성한다. 나머지는 일반 API 만드는 것과 같이 만든다.

```java
@FeignClient(name = "hello", url = "https://api.hello.com/api/v1/")
public interface ApiClient {
	@PostMapping("/hello/worlds")
	Response helloWorlds(@RequestParam String field);
}
```

## URL 가져오기

properties 같은 설정 파일에서 URL을 가져올 수 있다.

```yml
external:
	abc-service:
		host: 'https://com.example'
```

이때 `@FeignClient`의 url 속성을 수정해줘야 한다.

```java
@FeignClient(name = "hello", url = "${external.abc-service.host}")
```
