# RestTemplate 사용하기
## 의존성

RestTemplate은 `spring-boot-starter-web`에 포함되어 있다.

```groovy
dependencies {
	implementation 'org.springframework.boot:spring-boot-starter-web'
}
```

## 외부 API 스펙 파악

```txt
가상화폐 데이터를 가져올 수 있는 업비트 OPEN API
- https://docs.upbit.com/

그 중에서도 분(min) 캔들 데이터를 가져올 수 있는 API를 활용하려고 한다.
```

### 업비트 분(min) 캔들 데이터 조회 API

[업비트 분 캔들 데이터 조회 API docs](https://docs.upbit.com/reference/%EB%B6%84minute-%EC%BA%94%EB%93%A4-1)
- `GET https://api.upbit.com/v1/candles/minutes/{unit}` 

![업비트 분 캔들 요청 파라미터.png](/media/REST/업비트%20분%20캔들%20요청%20파라미터.png)

![업비트 분 캔들 응답 필드.png](/media/REST/업비트%20분%20캔들%20응답%20필드.png)

## 데이터를 받아오기
### 데이터를 받을 수 있도록 객체 생성

자료형과 변수명을 응답 데이터 형식에 맞춰서 작성한다. 이때 `snake_case`를 사용하지 않으면 데이터를 받을 수 없다. 따라서 `@JsonProperty`를 사용해서 데이터 매핑 시 사용될 이름을 따로 만들어 주는 것으로 대체할 수 있다.

```java
public class UpbitMinuteCandle {
	private String market;

	@JsonProperty("candle_date_time_utc")  // 매핑 시 사용
	private String candleDateTimeUtc;
	private String candle_date_time_kst;
	private Double opening_price;
	private Double high_price;
	// etc
}
```

### GET API 만들기

입력 값을 검증할 수 있도록 Validator와 실제 로직이 들어있는 Service를 주입받아서 사용한다.

```java
@RestController
@RequiredArgsConstructor
public class UpbitController {
	private final MinuteCandleValidator validator;
	private final UpbitService upbitService;

	@GetMapping("/api/v1/candle/minute/{unit}")
	public List<MinuteCandle> getMinuteCandles(
		@PathVariable int unit,
		@RequestBody MinuteCandleRequest request,
		BindingResult bindingResult
	) throws JsonProcessingException {
		validator.validate(unit.bindingResult);

		if (bindingResult.hasErrors()) {
			return new ArrayList<>();
		}

		return upbitService.getCandles(unit, request);
	}
}
```

```java
@Component
public class MinuteCandleValidator implements Validator {
	private List<Integer> allowedUnites = new ArrayList<>();

	MinuteCandleValidator() {
		allowedUnites.add(1);
		allowedUnites.add(3);
		allowedUnites.add(5);
		allowedUnites.add(10);
		allowedUnites.add(15);
		allowedUnites.add(30);
		allowedUnites.add(60);
		allowedUnites.add(240);
	}

	@Override
	public boolean supports(Class<?> clazz) {
		return clazz.isAssignableFrom(Integer.class);
	}

	@Override
	public void validate(Object target, Errors errors) {
		Integer unit = (Integer) target;

		if (!allowedUnites.contains(unit)) {
			errors.rejectValue("unit", "invalid unit");
		}
	}
}
```

### URI 설정 방식

`UriComponentBuilder`를 활용해서 URI를 설정할 수 있다.

```java
public List<MinuteCandle> getCandles(int unit, MinuteCandleRequest request) 
	throws JsonProcessingException {
	String uri = UriComponentBuilder.fromString("https://api.upbit.com/")
			.path("v1/candles/minutes/" + unit)
			.queryParam("market", request.getMarket())
			.queryParam("count", request.getCount())
			.build()
			.toUriString();
}
```

### 헤더 설정

`MAP`에 header의 속성과 값을 K-V로 같이 저장한 뒤 `HttpHeaders`로 설정할 수 있다.

```java
Map<String, String> headers = new HashMap<>();
headers.put("accept", "application/json");
HttpHeaders httpHeaders = new HttpHeaders();
httpHeaders.setAll(headers);
```

### ObjectMapper

```java
String result = httpClient.getData(uri, HttpMethod.GET, httpHeaders);

List<UpbitMinuteCandle> upbitMinuteCandles = 
		objectMapper.readValue(result, new TypeReference<>() {});

return upbitMinuteCandles.stream()
		.map(it -> MinuteCandle.builder()
				.market(it.getMarket())
				.build()).collect(Collectors.toList());
```

## 외부/내부 객체 분리

외부(업비트)에서 제공되는 데이터 형식과 내부에서 관리하는 데이터 형식은 별도로 관리되어야 한다. 동일한 필드가 존재하더라도 가짜 중복으로 볼 수 있다. 동일한 객체로 관리를 하던 중 만약 업비트에서 데이터 형식이 변경된다면 이 변경에 의해 내부 시스템도 영향을 받기 때문이다.