## 회원가입, 로그인 관련

회원가입과 로그인을 할 땐 Authentication에 관련된 값이 필요가 없다. 따라서 해당 토큰을 검증하는 필터를 거칠 필요가 없다.

```java
@Slf4j  
@Component  
@RequiredArgsConstructor  
public class JwtAuthenticationFilter extends OncePerRequestFilter {  
  
    private final JwtService jwtService;  
    private final UserService userService;  
  
    @Override  
    protected void doFilterInternal(HttpServletRequest request, HttpServletResponse response, FilterChain filterChain) throws ServletException, IOException {  
        if (isRegisterRequestOrLogin(request.getRequestURI())) {  
            filterChain.doFilter(request, response);  
            return;  
        }
          
  
		final String BEARER_PREFIX = "Bearer ";  
		String authorization = request.getHeader(HttpHeaders.AUTHORIZATION);  
		  
		// SecurityContext 의 세부 내용은 SecurityContextHolder 에 저장되기 때문에 
		// 해당 클래스에서 컨텍스트를 가져옴. 
		// 없으면 null
		SecurityContext securityContext = SecurityContextHolder.getContext(); 
		if (ObjectUtils.isEmpty(authorization) || !authorization.startsWith(BEARER_PREFIX)) {  
		    setErrorResponse(response, INVALID_AUTH_HEADER);  
		    return;  
		}  
		
		String token = authorization.substring(BEARER_PREFIX.length());  
		if (ObjectUtils.isEmpty(token)) {  
		    setErrorResponse(response, INVALID_JWT_TOKEN);  
		    return;  
		}
	    // .. 다른 검증 코드
    }
    private static void setErrorResponse(HttpServletResponse response, BaseResponseStatus baseResponseStatus) throws IOException {  
	    response.setContentType(APPLICATION_JSON_VALUE);  
	    response.setStatus(SC_BAD_REQUEST);  
	    response.setCharacterEncoding("UTF-8");  
	  
	    BaseResponse<Object> errorResponse = new BaseResponse<>(baseResponseStatus);  
	    String responseJson = new ObjectMapper().writeValueAsString(errorResponse);  
	    response.getWriter().write(responseJson);  
	}  
  
	private boolean isRegisterRequestOrLogin(String uri) {  
	    final String START_URI = "/api/v1/users/";  
	    final String REGISTER_URI = START_URI + "register";  
	    final String LOGIN_URI = START_URI + "login";  
	  
	    return uri.startsWith(REGISTER_URI) || uri.startsWith(LOGIN_URI);  
	}
}
```

토큰을 검증하는 필터를 거치기 전에 if 문으로 URI 값에 따라서 아직 토큰을 발급하기 전인 회원가입이나, 로그인의 경우 `doFilter`를 호출하게 했다.

또한 `setErrorResponse`로 사용자 정의 에러 status를 JSON 형태로 보낼 수 있게 해줬다. 

```java
@Bean  
public SecurityFilterChain filterChain(HttpSecurity http) throws Exception {  
    http.authorizeHttpRequests(requests ->  
                    requests.requestMatchers(
	                    "/api/v1/users/register", "/api/v1/users/login"
	                ).permitAll()  
                    .anyRequest()  
                    .permitAll())  
            .sessionManagement(session -> 
			        session.sessionCreationPolicy(SessionCreationPolicy.STATELESS))  
            .csrf(AbstractHttpConfigurer::disable)  
            .cors(Customizer.withDefaults())  
            .addFilterBefore(jwtAuthenticationFilter, UsernamePasswordAuthenticationFilter.class)  
            .addFilterBefore(jwtExceptionFilter, JwtAuthenticationFilter.class)  
            .httpBasic(Customizer.withDefaults());  
    return http.build();  
}
```

SecurityFilterChain은 바꾼게 없다. `JwtException`은 해당 에러가 발생했을 때만 잡게끔 만들었기 때문에 인증 토큰과는 관계가 없어서 역시 바꾼게 없다. 

```java
@Component  
public class JwtExceptionFilter extends OncePerRequestFilter {  
    @Override  
    protected void doFilterInternal(HttpServletRequest request, HttpServletResponse response, FilterChain filterChain) throws ServletException, IOException {  
        try {  
            filterChain.doFilter(request, response);  
        } catch (JwtException e) {  
            response.setContentType(APPLICATION_JSON_VALUE);  
            response.setStatus(SC_UNAUTHORIZED);  
            response.setCharacterEncoding("UTF-8");  
  
            Map<String, Object> errorMap = new HashMap<>();  
            errorMap.put("status", response.getStatus());  
            errorMap.put("message", e.getMessage());  
  
            ObjectMapper objectMapper = new ObjectMapper();  
            String responseJson = objectMapper.writeValueAsString(errorMap);  
            response.getWriter().write(responseJson);  
        }  
    }  
}
```
