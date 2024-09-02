## 조인이란?

하나의 테이블에 원하는 데이터가 모두 존재하면 좋겠지만, **두 개의 테이블을 엮어야 원하는 결과**가 나오는 경우도 많다. **조인을 사용하면 두 개의 테이블을 엮어서 원하는 데이터를 추출**할 수 있다.

두 테이블의 조인을 위해선 **기본 키와 외래 키 관계**로 맺어져야 하고, 이를 일대다(1:N) 관계라고 한다.

- INNER JOIN(내부 조인)
	- 두 테이블을 조인할 때, 두 테이블에 모두 지정한 열의 데이터가 있어야 한다.
	- 교집합
- OUTER JOIN(외부 조인)
	- 두 테이블을 조인할 때, 1개의 테이블에만 데이터가 있어도 결과가 나온다.
	- 방향에 따라 어느 쪽 테이블의 모든 데이터를 출력할 것인지 정할 수 있다.
- CROSS JOIN(상호 조인)
	- 한 쪽 테이블의 모든 행과 다른 쪽 테이블의 모든 행을 조인하는 기능이다.
- SELF JOIN(자체 조인)
	- 자신이 자신과 조인한다는 의미로, 1개의 테이블을 사용한다.

## 내부 조인

두 테이블을 연결할 때 가장 많이 사용하는 것이 내부 조인이다. 그냥 조인이라고 부르면 내부 조인을 의미한다.

```sql
SELECT <열 목록> 
FROM <첫 번째 테이블>     
	INNER JOIN <두 번째 테이블>     
	ON <조인 조건> 
[WHERE 검색 조건]  

# INNER JOIN을 JOIN이라고만 써도 INNER JOIN으로 인식함
```

![[inner_join.png]](/media/DB/inner_join.png)


## 외부 조인

내부 조인과는 다르게 한쪽에만 데이터가 있어도 결과가 나온다.

외부 조인에는 방향을 설정할 수 있다.
- LEFT OUTER JOIN
	- 왼쪽 테이블의 모든 값이 출력되는 조인
- RIGHT OUTER JOIN
	- 오른쪽 테이블의 모든 값이 출력되는 조인
- FULL OUTER JOIN
	- 모든 값이 출력되는 조인

```sql
SELECT <열 목록> 
FROM <첫 번째 테이블(LEFT 테이블)>     
	<LEFT | RIGHT | FULL> OUTER JOIN <두 번째 테이블(RIGHT 테이블)>      
	ON <조인 조건> 
[WHERE 검색 조건]
```

![[outer_join_1.png]](/media/DB/outer_join_1.png)

![[outer_join_2.png]](/media/DB/outer_join_2.png)


## 상호 조인

한쪽 테이블의 모든 행과 다른 쪽 테이블의 모든 행을 조인시키는 기능이다.

상호 조인의 결과의 전체 행 개수는 두 테이블의 각 행의 개수를 곱한 수만큼 된다. 카티션 곱(CARTESIAN PRODUCT)라고도 한다.

```sql
SELECT *
FROM <첫 번째 테이블>
	CROSS JOIN <두 번째 테이블>
```

![[cross_join.png]](/media/DB/cross_join.png)


## 자체 조인

자체 조인은 자기 자신과 조인하므로 하나의 테이블을 사용한다.

별도의 문법이 있는 것은 아니고 1개로 조인하면 자체 조인이 된다.

```sql
SELECT <열 목록>
FROM <테이블> [as] 별칭A
	INNER JOIN <테이블> [as] 별칭B
[WHERE 검색 조건]
```

![[self_join.png]](/media/DB/self_join.png)
