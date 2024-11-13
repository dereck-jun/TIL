# 개념

스프링 시큐리티는 초기화 때 인증이나 인가와 관련된 여러가지 작업들을 한다. 여기에는 여러 개의 객체나 설정들, 기타 여러가지 일들을 하는데 이런 일들을 종합적으로 처리하는 2개의 클래스가 SecurityBuilder와 SecurityConfigurer이다.

![SecurityBuilder 다이어그램.png](/media/Security/SecurityBuilder%20다이어그램.png)
![SecurityConfigurer 다이어그램.png](/media/Security/SecurityConfigurer%20다이어그램.png)

- SecurityBuilder는 빌더 클래스로서 웹 보안을 구성하는 Bean 객체와 설정 클래스들을 생성하는 역할을 하며 대표적으로 WebSecurity, HttpSecurity가 있다
- SecurityConfigurer는 Http 요청과 관련된 보안 처리를 담당하는 Filter들을 생성하고 여러 초기화 설정에 관여한다
- SecurityBuilder는 SecurityConfigurer를 참조하고 있으며 인증 및 인가 초기화 작업은 SecurityConfigurer에 의해 진행된다
	- SecurityConfigurer 클래스 자체는 인증이나 인가 초기화 작업을 SecurityBuilder 클래스에 의해서 하게 된다
- SecurityBuilder 클래스가 설정 클래스를 생성한 다음에 설정 클래스들이 가지고 있는 여러가지 기능들을 진행시키면서 초기화 작업들을 이끌어 가는 것이다
	- 이 설정 클래스 안에서 인증이나 인가와 관련된 여러가지 초기화 작업들이 이뤄진다는 것을 의미한다

![더 구체적인 Security 1.png](/media/Security/더%20구체적인%20Security%201.png)
![더 구체적인 Security 2.png](/media/Security/더%20구체적인%20Security%202.png)
1. SecurityBuilder에서 가장 핵심적인 역할을 하는 HttpSecurity가 생성
	- 처음에는 자동 설정에서 생성이 되고 Bean이 됨
2. Bean 객체가 SecurityConfigure 타입의 설정 클래스를 생성
3. SecurityConfigure에는 `init(B Builder)`/`configure(B Builder)` 2개의 메서드가 있고, 이때 매개변수에 HttpSecurity가 들어가고 초기화 작업들이 일어나게 됨
4. 그 과정에서 대표적으로 Filter들을 생성하게 된다. 
	- 각각의 configure 들마다 필터들이 있다

