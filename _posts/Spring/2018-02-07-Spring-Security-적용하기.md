---
layout: post
title:  "Spring Security를 기존 프로젝트에 적용하기"
tags: [Spring, Spring Security]
categories: [Spring]
description: "기존에 있는 프로젝트에 Spring Security를 적용 해보자"
image:
  feature: header/java-spring-logo.png
---

기존 프로젝트에 Spring Security를 적용 해보자.  

Spring Security 적용하기
-----------------------

Maven

```xml
<dependency>
  <groupId>org.springframework.boot</groupId>
  <artifactId>spring-boot-starter-security</artifactId>
</dependency>
```

Gradle

```
compile("org.springframework.boot:spring-boot-starter-security")
```

```java
@EnableWebSecurity
public class WebSecurityConfig extends WebSecurityConfigurerAdapter {

}
```

`WebSecurityConfig`를 만들면 모든 접근에 대해 인가가 필요하게 됩니다.

> 인증(Authentication) : 해당 사용자가 본인이 맞는지 확인하는 절차.  
> 인가(Authorization) : 인증된 사용자가 요청된 자원에 접근 가능한지를 결정하는 절차.  

```java
@EnableWebSecurity
public class WebSecurityConfig extends WebSecurityConfigurerAdapter {

  @Override
  	protected void configure(HttpSecurity http) throws Exception {
  		http.csrf().disable();
  		http.headers().frameOptions().disable();
  		http.httpBasic();

  		http
  			.authorizeRequests()
  			.antMatchers("/", "/create", "/login**", "/logout**")
  			.permitAll()
  			.antMatchers("/boards/**", "/myboards", "/api/boards**", "/api/decks/**", "/api/boards/**", "/api/users**")
  			.hasRole("USER");

  }
}
```

자신이 설정 할 정책들을 적용 합니다.

위와 같이 설정 해두면 `.hasRole("USER")`에 해당 하는 곳들은 권한이 없는 사용자는 접근이 불가능 하게 됩니다.  

(조금 있다 `.hasRole("USER")`에 해당 하는 롤을 만들 겁니다)

 만약 인가때문에 문제가 된다면 일단은 `.permitAll()`으로 모두를 설정 해두고 작업을 하면 됩니다.

---

기존 유저 모델

```java
@Getter
@Setter
@Entity
@EqualsAndHashCode(of = "uid")
@ToString
public class Member {

	@Id
	@GeneratedValue
	@Column(name = "MEMBER_ID")
	private long id;

	@Size(min = 3, max = 20)
	@Column(nullable = false, length = 20)
	private String name;

	@Column(nullable = false)
	@JsonIgnore
	private String password;

	@Email
	@Size(min = 6, max = 50)
	@Column(unique = true, nullable = false, length = 50)
	private String email;
}
```

일단 Role을 만들어야 합니다.

```java
@Getter
@Setter
@Entity
@Table(name = "member_roles")
@ToString
public class MemberRole {
	public static final String USER = "USER";

	@Id
	@GeneratedValue(strategy = GenerationType.IDENTITY)
	private long id;

	private String roleName;

	public MemberRole() {
	}

	public MemberRole(String roleName) {
		this.roleName = roleName;
	}
```

----

그리고 위의 `Member`에 적용 되어야 할 롤들을 추가합니다.

```java
@Getter
@Setter
@Entity
@EqualsAndHashCode(of = "uid")
@ToString
public class Member {

	@OneToMany(cascade=CascadeType.ALL, fetch=FetchType.EAGER)
	@JoinColumn(name="member")
	private List<MemberRole> roles = new ArrayList<>();

}
```

`cascade=CascadeType.ALL`은 엔티티들의 영속관계를 한 번에 처리하지 못하기 때문에 설정 하는 것이고, `fetch=FetchType.EAGER`은 `member`와 `member_roles` 테이블을 둘 다 조회해야 하기 때문에 즉시 로딩을 이용해서 조인을 하는 방식으로 처리 합니다.

---

자 이제 재료는 다 준비 되었으니 회원 가입 부분을 수정 해 봅시다.

```java
@Controller
@RequestMapping("/member")
public class MemberController {

	@Resource(name = " memberRepository")
	private MemberRepository memberRepository;

	@PostMapping("")
	public String create(Member member) {
		MemberRole role = new MemberRole();
		BCryptPasswordEncoder passwordEncoder = new BCryptPasswordEncoder();
		member.setPassword(passwordEncoder.encode(member.getPassword()));
		role.setRoleName("USER");
		member.setRoles(Arrays.asList(role));
		memberRepository.save(member);
		return "redirect:/";
	}

```

Spring Security에서 지원해 주는 BCryptPasswordEncoder를 이용하면 손쉽게 비밀번호를 암호화 할 수 있습니다.  

MemberRole을 정의해서 Member에 넣어주고 save를 합니다.

```html
<div class="signup-box z-depth-2">
     <h6 id="signUpFail" ></h6>
         <h4>Create a Account</h4>

         <form class="signup-form" action="/members" method="POST">
           <div class="row">
               <div class="input-field col s12">
                 <input id="name" name="name" type="text" class="validate">
                 <label for="name">Username</label>
               </div>
           </div>

           <div class="row">
               <div class="input-field col s12">
                   <input id="email" name="email" type="email" class="validate">
                   <label for="email">Email</label>
                 </div>
               </div>

          <div class="row">
               <div class="input-field col s12">
                 <input id="password" name="password" type="password" class="validate">
                 <label for="password">Password</label>
               </div>
          </div>
             <input class="signup-btn waves-effect waves-light btn" type="submit" value="가입하기" />
         </form>

     </div>
```

위와 같이 하면 이제 Role을 가지고 있는 멤버가 생성 됩니다.  

---

로그인 구현 하기
==============

회원 가입은 잘 되는데 로그인이 안돼서 아직 제대로 된 확인이 안됩니다. 그럼 본격 적으로 로그인 기능을 구현 해 봅시다.

`WebSecurityConfig`에서 `AuthenticationManagerBuilder`를 주입 해서 인증 처리를 해야 합니다.

```java
@Resource(name="securityMemberService")
private SecurityMemberService securityMemberService;


public void configureGlobal(AuthenticationManagerBuilder auth) throws Exception{
  auth.userDetailsService(securityMemberService).passwordEncoder(passwordEncoder());
}
```

위와 같이 설정 하면 `UserDetailsService`를 구현한 `securityMemberService`를 `HttpSecurity` 객체가 사용하게 되면서 우리가 만든 인증 로직을 바탕으로 동작 하게 됩니다.

그럼 `securityMemberService`를 제작해 봅시다.

securityMemberService
----------------------

#### (주의 : 저같은 경우는 `email`로 로그인을 합니다.)

```java
@Service("securityMemberService")
public class SecurityMemberService implements UserDetailsService{

	@Resource(name="memberRepository")
	private MemberRepository memberRepository;

	@Override
	public UserDetails loadUserByUsername(String username) throws UsernameNotFoundException {
		Member member = memberRepository.findByEmail(username).orElseThrow(() -> new UsernameNotFoundException("아이디 혹은 비밀번호를 잘 못 입력 하셨습니다."));
		return new SecurityMember(member);
	}
}
```

저같은 경우는 email을 통해 로그인을 하기때문에 받아온 `username`에 email이 담겨 있습니다. 그리고 그걸 이용하여 db에서 email을 통해 member를 가져와서 로그인을 절차를 거치게 됩니다.  

위와 같은 방법으로 로그인시 Id 혹은 email등 자신이 원하는 형태를 설정 할 수 있습니다.

`UserDetailsService` 인터페이스를 보면  

```java
UserDetails loadUserByUsername(String username) throws UsernameNotFoundException;
```  

`UserDetails`를 반환하는 하나의 메소드만을 가지고 있습니다. 그렇기때문에 기존에 구현한 `Member`와는 타입이 맞지 않습니다. 이를 해결 하기 위한 방법 중 Spring Security의 `User`를 상속하는 새로운 멤버 클래스를 추가 합니다. (이때문에 Member가 아닌 User라는 이름으로 클래스를 만들면 문제가 생길수 있습니다)

SecurityMember
--------------

```java
@Getter
@Setter
public class SecurityMember extends User{
	private static final long serialVersionUID = 1L;
	private static final String ROLE_PREFIX = "ROLE_";

	public SecurityMember(Member member) {
		super(member.getEmail(), member.getPassword(), makeGrantedAuthority(member.getRoles()));
	}

	private static List<GrantedAuthority> makeGrantedAuthority(List<MemberRole> roles){
		List<GrantedAuthority> list = new ArrayList<>();
		roles.forEach(
			role -> list.add(
					new SimpleGrantedAuthority(ROLE_PREFIX + role.getRoleName())));
		return list;
	}
}
```

위에도 언급했듯이 저같은 경우는 email로 로그인을 하기때문에 `User`클래스에 생성자의 `username` 위치에 `member.getEmail()`를 사용합니다.  

다시 WebSecurityConfig
------------------------

```java
@Configuration  //이번에는 추가해 주자
@EnableWebSecurity
public class WebSecurityConfig extends WebSecurityConfigurerAdapter {

	@Bean
	public BCryptPasswordEncoder passwordEncoder() {
		return new BCryptPasswordEncoder();
	}

	@Resource(name="securityMemberService")
	private SecurityMemberService securityMemberService;

  @Override
  	protected void configure(HttpSecurity http) throws Exception {
  		http.csrf().disable();
  		http.headers().frameOptions().disable();
  		http.httpBasic();

  		http
  			.authorizeRequests()
  			.antMatchers("/", "/create", "/login**", "/logout**")
  			.permitAll()
  			.antMatchers("/boards/**", "/myboards", "/api/boards**", "api/login**", "/api/decks/**", "api/boards/**", "/api/boards/**", "/api/users**")
  			.hasRole("USER");

  		http
  			.formLogin()
  			.loginPage("/login")
        .defaultSuccessUrl("/")
        .failureUrl("/login");

  		http
  			.logout()
  			.logoutUrl("/logout")
  			.invalidateHttpSession(true);

  }

  public void configureGlobal(AuthenticationManagerBuilder auth) throws Exception{
    auth.userDetailsService(securityMemberService).passwordEncoder(passwordEncoder());
  }
}
```  

`loginPage` :  로그인 뷰 페이지  

`loginProcessingUrl` : Post로 로그인을 처리할 Url  

`defaultSuccessUrl` : 로그인 성공 후 이동할 페이지

`failureUrl` : 실패 후 이동할 페이지

login.html
-----------

```html
<div class="login-box z-depth-2">

		<h6 id="loginFail" ></h6>
		<h4>Login To Trello</h4>

		<form class="login-form" action="/login" method="POST">
			<div class="row">
				<div class="input-field col s12">
					<input id="username" name="username" type="email" class="validate">
					<label for="username">Email</label>
				</div>
			</div>
			<div class="row">
				<div class="input-field col s12">
					<input id="password" name="password" type="password"
						class="validate"> <label for="password">Password</label>
				</div>
			</div>
			<button class="login-btn waves-effect waves-light btn" type="submit">로그인</button>
		</form>

		<p class="alternatives-separator">or</p>

		<a class="github-login waves-effect waves-light btn" href="#">
			<div class="github-icon"></div>
			<div class="github-login-text">github로 로그인</div>
		</a>

</div>
```

Spring Security의 기본 설정으로 인해 지금은 name 속성을 `username`과 `password`로 해둬야 제대로 동작 합니다.
