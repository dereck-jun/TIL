# TIL Wiki
## DB
- [JOIN](/DB/join.md)

## Batch
- [일반적인 배치 시스템](/Batch/일반적인%20배치%20시스템.md)

## JDBC
### JDBC 이해
- [기본](/JDBC/[JDBC]%20JDBC%20기본.md)
- [등장 이유](/JDBC/[JDBC]%20등장%20이유.md)
- [사용 방법](/JDBC/[JDBC]%20사용%20방법.md)

### 커넥션 풀과 데이터 소스 이해
- [데이터베이스 연결 구조와 DB 세션](/JDBC/[JDBC]%20데이터베이스%20연결%20구조와%20DB%20세션.md)
- [DataSource](/JDBC/[JDBC]%20DataSource.md)
- [커넥션 풀](/JDBC/[JDBC]%20커넥션%20풀.md)
- [DriverManager](/JDBC/[JDBC]%20DriverManager.md)

### JDBC Template
- [반복 문제](/JDBC/[JDBC]%20반복%20문제.md)
- [JdbcTemplate](/JDBC/[JDBC]%20JdbcTemplate.md)

## JPA
### JPA 소개
- [SQL 중심적인 개발의 문제점](/JPA/[JPA]%20SQL%20중심적%20개발의%20문제점.md)

### 영속성 관리 - 내부 동작 방식
- [영속성 컨텍스트](/JPA/[JPA]%20영속성%20컨텍스트.md)
- [준영속 상태](/JPA/[JPA]%20준영속%20상태.md)
- [플러시](/JPA/[JPA]%20플러시.md)

### 엔티티 매핑
- [객체와 테이블 매핑](/JPA/[JPA]%20객체와%20테이블%20매핑.md)
- [필드와 컬럼 매핑](/JPA/[JPA]%20필드와%20컬럼%20매핑.md)
- [기본 키 매핑](/JPA/[JPA]%20기본%20키%20매핑.md)

### 연관관계 매핑
- [연관관계 매핑 1 - 방향과 연관관계의 주인](/JPA/[JPA]%20연관관계%20매핑%201.md)
- [연관관계 매핑 2 - 다중성](/JPA/[JPA]%20연관관계%20매핑%202.md)
- [상속관계 매핑](/JPA/[JPA]%20상속%20관계%20매핑.md)
- [@MappedSuperClass](/JPA/[JPA]%20@MappedSuperClass.md)

### 프록시와 연관관계 관리
- [프록시](/JPA/[JPA]%20프록시.md)
- [즉시 로딩과 지연 로딩](/JPA/[JPA]%20즉시%20로딩과%20지연%20로딩.md)
- [영속성 전이](/JPA/[JPA]%20영속성%20전이.md)

### 값 타입
- [값 타입](/JPA/[JPA]%20값%20타입.md)
- [값 타입과 불변 객체](/JPA/[JPA]%20값%20타입과%20불변%20객체.md)

### 객체 지향 쿼리 언어
- [소개](/JPA/[JPA]%20소개.md)
- [프로젝션](/JPA/[JPA]%20프로젝션.md)
- [페이징 API](/JPA/[JPA]%20페이징%20API.md)
- [JPQL 타입 표현과 기타식](/JPA/[JPA]%20JPQL%20타입%20표현과%20기타식.md)
- [JPQL 함수](/JPA/[JPA]%20JQPL%20함수.md)
- [경로 표현식](/JPA/[JPA]%20경로%20표현식.md)
- [페치 조인](/JPA/[JPA]%20페치%20조인.md)
- [다형성 쿼리](/JPA/[JPA]%20다형성%20쿼리.md)
- [엔티티 직접 사용](/JPA/[JPA]%20엔티티%20직접%20사용.md)
- [Named 쿼리](/JPA/[JPA]%20Named%20쿼리.md)
- [벌크 연산](/JPA/[JPA]%20벌크%20연산.md)

## Security
- [doFilterInternal](/Security/doFilterInternal%20관련.md)
- [Interceptor와 Filter](/Security/Interceptor와%20Filter의%20차이.md)
- [SecurityBuilder와 SecurityConfigurer](/Security/SecurityBuilder와%20SecurityConfigurer.md)

## Reference
### 인프런
- [자바 ORM 표준 JPA 프로그래밍 - 기본편 (김영한님)](https://www.inflearn.com/course/ORM-JPA-Basic)
- [스프링 DB 1편 - 데이터 접근 핵심 원리](https://www.inflearn.com/course/%EC%8A%A4%ED%94%84%EB%A7%81-db-1)
- [스프링 DB 2편 - 데이터 접근 활용 기술](https://www.inflearn.com/course/%EC%8A%A4%ED%94%84%EB%A7%81-db-2)
- [스프링 시큐리티 완전 정복](https://www.inflearn.com/course/%EC%8A%A4%ED%94%84%EB%A7%81-%EC%8B%9C%ED%81%90%EB%A6%AC%ED%8B%B0-%EC%99%84%EC%A0%84%EC%A0%95%EB%B3%B5)

### 패스트캠퍼스
- 10개 프로젝트로 시작하는 백엔드 웹 개발

### 블로그
- [JPA 연관 관계 한방에 정리 (단방향/양방향, 연관 관계의 주인, 일대일, 다대일, 일대다, 다대다)](https://jeong-pro.tistory.com/231)
- [[JPA] 프록시란?](https://ict-nroo.tistory.com/131)
- [도메인 주도 설계 (3) - 애그리거트](https://velog.io/@gentledot/ddd-aggregate)
- [한빛 미디어 - SQL 기본 문법: JOIN](https://hongong.hanbit.co.kr/sql-%EA%B8%B0%EB%B3%B8-%EB%AC%B8%EB%B2%95-joininner-outer-cross-self-join/)