---
title: spring security
date: 2022-03-24 08:54:07
categories:
- Web/App
- Spring
tags:
- Spring Security
thumbnail: /images/spring.jpg
---


## 1부

Principal principal는 아규먼트 리졸버로 받아서 처리된다.

```java
@GetMapping("/dashboard")
public String dashboard(Model model, Principal principal) {
    model.addAttribute("message", "Hello " + principal.getName());
    sampleService.dashboard();
    return "dashboard";
}

```

의존성 추가
```xml
<dependency>
<groupId>org.springframework.boot</groupId>
<artifactId>spring-boot-starter-security</artifactId>
</dependency>
```

의존성을 추가하면 모든 요청은 인증을 필요로 해지고, 기본적으로 계정을 하나 만들어주는데 user/{패스워드랜덤} 으로 만들어준다.

### 11page
AuthenticationManager를 빈으로 등록하는 이유는 다른 곳에서 사용하기 위함임.
기본적으론 빈으로 등록이 안된다고 한다.

```java
@Bean
@Override
public AuthenticationManager authenticationManagerBean() throws Exception {
    return super.authenticationManagerBean();
}

```


### 12page {noop}123?
앞에 {xxxx}는 인코딩 방식을 의미한다. {noop}는 평문으로 저장한것이다

UserDetailsService 를 implements한 service를 빈으로 등록해둘 경우 따로 WebSecurityConfigureAdapter를 상속하고 configure() 메소드에 아래처럼 세팅을 안해도 된다.
PasswordEncoder 또한 마찬가지

```java
public class SecurityConfig extends WebSecurityConfigurerAdapter {
  @Autowired
  AccountService accountService;

  @Override
  protected void configure(AuthenticationManagerBuilder auth) throws Exception {
    auth.UserDetailsService(accountService);
  }
}
```



### 14page
```
<dependency>
	<groupId>org.springframework.security</groupId>
	<artifactId>spring-security-test</artifactId>
	<scope>test</scope>
</dependency>

```
가짜 유저를 활용하여 테스트 코드를 작성하는법.
실제로 디비에 들어있다고 가정하는것이 아닌 해당 테스트 유저가 있다고 가정하고 특정 페이지에 접근시 어떤 응답이 발생하는지 확인하는데 사용됨

@WithUser 대신 @WithMockUser(username = "keesun", roles = "USER") 를 넣어도 되지만 반복을 줄이기 위해 어노테이션을 하나 생성한것임
```java
@Retention(RetentionPolicy.RUNTIME)
@WithMockUser(username = "keesun", roles = "USER")
public @interface WithUser {
}



@Test
@WithAnonymousUser
public void index_anonymous() throws Exception {
    mockMvc.perform(get("/"))
            .andDo(print())
            .andExpect(status().isOk());
}

@Test
@WithUser
public void index_user() throws Exception {
    mockMvc.perform(get("/"))
            .andDo(print())
            .andExpect(status().isOk());
}

@Test
@WithUser
public void admin_user() throws Exception {
    mockMvc.perform(get("/admin"))
            .andDo(print())
            .andExpect(status().isForbidden());
}

@Test
@WithMockUser(username = "keesun", roles = "ADMIN")
public void admin_admin() throws Exception {
    mockMvc.perform(get("/admin"))
            .andDo(print())
            .andExpect(status().isOk());
}
```


form 로그인을 활용하여 로그인하는 테스트코드를 확인해보자.
```java
@Test
@Transactional
public void login_success() throws Exception {
    String username = "keesun";
    String password = "123";
    Account user = this.createUser(username, password);
    mockMvc.perform(formLogin().user(user.getUsername()).password(password))
            .andExpect(authenticated());
}

@Test
@Transactional
public void login_success2() throws Exception {
    String username = "keesun";
    String password = "123";
    Account user = this.createUser(username, password);
    mockMvc.perform(formLogin().user(user.getUsername()).password(password))
            .andExpect(authenticated());
}

@Test
@Transactional()
public void login_fail() throws Exception {
    String username = "keesun";
    String password = "123";
    Account user = this.createUser(username, password);
    mockMvc.perform(formLogin().user(user.getUsername()).password("12345"))
            .andExpect(unauthenticated());
}

private Account createUser(String username, String password) {
    Account account = new Account();
    account.setUsername(username);
    account.setPassword(password);
    account.setRole("USER");
    return accountService.createNew(account);
}
```





## 2부


### 16page

인증된 사용자 정보를 Principal라고 하는데 이를 Authentication객체에 담아서 보관하고 Authentication를 SecurityContext로 감싸고 SecurityContext를 SecurityContextHolder로 감싼다.


SecurityContextHolder
-	SecurityContext 제공, 기본적으로 ThreadLocal을 사용한다.
(즉 Authentication을 한 쓰레드 안에서 사용가능(파라미터를 넘기지 않아도..))
ThreadLocal 대신 다른 전략을 활용할 수 있다.

```java

@Service
public class AccountService implements UserDetailsService {

    @Autowired AccountRepository accountRepository;

    @Override
    public UserDetails loadUserByUsername(String username) throws UsernameNotFoundException {
        Account account = accountRepository.findByUsername(username);
        if (account == null) {
            throw new UsernameNotFoundException(username);
        }

        return new User.builder()
                    .username(account.getUsername())
                    .password(account.getPassword())
                    .roles(account.getRole())
                    .build();
    }
}


@Service
public class SampleService {

    public void dashboard() {
        Authentication authentication = SecurityContextHolder.getContext().getAuthentication();

        // 리턴하는건 위에 User.builder()에서 생성된 UserDetails 타입이다.
        // UserDetails 는 애플리케이션이 가지고 있는 유저 정보와
        //스프링 시큐리티가 사용하는 Authentication 객체 사이의 어댑터이다.
        Object principal =  authentication.getPrincipal();

        // User.builder()에서 지정한 role가 들어가있음
        Collection<? extends GrantedAuthority> authorities = authentication.getAuthorities();

        // null이 나옴
        Object credentials = authentication.getCredentials();

        boolean authenticated = authentication.isAuthenticated();
    }
  }

```


###SecurityContextHolder, AuthenticationManager   18p
Authentication정보를 담고있는 SecurityContextHolder. 그리고 인증을 담당하는 AuthenticationManager


### 인증을 하는 AuthenticationManager(ProviderManager를 보통 많이씀)의 흐름 (인증 연관)
스프링 시큐리티에서 인증(Authentication)은 AuthenticationManager가 한다.

AuthenticationManager인터페이스는 아래 메소드 하나만 갖는다.
`Authentication authenticate(Authentication authentication) throws AuthenticationException;`
인자로 받은 Authentication이 유효한 인증인지 확인하고 유효하다면 Authentication 객체를 리턴한다.
인자 Authentication는 사용자가 입력한 인증에 필요한 정보(username, password)로 만든 객체임. (폼 인증인 경우)
인증을 확인하는 과정에서 비활성 계정, 잘못된 비번, 잠긴 계정 등의 에러를 던질 수 있다.

AuthenticationManager 구현체로는 ProviderManager를 보통 많이쓴다.

인증이 들어오면 ProviderManager 의 authenticate() 메소드가 호출되는데
ProviderManager는 private List<AuthenticationProvider> providers; 를 갖고있는데 얘네들로 처리 가능한 인증인지 확인해보고 자신이 처리하지 못하는 인증일 경우  this.parent.authenticate(authentication); 를 통해 부모의 authenticate() 메소드를 호출한다.

인증이 가능한 providers를 갖고있는 ProviderManager에 가게되면 provider.authenticate() 메소드를 호출하게 되고

UserDetailsService를 사용했을 경우 AbstractUserDetailsAuthenticationProvider(추상클래스임) 로 들어가고  this.retrieveUser() 메소드를 호출하게 된다.

그 후 DaoAuthenticationProvider(AbstractUserDetailsAuthenticationProvider를 상속한클래스) 의 retrieveUser()로 들어오고  this.getUserDetailsService() 를 활용하는데 이는 위에서 만든 UserDetailsService를 implements한 AccountService이다.


그 후 DaoAuthenticationProvider를 빠져나와  AbstractUserDetailsAuthenticationProvider 안에서 this.preAuthenticationChecks.check(user);를 하는데 여기서 추가적인 체크를 진행한다. (계정잠금유무, 비활성 계정 등)

그 후 ProviderManager 안 authenticate() 에선 result라는 객체(Authentication타입)를 리턴하는데 얘는 곧 AuthenticationManager의 authenticate()메서드에서 리턴하는 객체이다.

result 안에 principal이라고 들어있는데 얘는 아까 위에서 loadUserByUsername() 메소드에서 리턴한 user객체가 들어있다.

또한 이 result(Authentication타입)가 SecurityContextHolder안에 들어가게 된다.

`정리` : 인증을 담당하는 AuthenticationManager 인터페이스를 구현한 ProviderManager는 AuthenticationProvider를 리스트로 갖고이으며 갖고있는 리스트로 인증을 처리할 수 없을 경우 this.parent.authenticate(authentication); 메소드를 호출하여 부모의 리스트를 통해 인증을 진행한다. UserDetailsService를 사용했을 경우 AbstractUserDetailsAuthenticationProvider -> DaoAuthenticationProvider 를 거치게 되고 retrieveUser()메소드가 호출되는데 여기선 UserDetailsService를 implements한 객체가 사용되며 loadUserByUsername() 메소드가 사용된다. 그 후  this.preAuthenticationChecks.check(user); 를 하는데 여기서 추가적인 체크를 진행한다. (계정잠금유무 등) 최종적으로 result라는 객체(Authentication타입)를 리턴하게 되고 result 안에 principal이라고 들어있는데 얘는 아까 위에서 loadUserByUsername() 메소드에서 리턴한 user객체가 들어있다. 또한 이 객체(result)는  SecurityContextHolder안에서 찾을 수 있다.



#### ThreadLocal
한 쓰레드안에서 변수를 공유

Java.lang 패키지에서 제공하는 쓰레드 범위 변수. 즉, 쓰레드 수준의 데이터 저장소.
●	같은 쓰레드 내에서만 공유.
●	따라서 같은 쓰레드라면 해당 데이터를 메소드 매개변수로 넘겨줄 필요 없음.
●	SecurityContextHolder의 기본 전략.


```java
public class AccountContext {

    private static final ThreadLocal<Account> ACCOUNT_THREAD_LOCAL
            = new ThreadLocal<>();

    public static void setAccount(Account account) {
        ACCOUNT_THREAD_LOCAL.set(account);
    }

    public static Account getAccount() {
        return ACCOUNT_THREAD_LOCAL.get();
    }

}

```


### 13.	Authencation과 SecurityContextHolder (인증 연관)
AuthenticationManager가 인증을 마친 뒤 리턴 받은 Authentication 객체의 행방은?
SecurityContextHolder에 Authencation이 언제들어가는지 확인해보자.

크게 UsernamePasswordAuthenticationFilter, SecurityContextPersisenceFilter 가  Authencation 객체를 SecurityContextHolder에 넣어준다.(위 예제의 경우)

UsernamePasswordAuthenticationFilter
●	폼 인증을 처리하는 시큐리티 필터
●	인증된 Authentication 객체를 SecurityContextHolder에 넣어주는 필터
●	SecurityContextHolder.getContext().setAuthentication(authentication)

SecurityContextPersistenceFilter
●	SecurityContext를 HTTP session에 캐시(기본 전략)하여 여러 요청에서 Authentication을 공유할 수 있게하는 필터.
●	SecurityContextRepository를 교체하여 세션을 HTTP session이 아닌 다른 곳에 저장하는 것도 가능하다.

`로그인 전 상세흐름`

처음 SecurityContextPersistenceFilter필터에 걸리는데 SecurityContextPersistenceFilter는 캐싱하고 있던 SecurityContext를 매 요청마다 복구하려 한다.
**`SecurityContextPersistenceFilter.java`**
```java
SecurityContext contextBeforeChainExecution = repo.loadContext(holder);		
try {
			SecurityContextHolder.setContext(contextBeforeChainExecution);
      ...
}
```
그 후 UsernamePasswordAuthenticationFilter에 걸리게 되는데(폼 인증일 때)
AuthenticationManager 를 가져와 authenticate()를 실행한다. 이 때 ProviderManager를 통해 인증이 벌어진다.
즉 UsernamePasswordAuthenticationFilter가 AuthenticationManager(ProviderManager가 얘를 구현함)를 쓰는것임


**`UsernamePasswordAuthenticationFilter.java`**
```java
return this.getAuthenticationManager().authenticate(authRequest);
```


인증이 정상적으로 일어나게 되면 UsernamePasswordAuthenticationFilter 가 상속한 AbstractAuthenticationProcessingFilter로 가게 되고 AbstractAuthenticationProcessingFilter는 UsernamePasswordAuthenticationFilter의 attemptAuthentication() 메소드를 통해 얻은 인증결과인 authResult를 활용하여 successfulAuthentication() 메소드를 호출하고

**`AbstractAuthenticationProcessingFilter.java`**
```java
public void doFilter(ServletRequest req, ServletResponse res, FilterChain chain)
			throws IOException, ServletException {
    // attemptAuthentication는 추상메서드로써 UsernamePasswordAuthenticationFilter이 구현한걸 사용함
    authResult = attemptAuthentication(request, response);    
    successfulAuthentication(request, response, chain, authResult);
}
```
successfulAuthentication() 메소드를 따라가면 SecurityContextHolder에 인증을 넣는것을 볼 수 있다.

**`AbstractAuthenticationProcessingFilter.java`**
```java
protected void successfulAuthentication(HttpServletRequest request,
    HttpServletResponse response, FilterChain chain, Authentication authResult) {
      ...
      SecurityContextHolder.getContext().setAuthentication(authResult);
      ...
}
```

정리하자면 AbstractAuthenticationProcessingFilter는 템플릿 메소드 패턴이며  UsernamePasswordAuthenticationFilter은 attemptAuthentication()메소드를 구현한것이다.


`로그인 후 상세흐름`
SecurityContextPersistenceFilter가 먼저 받고 SecurityContextRepository(기본적인 실 구현체 HttpSessionSecurityContextRepository) 에서 SecurityContext를 가져오고 SecurityContextHolder 에 다시 넣어준다.

**`SecurityContextPersistenceFilter.java`**
```java
private SecurityContextRepository repo;

SecurityContext contextBeforeChainExecution = repo.loadContext(holder);
try {
			SecurityContextHolder.setContext(contextBeforeChainExecution);
      chain.doFilter(holder.getRequest(), holder.getResponse());
}	finally {
      ...
      SecurityContextHolder.clearContext();
      ...
}
...

```
즉 SecurityContextPersistenceFilter는 매 요청마다 SecurityContextHolder에 SecurityContext를 넣어주고 비우고를 반복한다.


총 15개정도의 필터가 있다고 한다..




### 스프링 시큐리티 Filter와 FilterChainProxy
SecurityContextPersistenceFilter, UsernamePasswordAuthenticationFilter가 어디서 어떻게 호출 되는지 보자


FilterChainProxy에 getFilters()메소드가 있는데 urlpattern이 매치가 되면 매칭하는필터들을 가져오고 필터들을 순회하며 순차적으로 실행한다.
즉 filterChains중 하나의 chain에서 필터들을 찾아서 적용한다. (각 SecurityFilterChain은 여러개의 필터를 갖고있음)
**`FilterChainProxy.java`**
```java
private List<Filter> getFilters(HttpServletRequest request) {
  for (SecurityFilterChain chain : filterChains) {
    if (chain.matches(request)) {
      return chain.getFilters();
    }
  }

  return null;
}

private static class VirtualFilterChain implements FilterChain {
  ...
  @Override
  public void doFilter(ServletRequest request, ServletResponse response) {
    ...
    currentPosition++;
    Filter nextFilter = additionalFilters.get(currentPosition - 1);
    nextFilter.doFilter(request, response, this);
    ...
  }
  ...
}
```

위 getFilters() 에서 순회되는 filterChains(springSecurityFilterChain 리스트)는 내가 생성한 SecurityConfig(@configure가 붙어있고 WebSecurityConfigureAdapter를 상속한 애)가 된다. 즉 SecurityConfig가 여러개가 있으면 filterChains는 여러개가 됨.

> WebSecurity는  WebSecurityConfiguration 으로 만들어지며 WebSecurity를 활용하여 filterChainProxy를 만든다. 이 체인이 바로  DelegatingFilterProxy가 위임하는 체인 프록시이다. 또한 시큐리티 필터들도 WebSecurity가 만들게 된다,

filterChains안에 필터들이 들어있다.

스프링 시큐리티가 제공하는 필터들
1.	WebAsyncManagerIntergrationFilter
2.	SecurityContextPersistenceFilter
3.	HeaderWriterFilter
4.	CsrfFilter
5.	LogoutFilter
6.	UsernamePasswordAuthenticationFilter
7.	DefaultLoginPageGeneratingFilter
8.	DefaultLogoutPageGeneratingFilter
9.	BasicAuthenticationFilter
10.	RequestCacheAwareFtiler
11.	SecurityContextHolderAwareReqeustFilter
12.	AnonymouseAuthenticationFilter
13.	SessionManagementFilter
14.	ExeptionTranslationFilter
15.	FilterSecurityInterceptor

이 모든 필터는 FilterChainProxy가 갖고있으며 사용한다.(SecurityFilterChain 마다 갖고있는 필터가 다름)


### 15.	DelegatingFilterProxy와 FilterChainProxy
서블릿 필터 구현체중 하나인 DelegatingFilterProxy. 누군가에게 위임하는 프록시.
즉 자기가 직접 처리하지 않고 스프링 내 bean에게 위임함.
DelegatingFilterProxy가 FilterChainProxy를 호출하는것

DelegatingFilterProxy
●	일반적인 서블릿 필터.
●	서블릿 필터 처리를 스프링에 들어있는 빈으로 위임하고 싶을 때 사용하는 서블릿 필터.
●	타겟 빈 이름을 설정한다.
●	스프링 부트 없이 스프링 시큐리티 설정할 때는 AbstractSecurityWebApplicationInitializer를 사용해서 등록.
●	스프링 부트를 사용할 때는 자동으로 등록 된다. (SecurityFilterAutoConfiguration)

FilterChainProxy
●	보통 “springSecurityFilterChain” 이라는 이름의 빈으로 등록된다


`스프링부트 사용시`
DelegatingFilterProxy -> SecurityFilterAutoConfiguration -> FilterChainProxy -> filter list 호출


### 16.	AccessDecisionManager (인가 연관)
`인가`를 할 땐 AccessDecisionManager (인터페이스임)를 쓴다.
AccessDecisionManager는 여러개의 AccessDecisionVoter(투표자)가 있다,


Access Control 결정을 내리는 인터페이스로, 구현체 3가지를 기본으로 제공한다.
●	AffirmativeBased: 여러 Voter중에 한명이라도 허용하면 허용. (디폴트)
●	ConsensusBased: 다수결
●	UnanimousBased: 만장일치


AccessDecisionVoter
●	해당 Authentication이 특정한 Object에 접근할 때 필요한 ConfigAttributes를 만족하는지 확인한다.
●	WebExpressionVoter: 웹 시큐리티에서 사용하는 기본 구현체, ROLE_Xxxx가 매치하는지 확인.
●	RoleHierarchyVoter: 계층형 ROLE 지원. ADMIN > MANAGER > USER


AffirmativeBased 를 보면 아래와같은것들이 있다. 투표를 진행하는 코드임. 하나라도 ACCESS_GRANTED이면 승인함
**`AffirmativeBased.java`**
```java
public class AffirmativeBased extends AbstractAccessDecisionManager {
  ...
  public void decide(Authentication authentication, Object object,
			Collection<ConfigAttribute> configAttributes) throws AccessDeniedException {
		int deny = 0;
    ...
		for (AccessDecisionVoter voter : getDecisionVoters()) {
			int result = voter.vote(authentication, object, configAttributes);
      ...
    }

    switch (result) {
    case AccessDecisionVoter.ACCESS_GRANTED:
      return;

    case AccessDecisionVoter.ACCESS_DENIED:
      deny++;

      break;

    default:
      break;
    }

    if (deny > 0) {
			throw new AccessDeniedException(messages.getMessage(
					"AbstractAccessDecisionManager.accessDenied", "Access is denied"));
		}
    ...
}
}
```



admin은 일반 user권한 페이지 또한 접근이 가능해야한다. 이를 위해 아래처럼 수정해보자.
roleHierarchy 를 등록하기 위해 아래절차를 거치는것임. roleHierarchy 를 추가한것 외엔 기존 AffirmativeBased 디폴트 설정과 동일하게 쓰는것임

> 위에서 말했듯이 AccessDecisionManager를 등록해놓지 않으면 기본적으로 AffirmativeBased를 쓰게 된다.

**`SecurityConfig.java`**
```java
@Configuration
@EnableWebSecurity
public class SecurityConfig extends WebSecurityConfigurerAdapter {

public AccessDecisionManager accessDecisionManager() {

  RoleHierarchyImpl roleHierarchy = new RoleHierarchyImpl();
  roleHierarchy.setHierarchy("ROLE_ADMIN > ROLE_USER");

  DefaultWebSecurityExpressionHandler handler = new DefaultWebSecurityExpressionHandler();
  handler.setRoleHierarchy(roleHierarchy);

  WebExpressionVoter webExpressionVoter = new WebExpressionVoter();
  webExpressionVoter.setExpressionHandler(handler);

  List<AccessDecisionVoter<? extends Object>> voters = Arrays.asList(webExpressionVoter);
  return new AffirmativeBased(voters);
}

protected void configure(HttpSecurity http) throws Exception {
  http.authorizeRequests()
    .mvcMatchers("/", "/info", "/account/**").permitAll()
    .mvcMatchers("/admin").hasRole("ADMIN")
    .mvcMatchers("/user").hasRole("USER")
    .anyRequest().authenticated()
    .accessDecisionManager(accessDecisionManager())
    ;
  http.formLogin();
  http.httpBasic();

}

}
```

아래는 AccessDecisionManager를 오버라이딩 하는방식이 아닌 AccessDecisionManager가 사용하는 expressionHandler만 교체해서 사용함 . 더욱 간단해보이지?

```java
@Configuration
@EnableWebSecurity
public class SecurityConfig extends WebSecurityConfigurerAdapter {

    public SecurityExpressionHandler expressionHandler() {
        RoleHierarchyImpl roleHierarchy = new RoleHierarchyImpl();
        roleHierarchy.setHierarchy("ROLE_ADMIN > ROLE_USER");

        DefaultWebSecurityExpressionHandler handler = new DefaultWebSecurityExpressionHandler();
        handler.setRoleHierarchy(roleHierarchy);

        return handler;
    }

    @Override
    protected void configure(HttpSecurity http) throws Exception {
        http.authorizeRequests()
                .mvcMatchers("/", "/info", "/account/**").permitAll()
                .mvcMatchers("/admin").hasRole("ADMIN")
                .mvcMatchers("/user").hasRole("USER")
                .anyRequest().authenticated()
                .expressionHandler(expressionHandler());
        http.formLogin();
        http.httpBasic();
    }

}


```

### FilterSecurityInterceptor (인가 연관)
AccessDecisionManager는 도대체 어디서 사용하는것일까? FilterSecurityInterceptor!

FilterSecurityInterceptor란?
AccessDecisionManager를 사용하여 Access Control 또는 예외 처리 하는 필터.
FilterChainProxy가 들고있는 여러개의 필터중 하나이며 대부분의 경우 제일 마지막 필터로 들어있다.


인증이 마지막에 ConfigAttributes를 만족하는지 확인하는 필터가 되겠다.


FilterSecurityInterceptor의 부모클래스인 AbstractSecurityInterceptor를 보면
accessDecisionManager를 활용해서 decision을 한다.
**`AbstractSecurityInterceptor.java`**
```java
try {
  this.accessDecisionManager.decide(authenticated, object, attributes);
}
```


### 19.	ExceptionTranslationFilter (인증,인가 연관)

FilterSecurityInterceptor에서 발생한 AccessDeniedException과 AuthenticationException을 처리하는 필터
(정확히는 FilterSecurityInterceptor 상위클래스인	AbstractSecurityInterceptor 에서 발생한것을 처리)


AuthenticationException 발생 시
●	AuthenticationEntryPoint 실행
●	AbstractSecurityInterceptor 하위 클래스(예, FilterSecurityInterceptor)에서 발생하는 예외만 처리.
●	그렇다면 UsernamePasswordAuthenticationFilter에서 발생한 인증 에러는? ExceptionTranslationFilter 가 처리하지 않고 UsernamePasswordAuthenticationFilter 상위클래스인 AbstractAuthenticationProcessingFilter에서 처리됨

**`AbstractAuthenticationProcessingFilter.java`**
```java
catch (AuthenticationException failed) {
  // Authentication failed
  unsuccessfulAuthentication(request, response, failed);

  return;
}
```

AccessDeniedException 발생 시
●	익명 사용자라면 AuthenticationEntryPoint 실행 - 로그인이 안된 사용자가 admin있는 페이지 접속
●	익명 사용자가 아니면 AccessDeniedHandler에게 위임 - 로그인이 된 user 사용자가 admin 페이지 접속


ExceptionTranslationFilter 생김새는 다음과 같다
**`ExceptionTranslationFilter.java`**
```java
		if (exception instanceof AuthenticationException) {
      ...
    }
		else if (exception instanceof AccessDeniedException) {
      if (authenticationTrustResolver.isAnonymous(authentication)) {
        ...
      }
      else {
        ...
      }
      ...
    }
```









SecurityContextHolder - Authentication정보를 담고있는 SecurityContextHolder
AuthenticationManager(ProviderManager를 보통 많이씀) - 인증(Authentication)은 AuthenticationManager가 한다.
AuthenticationProvider - ProviderManager가 리스트로 갖고있음
SecurityContextPersisenceFilter - SecurityContext를 HTTP session에 캐시(기본 전략)하여 여러 요청에서 Authentication을 공유할 수 있게하는 필터.
UsernamePasswordAuthenticationFilter - 폼 인증을 처리하는 시큐리티 필터/인증된 Authentication 객체를 SecurityContextHolder에 넣어주는 필터
AbstractAuthenticationProcessingFilter - UsernamePasswordAuthenticationFilter가 상속하는 필터(추상클래스).
FilterChainProxy - filterChains를 활용해

AccessDecisionManager - 인가를 할 땐 AccessDecisionManager 를 쓴다. 여러개의 AccessDecisionVoter(투표자)가 있으며 AccessDecisionManager 구현체로는 최소하나(디폴트), 다수결, 만장일치 가 있다.
FilterSecurityInterceptor - AccessDecisionManager는 도대체 어디서 사용하는것일까? FilterSecurityInterceptor!
ExceptionTranslationFilter - FilterSecurityInterceptor에서 발생한 AccessDeniedException과 AuthenticationException을 처리하는 필터






### ignoring()
시큐리티 설정 중 하나임.

지금까진 모든 요청은 필터를 활용해왔다.
하지만 static 데이터들은 필터들을 적용하고 싶지 않다!(ex 파비콘아이콘)

스프링 부트가 제공하는 PathRequest를 사용해서 정적 자원 요청을 스프링 시큐리티 필터를 적용하지 않도록 설정.
```java
@Override
public void configure(WebSecurity web) throws Exception {
           web.ignoring().requestMatchers(PathRequest.toStaticResources().atCommonLocations());
}
```
아래처럼 해도 되지 않나??
```java
@Override
public void configure(WebSecurity web) throws Exception {
           http.authorizeRequests()
                     .mvcMatchers("/").permitAll()
                     .mvcMatchers("/admin").hasRole("ADMIN")
                     .requestMatchers(PathRequest.toStaticResources().atCommonLocations()).permitAll()
}
```

해도 되긴하는데 아래 방식은 추천하지 않음.
위의 경우 거치는 필터가 0개지만
아래 방식으로 할 경우 15개 필터를 모두 거치게 된다

특정 리소스에 대해 인증, 인가를 거치고 싶으면 위에서 .exclude()를 활용하면 된다,

요약
이런 설정으로도 같은 결과를 볼 수는 있지만 스프링 시큐리티 필터가 적용된다는 차이가 있다.
● 동적 리소스는 http.authorizeRequests()에서 처리하는 것을 권장합니다.
● 정적 리소스는 WebSecurity.ignore()를 권장하며 예외적인 정적 자원 (인증이 필요한
정적자원이 있는 경우)는 http.authorizeRequests()를 사용할 수 있습니다.




### 시큐리티 필터중 최상위 클래스인 WebAsyncManagerIntegrationFilter

스프링 mvc async handler를 지원하는 핸들러임
시큐리티 컨텍스트가 threadlocal을 사용하기 때문에 동일 스레드에서만 시큐리티 컨텍스트를 쓸 수 있는데
다른 쓰레드에서도 시큐리티 컨텍스트를 쓸 수 있도록 도와주는 필터가 WebAsyncManagerIntegrationFilter


스프링 MVC의 Async 기능(핸들러에서 Callable을 리턴할 수 있는 기능)을 사용할 때에도 SecurityContext를 공유하도록 도와주는 필터.

WebAsyncManagerIntegrationFilter
● PreProcess: SecurityContext를 설정한다.
● Callable: 비록 다른 쓰레드지만 그 안에서는 동일한 SecurityContext를 참조할 수 있다.
● PostProcess: SecurityContext를 정리(clean up)한다.



아래처럼 코드를 구성했을 때 Callable에 대한 설명
           call메소드를 호출하고 리퀘스트를 처리하고 있던 쓰레드를 반환 후 call()이 완료되면 그 때 응답을 보냄. 두페이지로 처리하는것!
```java
           @GetMapping("/async-handler")
           @ResponseBody
           public Callalbe<String> syncHandler() {
                     return new Callable<String> () {
                                @Override
                                public String call() throws Exception {
                                          return null;
                                }
                     }
           }
```

아래를 돌려보면 쓰레드는 다르지만 Authentication정보는 유지된다.. 이를 WebAsyncManagerIntegrationFilter가 해주는것이다.

```java
           @GetMapping("/async-handler")
           @ResponseBody
           public Callalbe<String> syncHandler() {
                     //톰캣이 할당한 NIO 쓰레드 부분
                     log("MVC");
                     return new Callable<String> () {
                                @Override
                                public String call() throws Exception {
                                          //별도의 쓰레드 영역
                                          SecurityLogger.log("Callable");
                                          return "async handler";
                                }
                     }
           }



```

```java

public class SecurityLogger {
           public void log(String message) {
                     System.out.println(message);
                     Thread thread = Thread.getCurrentThread();
                     System.out.println(threade.getName());
                     Object principal = SecurityContextHolder.getContext().getAuthentication().getPrincipal();
                     System.out.println("principal: " + principal);

           }
}

```

### 스프링 시큐리티와 @Async

> @Async를 사용하기 위해선 @EnableAsync 를 붙여야함(추가로 쓰레드풀 설정을 해줘야 더 올바르게 쓸 수 있다)


@Async를 사용한 서비스를 호출하는 경우
● 쓰레드가 다르기 때문에 SecurityContext를 공유받지 못한다.

```java
           @GetMappint("/async-service")
           @ResponseBody
           public String asyncService() {
                     SecurityLogger.log("MVC, before async service");
                     samplerService.asyncService();
                     SecurityLogger.log("MVC, after async service");

                     return "Async Service";
           }

```


```java
           @Async
           public void asyncService() {
                     SecurityLogger.log("async Service"); // NPE 발생
           }

```


@Async를 사용하면 서비스 안에서 SecurityContextHolder.getContext().getAuthentication().getPrincipal(); 에서 NPE가 찍히는것을 확인할 수 있다..
기본적으로 SecurityContextHolder는 strategy를 선택할 수 있다. 즉 SecurityContextHolder를 어디까지 유지할것인가를 선택할 수 있다(기본은 ThreadLocal. 즉 쓰레드 내)

아래처럼 설정하면 현재 쓰레드에서 생성하는 하위 쓰레드까지 공유가 된다
● SecurityContext를 자식 쓰레드에도 공유하는 전략.
● @Async를 처리하는 쓰레드에서도 SecurityContext를 공유받을 수 있다.

```java
proteceted void configure(HttpSecurty http) throws Exception {
           http.authorizeRequests()
           .mvcMatchers("/").permitAll()
           ....

           SecurityContextHolder.setStrategyName(SecurityContextHolder.MODE_INHERITABLETHREADLOCAL);

}
```


### SecurityContext 영속화 필터: SecurityContextPersistenceFilter
여러 요청간 시큐리티 콘텍스트를 공유할 수 있게해주는 필터로써 두번째에 위치한다(첫번째는 WebAsyncManagerIntegrationFilter)
최초 인증 후 재 리퀘스트를 날려도 인증정보가 유지됨.

SecurityContextRepository를 사용하는데 SecurityContextRepository의 기본 구현체가 HTTPSessionSecurityContextRepository이다.
즉 HTTPSession에서 읽어오는것이다. 처음엔 HTTPSession엔 아무런 정보가 없는데 비어있을 때 비어있는 시큐리티 컨텍스트를 만들때도 SecurityContextPersistenceFilter가 활용이 된다. 기존에 HTTPSesion에 시큐리티 컨텍스트가 저장되어 있는경우에도 가져와 활용한다.
그러므로 모든 인증 필터들 보다 위에 있어야 한다. 필터 중 두번째에 위치함

SecurityContextRepository를 사용해서 기존의 SecurityContext를 읽어오거나 초기화 한다.
● 기본으로 사용하는 전략은 HTTP Session을 사용한다.
● Spring Session과 연동하여 세션 클러스터를 구현할 수 있다

### 시큐리티 관련 헤더 추가하는 필터: HeaderWriterFilter
직접 신경쓰지 않아도 되는필터임

응답 헤더에 시큐리티 관련 헤더를 추가해주는 필터로써 세번째 필터임(첫번째는 WebAsyncManagerIntegrationFilter/ 두번째는 SecurityContextPersistenceFilter / 세번째는 HeaderWriterFilter)


XContentTypeOptionsHeaderWriter: 마임 타입 스니핑 방어.
XXssProtectionHeaderWriter: 브라우저에 내장된 XSS 필터 적용.
CacheControlHeadersWriter: 캐시 히스토리 취약점 방어.
HstsHeaderWriter: HTTPS로만 소통하도록 강제.
XFrameOptionsHeaderWriter: clickjacking 방어.


Cache-Control: no-cache, no-store, max-age=0, must-revalidate
Content-Language: en-US
Content-Type: text/html;charset=UTF-8
Date: Sun, 04 Aug 2019 16:25:10 GMT
Expires: 0
Pragma: no-cache
Transfer-Encoding: chunked
X-Content-Type-Options: nosniff
X-Frame-Options: DENY
X-XSS-Protection: 1; mode=block


기본적으로 다섯개의 HeaderWriter가 적용된다.
● XContentTypeOptionsHeaderWriter: 마임 타입 스니핑 방어.
브라우저가 마임타입을 판단하려고 컨텐츠를 분석하는 경우가 있다. 그럴 경우 보안상 이슈가 발생할 수 있다.
X-Content-Type-Options: nosniff 을 주면 반드시 Content-Type으로만 랜더링 하도록 되어있다. 즉 브라우저가 추가적인 실행을 하지 않기 때문에 보안상 안전하다

● XXssProtectionHeaderWriter: 브라우저에 내장된 XSS 필터 적용.
XSS를 방어해주는것. 브라우저마다 내장된 XSS 필터가 있는데 이 필터로 공격을 방어할 순 없으나 최소한 1차적으로 공격을 걸러낼 수 있다.
X-XSS-Protection: 1; mode=block 에서 1이 그 기능을 활성화 하는것이고 mode=block가 막아준다는 옵션임
이걸 켜 놓고 부가적으로 커스텀한 XSS 필터를 적용할 수 있다..

● CacheControlHeadersWriter: 캐시 히스토리 취약점 방어.
Cache-Control: no-cache, no-store, max-age=0, must-revalidate
Expires: 0
Pragma: no-cache
위 세개 옵션을 활용해서 캐시를 쓰지 않도록 설정하는것.
정적인 리소스는 캐시를 쓰면 좋지만 동적인 리소스는 민감한 정보가 포함되어 있을 수 있기때문에 그런경우에 대한 방어를 하고자 캐시를 비워주는것

● HstsHeaderWriter: HTTPS로만 소통하도록 강제.
Strict-Transport-Security: max-age=31536000; includeSubDomains 이런 정보가 샘플로 나간다.

● XFrameOptionsHeaderWriter: clickjacking 방어.
iframe, object, http 그런걸 넣을 수 있는데 보이지 않은 영역에 뭘 누르면 내 정보가 가도록 설정되어 있을 수 있다. 그걸 clickjacking 이라 하는데 이걸 방어하는것으로 아래 헤더정보를 추가한다.
X-Frame-Options: DENY


### CSRF 어택 방지 필터: CsrfFilter (네번째 필터)

CSRF 어택을 방지하는 필터임
Cross site request forgery - 원치않는 요청을 임의로 만들어서 보내는것.
인증된 유저의 계정을 사용해 악의적인 변경 요청을 만들어 보내는 기법.
은행사이트에 로그인 후 나쁜사이트에 접속했는데 나쁜사이트에서 재밌어 보이는걸 눌렀는데 은행으로 요청을 보냄

모든 대부분의 요청은 same origin이라 해서 요청하는곳이 일치해야하지만 경우에 따라서 다른 도메인에서의 요청을 허용해야 하는 경우가 있다.
이 경우 인증서를 사용하거나 특정 도메인들한테 열어주는것(CORS)가 있다.
CORS를 사용할 때 특히 주의 해야 함. 타 도메인에서 보내오는 요청을 허용하기 때문에...

스프링시큐리티에선 이 경우 CsrfFilter을 활용해서 특정한 토큰(CSRF 토큰)을 활용한다.
이를 활용하여 리소스를 변경하는 요청의 경우 서버에서 발급한 토큰이 있는지 확인한다.

은행이 만들어준 form에는 csrf 토큰이 들어있고 나쁜 사이트가 만들어준 form에는 csrf 토큰이 없기 때문에 은행서버에선 잘못된 요청을 인지할 수 있다.
(스프링 시큐리티에서 제공하는 기본 로그인 화면에서도 폼에 csrf가 설정되어있는것을 확인할 수 있다.)
restapi에서도 csrf 토큰을 적용할 순 있다. form 기반에선 리소스를 변경하는 요청에는 csrf 토큰을 활용하는것을 추천한다.

CsrfFilter.java
```java

if(csrfToken.getToken().equals(actualToken)) {

}
```

csrf 토큰을 사용하지 않고 싶을 땐 http.csrf().disable(); 를 추가해주면 됨

```java
proteceted void configure(HttpSecurty http) throws Exception {
           http.authorizeRequests()
           .mvcMatchers("/").permitAll()
           ....

           http.csrf().disable();

}
```


### 28.	CSRF 토큰 사용 예제
JSP에서 스프링 MVC가 제공하는 <form:form> 태그 또는 타임리프 2.1+ 버전을 사용할 때 폼에 CRSF 히든 필드가 기본으로 생성 됨.
get요청인 경우 csrf 토큰 확인안하지만 post 요청인 경우 확인한다
포스트맨으로 post로 form으로 로그인 api 호출하면 401이 발생한다.

```java
@RunWith(SpringRunner.class)
@SpringBootTest
@AutoConfigureMockMvc
public class SignUpControllerTest {

    @Autowired
    MockMvc mockMvc;

    @Test
    public void signUpForm() throws Exception {
        mockMvc.perform(get("/signup"))
                .andDo(print())
                .andExpect(content().string(containsString("_csrf")));
    }

    @Test
    public void procesSignUp() throws Exception {
        mockMvc.perform(post("/signup")
            .param("username", "keesun")
            .param("password", "123")
            .with(csrf()))
                .andExpect(status().is3xxRedirection());
    }
}

```


### 29.	로그아웃 처리 필터: LogoutFilter
LogoutHandler, LogoutSuccessHandler가 있다.

LogoutHandler는 composite 객체로써 다른 여러가지 핸들러를 감싸고 있는 composite타입이여서 사실상 여러개의 핸들러를 사용하고 있는것임
**`CompositeLogoutHandler.java`**
```java
public final class CompositeLogoutHandler implements LogoutHandler {

private final List<LogoutHandler> logoutHandlers;

public CompositeLogoutHandler(LogoutHandler... logoutHandlers) {
  Assert.notEmpty(logoutHandlers, "LogoutHandlers are required");
  this.logoutHandlers = Arrays.asList(logoutHandlers);
}

@Override
public void logout(HttpServletRequest request, HttpServletResponse response, Authentication authentication) {
  for (LogoutHandler handler : this.logoutHandlers) {
    handler.logout(request, response, authentication);
  }
}
}
```

LogoutSuccessHandler는 로그아웃을 끝내고 난 후 어떤처리를 할것인지에 대한 핸들러임

기본으론 SimplUrlLogoutSuccessHandler가 명시되어있는 쪽으로 이동시켜 준다.


로그아웃 페이지로 가면 로그아웃 창이 뜨는데 이는 DefaultLogoutPageGeneratingFilter가 만들어 준것이다.

거기서 로그아웃 버튼을 눌렀을 때 (실제로그아웃이 일어났을 때) 로그아웃 필터가 해준다.

**`LogoutFilter.java`**
```java
if (requiresLogout(request, response)) {  //로그아웃요청만 if문안에 들어감
  Authentication auth = SecurityContextHolder.getContext().getAuthentication();

  this.handler.logout(request, response, auth);
  logoutSuccessHandler.onLogoutSuccess(request, response, auth);
  return;
}
```



위에서 logout를 호출하면 CompositeLogoutHandler logout로 오게되는데 기본적으로
얘는 여러 로그아웃 핸들러를 들고있고(this.logoutHandlers) 우리가 임의로 추가할 수 있다.

기본적으로는 2개를 들고있는데
하나는 CsrfLogoutHandlers이고 하나는 SecurityContextLogoutHandler이다.
**`CompositeLogoutHandler.java`**
```java
@Override
	public void logout(HttpServletRequest request, HttpServletResponse response, Authentication authentication) {
		for (LogoutHandler handler : this.logoutHandlers) {
			handler.logout(request, response, authentication);
		}
	}
```

logout 후 위에서 logoutSuccessHandler.onLogoutSuccess를 호출하게 되는데 기본적으로 로그인 페이지로 가도록 되어있다.


근데 얘를 / 로 바꾸고 싶으면 아래처럼 하면 된다.
```java

@Override
protected void configure(HttpSecurity http) throws Exception {
http.logout()
        .loginUrl("/logout")    // logout 하는 주소
        .logoutSuccessUrl("/"); // logout 후 주소
}
```




### 폼 인증 처리 필터: UsernamePasswordAuthenticationFilter

로그인 버튼을 눌렀을 때 이를 처리하는것이 UsernamePasswordAuthenticationFilter
UsernamePasswordAuthenticationFilter는 사용자가 폼에 입력한 username과 password로 Authentcation을 만들고
AuthenticationManager를 사용하여 인증을 시도한다

UsernamePasswordAuthenticationFilter 를 보면 UsernamePasswordAuthenticationToken을 만듦. 이후 AuthenticationManager를 활용해 인증을 시도함.
```java
UsernamePasswordAuthenticationToken authRequest = UsernamePasswordAuthenticationToken(username, password);
...
return this.getAuthenticationManager().authenticate(authRequest);
```

AuthenticationManager는 AuthenticationManager의 구현체인 ProviderManager를 사용하는데 ProviderManager는 여러개의 AuthenticationProvider을 갖고있는데 얘한테 위임해서 인증처리를 위임해서 함.
AuthenticationProvider 또한 자신이 처리하지 못할경우 부모한테 위임해서 처리를 함.

AuthenticationProvider 중에서 DaoAuthenticationProvider를 사용하는데 얘는 UserDetailService를 사용하는데 얘가 바로  UserDetailService를 구현한 (우리가만든)놈이다.

인증이 끝나면 AbstractAuthenticationProcessingFilter(UsernamePasswordAuthenticationFilter 가 상속함) 쪽에서 SecurityContextHolder에다 넣어준다.
```java
SecurityContextHolder.getContext().setAuthentication(authResult);
```


### DefaultLoginPageGeneratingFilter
로그인 페이지를 만들어주는 필터
get으로 로그인 api에 들어오면 로그인 페이지로 넘겨줌

```java
http.formLogin()
           .loginPage("/login")         //얘를 붙이면 DefaultLoginPageGeneratingFilter, DefaultLogoutPageGeneratingFilter 가 등록이 안됨.(LogoutFilter는 유지됨)
                                // 커스텀한 로그인페이지를 쓴다고 인지하기 때문
           .usernameParameter("my-username")         // 파라미터 변경
           .passwordParameter("my-password");
           .
```

### 로그인/로그아웃 폼 커스터마이징
```java
http.formLogin()
           .loginPage("/signin")                 
          // 이 옵션을 추가하면 DefaultLoginPageGeneratingFilter, DefaultLogoutPageGeneratingFilter 가 등록이 안됨.
           // 즉 로그인, 로그아웃 페이지를 만들어줘야함
           .permitAll();
```

signin 에 get 으로 들어오면 form을 보여주고 post로 들어오면 UsernamePasswordAuthenticationFilter 가 처리한다.

get으로 들어오는 페이지를 보여주는 컨트롤러만 생성하면 됨. post signin은 UsernamePasswordAuthenticationFilter 가 처리해줌

위에서 말한대로 loginPage("/signin") 를 적용하면 Logout 뷰도 사라지기 때문에 새로 만들어줘야한다.






###  Basic 인증 처리 필터: BasicAuthenticationFilter
요청 헤더에 username와 password를 실어 보내면 브라우저 또는 서버가 그 값을 읽어서 인증하는 방식.
예)  Authorization: Basic QWxhZGRpbjpPcGVuU2VzYW1l
QWxhZGRpbjpPcGVuU2VzYW1l <- (keesun:123 을 BASE 64인코딩한것)

보통, 브라우저 기반 요청이 클라이언트의 요청을 처리할 때 자주 사용.
● 보안에 취약하기 때문에 반드시 HTTPS를 사용할 것을 권장.


```java
           http.httpBasic();
```

curl -u keesun:123 http:localhost:8080

BasicAuthenticationFilter
```java
//form 로그인은 form에서 읽어왔지만 Basic 인증의 경우에는 헤더에서 읽어옴
UsernamePasswordAuthenticationToken authRequest = this.authenticationConverter.convert(request);


if (authenticationIsRequired(username)) {
                                          Authentication authResult = this.authenticationManager.authenticate(authRequest);
                                          SecurityContext context = SecurityContextHolder.createEmptyContext();
                                          context.setAuthentication(authResult);
                                          SecurityContextHolder.setContext(context);

                                          this.rememberMeServices.loginSuccess(request, response, authResult);          //이부분 강의시점엔 없는데 추가된듯??
                                          this.securityContextRepository.saveContext(context, request, response);         //이부분 강의시점엔 없는데 추가된듯??
                                          onSuccessfulAuthentication(request, response, authResult);
                                }

```

repo 에 저장하는건 없기에 form 로그인처럼 로그인이 유지되진 않는다. 즉 form로그인과 다르게 stateless 함.
강의시점엔 로그인이 유지되진 않았으나 인증이 유지되도록 변경된듯 하다.


### 요청 캐시 필터: RequestCacheAwareFilter

현재 요청과 관련 있는 캐시된 요청이 있는지 찾아서 적용하는 필터.
● 캐시된 요청이 없다면, 현재 요청 처리
● 캐시된 요청이 있다면, 해당 캐시된 요청 처리

대시보드 페이지로 접속했는데 로그인 페이지로 넘어가게 될 경우 대시보드 요청이 캐시에 저장이 되어있다가 로그인이 완료되면 캐시에 있는걸 꺼내서 대시보드로 연결시켜줌


### 시큐리티 관련 서블릿 스팩 구현 필터: SecurityContextHolderAwareRequestFilter
서블릿3 스펙을 지원하는 일을 함. 그중에서 시큐리티 관련된 스프링 시큐리티 기반으로 구현을 매꿔줌

HttpServletRequest 클래스 안에 authenticate라는 메소드가 있는데 얘를 호출하면 인증여부를 판단하고 안되어 있으면 로그인페이지로 보냄
login메소드는 AuthenticationManager을 활용하서 내부 인증하는것이고
logout메소드는 LogoutHandler로 구현이 되어있고
AsynContext 의 start메소드도 지원하는데 SecurityContextHolder을 지원하는것임. 새로운 스레드에서도 SecurityContextHolder을 복사하여 사용가능케함

시큐리티 관련된 서블릿 api를 지원하는것임. 서블릿을 직접 쓸 일 이 있으면 해당 메소드를 쓰면 됨. 뒷단은 결국 스프링 시큐리티 기반으로 동작함


### 익명 인증 필터: AnonymousAuthenticationFilter
 아무도 인증하지 않은 요청일 때 인증이 안된사용자를 AnonymousAuthentication로 만들어 SecurityContextHolder에 넣어준다
null object pattern 이라 해서 null 대신에 null을 대변하는 객체를 넣는 패턴임.
타 필터를 보면 종종 Anonymous 를 판단해서 분기하는 로직이 종종있다.
principal named은 기본적으로 "anonymousUser"이고
authority은 기본적으로 "ROLE_ANONYMOUS".
아래코드를 활용할 경우 변경할 수 있다.
```java
http.anonymous()
	.principal("auser")
	.authorities("ROLE_A")
	.key()
```

### 세션 관리 필터: SessionManagementFilter
`기능 1. 세션 변조 방지 전략 설정: sessionFixation`
공격자가 웹사이트에 로그인 해서 쿠키를 받아오고 희생자는 공격자의 쿠키로 로그인을 함. 그럼 공격자는 희생자의 정보를 읽어들일 수 있음.
해결방법"인증 후 세션 id나 정보를 바꿔 어택커의 쿠키로 희생자의 정보를 볼 수 없도록 함.
스프링 시큐리티는 세션변조 방지전략이 서블릿 컨테이너에 따라 달라짐.
● none
● newSession
● migrateSession (서블릿 3.0- 컨테이너 사용시 기본값)
● changeSessionId (서브릿 3.1+ 컨테이너 사용시 기본값) - 위에서 말한 세션 id를 바꾸는 전략
서블릿 3.0 이하부터 migrateSession전략을 쓰는데 이는 인증이 되었을 때 새로운 세션을 새로 만들고 세션에 있는 정보들을 복사해 오는것임.
changeSessionId는 copy과정이 없기에 migrateSession보다 빠름
```java
http.sessionManagement()
	.newSession() / .migrateSeesion()/ .changeSessionId() / .none()를 선택할 수 있다.
```

`기능 2. 유효하지 않은 세션을 리다이렉트 시킬 URL 설정`
로그아웃했을 때 해당세션은 유효하지 않기 때문에 어디로 보낼지 설정가능함
```java
http.sessionManagement().invalidSessionUrl("/error");
```

`기능 3. 동시성 제어`
여러 로그인을 막고싶을때 사용함
```java
http.sessionManagement()
	.maximumSession(1)
	.expiredUrl()	// 다중로그인로 인해 만료가 되었을 때 어디로 보낼것인지
	.maxSessionsPreventsLogin(true) // 새로운 세션로그인을 불가능하게 할것이다

```
동시성 제어: maximumSessions
● 추가 로그인을 막을지 여부 설정 (기본값, false)

`기능 4. 세션생성전략`
아래 4가지가 들어갈 수 있다.
● IF_REQUIRED - 기본값. 필요할때 만들어서 사용
● NEVER	- 새로 만들진 않지만 있으면 갖다가 쓴다
● STATELESS	- 세션이 있더라도 쓰지 않음(캐싱된것도 안쓴다)
● ALWAYS

```java
http.sessionManagement()
	.sessionCreationPolicy(IF_REQUIRED)


```
여러개의 서버를 띄울 때 세션을 관리하고자 하면 spring session을 써서 세션 클러스터링을 할 수 있다.

### 인증/인가 예외 처리 필터: ExceptionTranslationFilter
마지막에서 두번째 앞에 위치함
마지막 필터인 FilterSecurityInterceptor와 밀접한 관계가 있음
 ExceptionTranslationFilter가 FilterSecurityInterceptor보다 앞서 위치해서 FilterSecurityInterceptor를 감싸서 실행해야함.
FilterSecurityInterceptor의 구현체가 AccessDecisionManager, AffirmativeBased이고 얘네를 활용해서 인가처리를 하는데 이 때 인증관련예외, 인가 관련예외가 발생할 수 있는데 이게 발생하면 ExceptionTranslationFilter는 각 예외에 맞는 처리를 한다.
AuthenticationException이 발생하면 ExceptionTranslationFilter가 갖고있는 AuthenticationEntryPoint를 사용하여 예외를 처리함(해당 유저를 인증할 수 있게 인증이 가능한 페이지로 보내는것)
AccessDeniedException이 발생하면 ExceptionTranslationFilter가 갖고있는 AccessDeniedHandler를 사용하여 처리를 하는데 기본처리는 403 에러메세지를 보여주는것이다.

AccessDeniedException을 커스텀한 페이지로 보내고 싶다면 간단하게 아래처럼 하면된다.
```java
http.exceptionHandling()
	.accessDeniedPage("/access-denied");
```

서버단에 로그를 남기고 싶다면
```java
http.exceptionHandling()
	.accessDeniedHandler((request, response, accessDeniedException) -> {
  UserDetails principal = (UserDetails) SecurityContextHolder.getContext().getAuthentication().getPrincipal();
  String username = principal.getUsername();
  String servletPath = request.getServletPath();
  System.out.println(username + " is denied to access to " + servletPath);
  response.sendRedirect("/access-denied");
});
```

### 인가 처리 필터: FilterSecurityInterceptor
기본적으로 등록하는 필터중 가장 마지막에 있는 필터
HTTP 리소스 시큐리티 처리를 담당하는 필터. AccessDecisionManager를 사용하여 인가를 처리한다.

antPattern()도 지원되고 mvcMatcher() 또는 regexMatcher()도 사용해도 된다.


```java
http.authorizeRequests()
.mvcMatchers("/", "/info", "/account/**", "/signup").permitAll()
.mvcMatchers("/admin").hasRole("ADMIN")
.mvcMatchers("/user").hasRole("USER")
.anyRequest().authenticated()	//어떤 권한을 갖던 인증을 하기만 하면 접근가능
.anyRequest().fullyAuthenticated() //이전까진 rememberme로 동작하다 중요한 때 다시 인증을 요청하는것
.expressionHandler(expressionHandler());
```
hasRole은 hasAuthority의 하위개념임 .hasRole("USER") .hasAuthority("ROLE_USER") 은 동일하다고 보면 됨
마찬가지로 아래코드에서 .roles(account.getRole()) 는 .authorities("ROLE_"+account.getRole()) 와 같은것임
```java
return User.builder()
	.username(account.getUsername())
	.password(account.getPassword())
	.roles(account.getRole())
	.build();
```
즉 .roles() 는 prefix로 ROLE_가 붙음


### 토큰 기반 인증 필터 : RememberMeAuthenticationFilter
로그인할 때 로그인 기억하기 체크박스를 본적 있을것이다. 세션이 종료되거나 만료되어도 세션보다 더 긴 것으로 쿠키나 토큰이 브라우저에 남아있거나 서버 디비에 남아있거나 함.

```java
http.rememberMe()
.userDetailsService(accountService)
.tokenValiditySeconds(10) //기본 2주가 유지됨
.useSecureCookie(true)
 //https만 쿠키에 접근가능하도록 하는것. https를 적용한다면 true로 해주면 좋다. 쿠키가 노출되면 취약하기에...
.key("remember-me-sample");
```
위를 적용하면 브라우저 내 remember-me-sample 키를 갖는 쿠기가 추가된다.
로그인을 하면 jsseionId, remember-me-sample 두개가 있는것을 확인할 수 있고, jsessionId를 지우고 리프레시를 해도 재로그인이 필요없고 jsessionId가 다시 생긴것을 확인할 수 있다.


jsessionId를 지우고 remember-me-sample만 갖고있는 상황에서 request를 날리면 `SecurityContextHolder.getContext().getAuthentication() == null` 가 충족하고 rememberMeAuth로 인증을 한다. 인증 후엔 SecurityContextHolder에 넣어준다.

**`RememberMeAuthenticationFilter.java`**
```java
if (SecurityContextHolder.getContext().getAuthentication() == null) {
    Authentication rememberMeAuth = rememberMeServices.autoLogin(request,
        response);

    if (rememberMeAuth != null) {
      // Attempt authenticaton via AuthenticationManager
      try {
        rememberMeAuth = authenticationManager.authenticate(rememberMeAuth);

        // Store to SecurityContextHolder
        SecurityContextHolder.getContext().setAuthentication(rememberMeAuth);

    }
  }
}
```


### 커스텀필터 추가하기

서블릿 필터를 만들어서 활용해도 되고 GenericFilterBean 을 상속받아서 클래스를 만들어도 됨
그러면 doFilter()만 오버라이딩 하면 됨

```java
public class LoggingFilter extends GenericFilterBean {
  private Logger logger = LoggerFactory.getLogger(this.getClass());
  @Override
  public void doFilter(ServletRequest request, ServletResponse response, FilterChain chain) throws IOException, ServletException {
    StopWatch stopWatch = new StopWatch();
    stopWatch.start(((HttpServletRequest)request).getRequestURI());
    chain.doFilter(request, response);
    stopWatch.stop();
    logger.info(stopWatch.prettyPrint());
  }
}
```

addFilterAfter() 메소드로 어디 뒤에놓을지, addFilterBefroe() 메소드로 어디 앞에놓을지 설정 가능

```java
http.addFilterAfter(new LoggingFilter(), UsernamePasswordAuthenticationFilter.class);
```




### 메소드 시큐리티
웹이아니라 평범한 애플리케이션일 때 활용할 수 있도록 하는것
@Secured와 @RollAllowed @PreAuthorize와 @PostAuthorize 를 서비스단의 메소드에 붙이고 서비스 호출 전 Authencation을 만들어 SecurityContextHolder에 넣어서 서비스 메소드 호출가능여부를 판단


### @AuthenticationPrincipal

@AuthenticationPrincipal을 활용하면 UserDetailsService 구현체에서 리턴하는 객체를 매개변수로 받을 수 있다.

**`UserAccount.java`**
```java
public class UserAccount extends User {
  private Account account;
  public UserAccount(Account account) {
    super(account.getUsername(), account.getPassword(), List.of(new SimpleGrantedAuthority("ROLE_" + account.getRole())));
    this.account = account;
  }

  public Account getAccount() {
    return account;
  }
}
```

**`UserDetails.java`**
```java
public UserDetails loadUserByUsername(String username) throws UsernameNotFoundException {
  Account account = accountRepository.findByUsername(username);
  if (account == null) {
    throw new UsernameNotFoundException(username);
  }

  return new UserAccount(account);
}
```

UserDetailsService 구현체에서 리턴하는 객체를 매개변수로 받을 수 있다.
그 안에 들어있는 Account객체를 getter를 통해 참조할 수 있다.

```java
@GetMapping("/")
public String xxx(Model model, @AuthenticationPrincipal UserAccount userAccount) {

}

```


Account를 바로 받을 수 있도록 바꿔보자

아래처럼 어노테이션을 생성하고
```java
@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.PARAMETER)
@AuthenticationPrincipal(expression = "#this == 'anonymousUser' ? null : account")
public @interface CurrentUser {
}
```

UserAccount안에 있는 account를 꺼낸다는 의미
아래처럼 만들어주면 된다.

```java
@GetMapping("/")
public String xxx(Model model, @CurrentUser Account account) {

}

```

{% asset_img  when_login.jpg whenLoginFLow %}
