# JWT Token & Security


![](https://i.imgur.com/fsk0NnK.png)
출처 : https://inpa.tistory.com/entry/WEB-📚-Access-Token-Refresh-Token-원리-feat-JWT

# JWT Token❓(Stateless)

JWT(JSON Web Token)이란 <mark>인증에 필요한 정보들을 암호화시킨 토큰</mark>이다.

클라이언트가 서버에 접속 시 서버에서 인증의 의미로 클라이언트에게 토큰을 부여한다.
토큰을 발급 받은 클라이언트는 서버에 요청을 보낼 때 요청 헤더에 토큰을 심어 보낸다.
서버는 클라이언트로부터 받은 토큰을 서버에서 제공한  토큰과 일치하는지 확인하여 인증 과정을 처리한다.

💡 여기서 ! **세션 기반 인증**과 차이점은?

세션 기반 인증이란 <mark>클라이언트의 인증 정보를 서버 측에서 관리하는 것</mark>이다.
이는 사용자가 증가함에 따라 **서버 부하**로 성능의 문제를 일으킬 수 있다.

### Access Token & Refresh Token ❓

Access Token 이란 <mark>서비스에 액세스하는 토큰</mark>이다.

Access Token의 단점은 제 3자에게 탈취당할 경우 보안에 취약하다는 점이다.
방안으로 토큰의 유효기간을 짧게하여 방지할 수 있지만 사용자는 로그인을 자주 해야하기 때문에 불편하다.

이러한 단점을 보안하기 위해 **Refresh Token**이 필요하다.
Refresh Token은 로그인을 완료했을 때 Access Token과 같이 발급되어 긴 유효기간을 가진다.
Access Token이 만료되었을 때 새로 발급해주는 열쇠이다.

Refresh Token의 유효기간이 2주 Access Token의 유효기간이 2시간이라면
Access Token이 만료되었을 때 Refresh Token 유효기간 동안 재발급이 가능하다.

이때! Refresh Token도 만료될 경우는 다시 로그인을 해야한다.

---

### 환경 설정

- build.gradle

![](https://i.imgur.com/GcOmi4K.png)

- application.yml

![](https://i.imgur.com/48JkAnd.png)

````
📎
jwt 설정하면서 추가될 예정!
일단은 기본으로 h2 , jpa 설정을 해줌

* H2 디비 세팅 필수 *
````


## User

````
로그인 때 필요한 계정을 DB에 저장해주기 위해 User 관련 파일을 생성
````

### UserEntity

````java
import jakarta.persistence.*;  
import lombok.AllArgsConstructor;  
import lombok.Builder;  
import lombok.NoArgsConstructor;  
import org.hibernate.annotations.Comment;  
  
@Entity  
@Builder(toBuilder = true)  
@NoArgsConstructor  
@AllArgsConstructor  
@Table(name = "tb_user")  
public class UserEntity {  
  
@Id  
@Comment("사용자 아이디")  
@Column(name="user_id", length = 50)  
private String userId;  
  
@Comment("사용자 비밀번호")  
@Column(name="user_password", nullable = false, length = 100)  
private String userPassword;  
  
@Comment("사용자 이름")  
@Column(name = "user_name", nullable = false, length = 100)  
private String userName;  
}
````

````
📎

사용자 Entity

@Builder(toBuilder = true)  
=> build()로 객체에 값을 넣어주기 위함

@NoArgsConstructor  
=> 기본 생성자

@AllArgsConstructor  
=> 모든 생성자
````

### UserDto

````java
import lombok.Getter;  
import lombok.Setter;  
  
@Getter  
@Setter  
public class UserDto {  
private String userId;  
private String userPassword;  
}
````

````
📎

파라미터를 넘겨주기 위해 dto도 생성
````


### UserRepository

````java
import org.springframework.data.jpa.repository.JpaRepository;  
  
public interface UserRepository extends JpaRepository<UserEntity, String> {  
}
````

````
📎

crud를 위한 repository 생성
````

## Security

### GroupType

````java
import lombok.Getter;  
  
@Getter  
public enum GroupType {  
ADMIN("관리자");  
  
private final String description;  
  
GroupType(String description) {this.description = description;}  
}
````

````
📎

securityConfig에서 권한을 설정하기 위해 (ROLE_?)
그 값을 정의할 enum class 생성
````

### TokenType

````java
public enum TokenType {  
SESSION_TOKEN,  
ACCESS_TOKEN,  
REFRESH_TOKEN  
}
````

````
📎

토큰을 구분할 enum class 생성
````

### ExceptionType

````java
import lombok.Getter;  
import lombok.RequiredArgsConstructor;  
import org.springframework.http.HttpStatus;  
  
@Getter  
@RequiredArgsConstructor  
public enum ExceptionType {  
  
JWT_USERNAME_NULL(HttpStatus.UNAUTHORIZED, "인증 아이디가 없습니다."),  
JWT_PASSWORD_NULL(HttpStatus.UNAUTHORIZED, "인증 비밀번호가 없습니다."),  
JWT_BODY_NULL(HttpStatus.UNAUTHORIZED, "인증 데이터가 없습니다."),  
JWT_ERROR(HttpStatus.FORBIDDEN, "잘못된 토큰입니다."),  
JWT_COOKIE_NULL(HttpStatus.UNAUTHORIZED, "로그아웃이 된 유저입니다."),  
  
FORBIDDEN_ACCESS(HttpStatus.FORBIDDEN, "접근 권한이 없습니다."),  
METHOD_NOT_ALLOW(HttpStatus.METHOD_NOT_ALLOWED, "지원되지 않는 방식입니다."),  
  
BAD_LOGIN_INFO(HttpStatus.UNAUTHORIZED, "로그인 정보가 일치하지 않습니다."),  
BAD_CREDENTIAL(HttpStatus.UNAUTHORIZED, "비밀번호가 일치하지 않습니다."),  
  
UNAUTHORIZED_REQUEST(HttpStatus.UNAUTHORIZED, "인증되지 않은 사용자입니다."),  
UNSUPPORTED_MEDIA_TYPE(HttpStatus.UNSUPPORTED_MEDIA_TYPE, "지원되지 않는 타입입니다."),  
  
NOT_ACCEPT_MAP(HttpStatus.UNAUTHORIZED, "접속 권한이 있는 도면이 없습니다."),  
NOT_ACCEPT_IP(HttpStatus.UNAUTHORIZED, "승인된 IP와 접속정보가 일치하지 않습니다."),  
NOT_FOUND(HttpStatus.NOT_FOUND, "요청한 페이지를 찾을 수 없습니다."),  
NOT_FOUND_USER(HttpStatus.UNAUTHORIZED, "사용자를 찾을 수 없습니다."),  
  
INTERNAL_SERVER_ERROR(HttpStatus.INTERNAL_SERVER_ERROR, "서버 오류입니다.");  
  
  
private HttpStatus status;  
private String message;  
  
ExceptionType(HttpStatus httpStatus, String message) {  
this.status = httpStatus;  
this.message = message;  
}  
}
````

````
📎

Exception을 핸들링 하기 위해 Exception 타입을 지정해줌
````

### JwtErrorException

````java
import com.example.jwtbase.constant.ExceptionType;  
import io.jsonwebtoken.JwtException;  
import lombok.Getter;  
  
@Getter  
public class JwtErrorException extends JwtException {  
  
private final ExceptionType exceptionType;  
  
public JwtErrorException(ExceptionType exceptionType) {  
super(exceptionType.getMessage());  
this.exceptionType = exceptionType;  
}  
}
````

````
📎

JwtException을 상속 받은 JwtErrorException 생성
위에서 생성한 타입들을 사용하기 위함
````
### SecurityUserDetails

````java
import com.example.jwtbase.user.UserEntity;  
import org.springframework.security.core.GrantedAuthority;  
import org.springframework.security.core.authority.SimpleGrantedAuthority;  
import org.springframework.security.core.userdetails.UserDetails;  
  
import java.util.Collection;  
import java.util.Collections;  
  
public class SecurityUserDetails implements UserDetails {  
  
private final UserEntity user;  
  
public SecurityUserDetails(UserEntity user) {  
this.user = user;  
}  
  
@Override  
public Collection<? extends GrantedAuthority> getAuthorities() {  
return Collections.singleton(new SimpleGrantedAuthority("ROLE_"+user.getGroupType().name()));  
}  

public UserEntity getUser() {
	return user.toBuilder().userPassword("").build();
}
  
@Override  
public String getPassword() {  
return user.getUserPassword();  
}  
  
@Override  
public String getUsername() {  
return user.getUserId();  
}  
  
@Override  
public boolean isAccountNonExpired() {  
return UserDetails.super.isAccountNonExpired();  
}  
  
@Override  
public boolean isAccountNonLocked() {  
return UserDetails.super.isAccountNonLocked();  
}  
  
@Override  
public boolean isCredentialsNonExpired() {  
return UserDetails.super.isCredentialsNonExpired();  
}  
  
@Override  
public boolean isEnabled() {  
return UserDetails.super.isEnabled();  
}  
  
}
````

````
📎

Spring Security의 사용자 정보를 담는 UserDetails를 커스텀
권한 정보는 UserEntity에 groupType을 따르게 만들고 password와 username도 각각 UserEntity의 비밀번호/아이디로 지정

getUser()는 토큰에 사용자 정보를 담을 예정인데 password를 넣지 않기위해 따로 만듬
````

### SecurityUserDetailsService

````java
import com.example.jwtbase.user.UserEntity;  
import com.example.jwtbase.user.UserRepository;  
import lombok.RequiredArgsConstructor;  
import org.springframework.security.core.userdetails.UserDetails;  
import org.springframework.security.core.userdetails.UserDetailsService;  
import org.springframework.security.core.userdetails.UsernameNotFoundException;  
import org.springframework.stereotype.Service;  
  
  
@Service  
@RequiredArgsConstructor  
public class SecurityUserDetailsService implements UserDetailsService {  
  
private final UserRepository userRepository;  
  
@Override  
public UserDetails loadUserByUsername(String username) throws UsernameNotFoundException {  
UserEntity user = userRepository.findById(username).orElseThrow();  
return new SecurityUserDetails(user);  
}  
}
````

````
📎

Spring Security에서 커스텀된 SecurityUserDetailsService를 사용하기 위해
사용자 정보를 가져오는 SecurityUserDetailsService를 작성

사용자 인증 및 권한 부여를 위한 사용자 정보를 로드하는 서비스
````

### JwtTokenProvider

````java
import com.example.jwtbase.constant.TokenType;  
import com.example.jwtbase.user.UserEntity;  
import com.fasterxml.jackson.databind.ObjectMapper;  
import io.jsonwebtoken.*;  
import io.jsonwebtoken.security.Keys;  
import jakarta.servlet.http.Cookie;  
import jakarta.servlet.http.HttpServletRequest;  
import jakarta.servlet.http.HttpServletResponse;  
import org.springframework.beans.factory.InitializingBean;  
import org.springframework.beans.factory.annotation.Value;  
import org.springframework.http.ResponseCookie;  
import org.springframework.security.authentication.UsernamePasswordAuthenticationToken;  
import org.springframework.security.core.Authentication;  
import org.springframework.stereotype.Component;  
  
import java.io.IOException;  
import java.security.Key;  
import java.util.*;  
  
@Component  
public class JwtTokenProvider implements InitializingBean {  
  
@Value("${jwt.secret}")  
private String SECRET_KEY;  
  
@Value("${jwt.access.expiration}")  
private Long ACCESS_VALIDITY;  
  
@Value("${jwt.refresh.expiration}")  
private Long REFRESH_VALIDITY;  
  
@Value("${jwt.access.cookie-name}")  
private String ACCESS_COOKIE_NAME;  
  
@Value("${jwt.refresh.cookie-name}")  
private String REFRESH_COOKIE_NAME;  
  
private Key key;  
  
@Override  
public void afterPropertiesSet() throws Exception {  
String encodeKey = Base64.getEncoder().encodeToString(SECRET_KEY.getBytes());  
key = Keys.hmacShaKeyFor(encodeKey.getBytes());  
}  
  
// 토큰 생성  
public Map<String, String> createToken(SecurityUserDetails securityUserDetails) {  
Map<String, String> tokenMap = new HashMap<>();  
  
Claims claims = Jwts.claims().setSubject(securityUserDetails.getUsername());  
claims.put("user", securityUserDetails.getUser());   
  
String accessToken = Jwts.builder()  
.setClaims(claims)  
.setIssuedAt(new Date(System.currentTimeMillis()))  
.setExpiration(new Date(System.currentTimeMillis() + (ACCESS_VALIDITY * 1000)))  
.signWith(key, SignatureAlgorithm.HS512)  
.compact();  
  
String refreshToken = Jwts.builder()  
.setClaims(claims)  
.setIssuedAt(new Date(System.currentTimeMillis()))  
.setExpiration(new Date(System.currentTimeMillis() + (REFRESH_VALIDITY * 1000)))  
.signWith(key, SignatureAlgorithm.HS512)  
.compact();  

tokenMap.put("ACCESS_TOKEN", accessToken);  
tokenMap.put("REFRESH_TOKEN", refreshToken);  
  
return tokenMap;  
}  
  
// 인증  
public Authentication getAuthentication(SecurityUserDetails userDetails) {  
return new UsernamePasswordAuthenticationToken(userDetails.getUsername(), "", userDetails.getAuthorities());  
}  
  
// 해당 토큰의 사용자 정보 조회  
public SecurityUserDetails getUserDetailFromToken(String token) {  
Claims claims = Jwts.parserBuilder().setSigningKey(key).build().parseClaimsJws(token).getBody();  
  
ObjectMapper objectMapper = new ObjectMapper();  
return new SecurityUserDetails(objectMapper.convertValue(claims.get("user"), UserEntity.class));  
}  
  
// 해당 토큰의 사용자 아이디 조회  
public String getUsernameFromToken(String token) {  
return Jwts.parserBuilder().setSigningKey(key).build().parseClaimsJws(token).getBody().getSubject();  
}  
  
// 토큰 유효성 체크  
public boolean validationToken(String token) {  
try {  
return !Jwts.parserBuilder().setSigningKey(key).build().parseClaimsJws(token).getBody().getExpiration().before(new Date());  
} catch (ExpiredJwtException e) {  
return false;  
} catch (JwtException e) {  
throw new JwtException("토큰 에러입니다.");  
}  
}  
  
// 쿠키에 있는 토큰 정보 조회  
public String getToken(HttpServletRequest request, String cookieName) {  
if (request.getCookies() != null && request.getCookies().length > 0) {  
return Arrays.stream(request.getCookies())  
.filter(cookie -> cookie.getName().equals(cookieName))  
.findFirst()  
.map(Cookie::getValue)  
.orElse(null);  
}  
return null;  
}  
  
// 쿠키 생성  
public void createCookie(HttpServletResponse response, SecurityUserDetails userDetails) {  
this.createToken(userDetails).forEach((key, value) -> {  
TokenType tokenType = TokenType.valueOf(key);  
String name = tokenType.equals(TokenType.ACCESS_TOKEN) ? ACCESS_COOKIE_NAME : REFRESH_COOKIE_NAME;  
  
Cookie cookie = new Cookie(name, value);  
cookie.setHttpOnly(true);  
cookie.setPath("/");  
  
response.addCookie(cookie);  
  
});  
}  
  
// 쿠키 삭제  
public void deleteCookie(HttpServletRequest request, HttpServletResponse response) throws IOException {  
if (request.getCookies() != null && request.getCookies().length > 0) {  
Arrays.stream(request.getCookies())  
.toList().forEach(cookie -> {  
response.addHeader("Set-Cookie", ResponseCookie.from(cookie.getName(), cookie.getValue())  
.path("/")  
.maxAge(0)  
.build().toString());  
});  
}  
}  
  
}
````

````
📎

토큰, 쿠키 생성, 삭제, 조회, 유효성 체크 등을 하는 클래스 생성
````

### JwtAuthenticationProvider

````java
import io.jsonwebtoken.JwtException;  
import lombok.RequiredArgsConstructor;  
import org.springframework.security.authentication.AuthenticationProvider;  
import org.springframework.security.authentication.UsernamePasswordAuthenticationToken;  
import org.springframework.security.core.Authentication;  
import org.springframework.security.core.AuthenticationException;  
import org.springframework.security.crypto.password.PasswordEncoder;  
import org.springframework.stereotype.Component;  
  
@Component  
@RequiredArgsConstructor  
public class JwtAuthenticationProvider implements AuthenticationProvider {  
  
private final SecurityUserDetailsService securityUserDetailsService;  
private final PasswordEncoder passwordEncoder;  
  
@Override  
public Authentication authenticate(Authentication authentication) throws AuthenticationException {  
String username = (String) authentication.getPrincipal();  
String password = (String) authentication.getCredentials();  
  
SecurityUserDetails userDetails = (SecurityUserDetails) securityUserDetailsService.loadUserByUsername(username);  
  
if (!passwordEncoder.matches(password, userDetails.getPassword())) {  
throw new JwtException("계정 정보 오류");  
}  
  
return new UsernamePasswordAuthenticationToken(userDetails, "", userDetails.getAuthorities());  
}  
  
@Override  
public boolean supports(Class<?> authentication) {  
return true;  
}  
}
````

````
📎
AuthenticationProvider는 실제 인증 로직이 담긴 객체로 JwtAuthenticationProvider로 커스터마이징을 함
authentication을 오버라이드하여 아이디와 비밀번호가 실제 DB에 있는 데이터와 일치하는지 확인 후 인증된 authentication 객체를 반환한다.
````

### JwtAuthenticationFilter

````java
import com.example.jwtbase.constant.ExceptionType;  
import com.fasterxml.jackson.databind.ObjectMapper;  
import io.micrometer.common.util.StringUtils;  
import jakarta.servlet.FilterChain;  
import jakarta.servlet.ServletException;  
import jakarta.servlet.ServletRequest;  
import jakarta.servlet.ServletResponse;  
import jakarta.servlet.http.HttpServletRequest;  
import jakarta.servlet.http.HttpServletResponse;  
import org.springframework.beans.factory.annotation.Value;  
import org.springframework.http.HttpMethod;  
import org.springframework.http.MediaType;  
import org.springframework.security.authentication.AuthenticationManager;  
import org.springframework.security.authentication.UsernamePasswordAuthenticationToken;  
import org.springframework.security.core.Authentication;  
import org.springframework.security.core.AuthenticationException;  
import org.springframework.security.core.context.SecurityContextHolder;  
import org.springframework.security.web.authentication.UsernamePasswordAuthenticationFilter;  
import org.springframework.security.web.util.matcher.AntPathRequestMatcher;  
import org.springframework.util.PatternMatchUtils;  
import org.springframework.web.servlet.HandlerExceptionResolver;  
  
import java.io.IOException;  
import java.util.Map;  
  
public class JwtAuthenticationFilter extends UsernamePasswordAuthenticationFilter {  
  
private static final String LOGIN_URL = "/login";  
private static final String LOGOUT_URL = "/login/logout.do";  
  
private final JwtTokenProvider jwtTokenProvider;  
private final SecurityUserDetailsService securityUserDetailsService;  
private final HandlerExceptionResolver handlerExceptionResolver;  
  
@Value("${jwt.exclude-path}")  
private String[] EXCLUDE_PATH;  
  
@Value("${cookie.name.access}")  
private String ACCESS_COOKIE_NAME;  
  
@Value("${cookie.name.refresh}")  
private String REFRESH_COOKIE_NAME;  
  
public JwtAuthenticationFilter(JwtTokenProvider jwtTokenProvider, SecurityUserDetailsService securityUserDetailsService,  
AuthenticationManager authenticationManager, JwtLoginSuccessHandler jwtLoginSuccessHandler, HandlerExceptionResolver handlerExceptionResolver) {  
  
this.setRequiresAuthenticationRequestMatcher(new AntPathRequestMatcher(LOGIN_URL, HttpMethod.POST.name()));  
this.setAuthenticationManager(authenticationManager);  
this.setAuthenticationSuccessHandler(jwtLoginSuccessHandler);  
this.jwtTokenProvider = jwtTokenProvider;  
this.securityUserDetailsService = securityUserDetailsService;  
this.handlerExceptionResolver = handlerExceptionResolver;  
}  
  
@Override  
public void doFilter(ServletRequest servletRequest, ServletResponse servletResponse, FilterChain chain) throws IOException, ServletException {  
HttpServletRequest request = (HttpServletRequest) servletRequest;  
HttpServletResponse response = (HttpServletResponse) servletResponse;  
String accept = request.getHeader("Accept");  
  
if (PatternMatchUtils.simpleMatch(EXCLUDE_PATH, request.getRequestURI()) || requiresAuthentication(request, response)) {  
super.doFilter(request, response, chain);  
return;  
}  
  
try {  
String refreshToken = jwtTokenProvider.getToken(request, REFRESH_COOKIE_NAME);  
  
// 리프레시 쿠키가 없거나 로그아웃 요청일 시 쿠키 삭제  
if ((HttpMethod.GET.matches(request.getMethod()) && LOGOUT_URL.equals(request.getRequestURI())) || StringUtils.isBlank(refreshToken)) {  
jwtTokenProvider.deleteCookie(request, response);  
if (StringUtils.isNotBlank(accept) && accept.contains("text/html")) {  
response.sendRedirect("/login/index.do");  
} else {  
throw new JwtErrorException(ExceptionType.JWT_COOKIE_NULL);  
}  
return;  
}  
  
  
String accessToken = jwtTokenProvider.getToken(request, ACCESS_COOKIE_NAME);  
if (StringUtils.isNotBlank(accessToken)) {  
SecurityUserDetails userDetails;  
if (jwtTokenProvider.validationToken(accessToken)) {  
userDetails = jwtTokenProvider.getUserDetailFromToken(accessToken);  
  
if (StringUtils.isNotBlank(accept) && accept.contains("text/html")) {  
String userId = jwtTokenProvider.getUsernameFromToken(accessToken);  
userDetails = (SecurityUserDetails) securityUserDetailsService.loadUserByUsername(userId);  
}  
} else {  
String userId = jwtTokenProvider.getUsernameFromToken(refreshToken);  
userDetails = (SecurityUserDetails) securityUserDetailsService.loadUserByUsername(userId);  
jwtTokenProvider.createCookie(response, userDetails);  
}  
  
Authentication authentication = jwtTokenProvider.getAuthentication(userDetails);  
SecurityContextHolder.getContext().setAuthentication(authentication);  
}  
  
super.doFilter(request, response, chain);  
} catch (Exception e) {  
handlerExceptionResolver.resolveException(request, response, null, e);  
}  
}  
  
@Override  
public Authentication attemptAuthentication(HttpServletRequest request, HttpServletResponse response) throws AuthenticationException {  
try {  
if (!HttpMethod.POST.matches(request.getMethod())) {  
throw new JwtErrorException(ExceptionType.METHOD_NOT_ALLOW);  
} else if (!MediaType.APPLICATION_JSON.toString().equals(request.getContentType())) {  
throw new JwtErrorException(ExceptionType.UNSUPPORTED_MEDIA_TYPE);  
}  
Map<String, String> loginInfo = getLoginInfo(request);  
  
String username = loginInfo.get("username");  
String password = loginInfo.get("password");  
  
if (StringUtils.isBlank(username)) {  
throw new JwtErrorException(ExceptionType.JWT_USERNAME_NULL);  
}  
  
if (StringUtils.isBlank(password)) {  
throw new JwtErrorException(ExceptionType.JWT_PASSWORD_NULL);  
}  
  
UsernamePasswordAuthenticationToken authenticationToken =  
new UsernamePasswordAuthenticationToken(username, password);  
  
return this.getAuthenticationManager().authenticate(authenticationToken);  
} catch (Exception e) {  
handlerExceptionResolver.resolveException(request, response, null, e);  
}  
return null;  
}  
  
@Override  
protected void successfulAuthentication(HttpServletRequest request, HttpServletResponse response, FilterChain chain, Authentication authResult) throws IOException, ServletException {  
super.successfulAuthentication(request, response, chain, authResult);  
}  
  
@Override  
protected void unsuccessfulAuthentication(HttpServletRequest request, HttpServletResponse response, AuthenticationException failed) throws IOException, ServletException {  
handlerExceptionResolver.resolveException(request, response, null, failed);  
}  
  
private Map<String, String> getLoginInfo(HttpServletRequest request) {  
try {  
return new ObjectMapper().readValue(request.getInputStream(), Map.class);  
} catch (IOException e) {  
throw new JwtErrorException(ExceptionType.JWT_BODY_NULL);  
}  
}  
}
````

````
📎

사용자 검증 필터,

attemptAuthentication()는 사용자가 로그인할 시 JwtAuthenticationProvider에서 아이디와 비밀번호를 검증하여 인증 객체를 만들어주는 함수

doFilter()는 예외처리한 url 제외 모든 url 요청시 거치는 필터로직 함수로 이곳에서 쿠키를 통해 Access Token과 Refresh Token을 확인

만약 Refresh Cookie가 삭제되거나 로그아웃 url 일시에는 모든 쿠키를 삭제하여 로그아웃 후 로그인 페이지 이동

Access Token 유효하다면 토큰 안에 있던 사용자 정보를 통해 권한 생성
유효하지 않다면 리프레시 토큰에서 정보를 꺼내온 후 쿠키 다시 생성

StringUtils.isNotBlank(accept) && accept.contains("text/html")
=> 이부분은 만약 페이지 이동 시 유저 정보가 바뀌었을 가능성이 있어서 사용
페이지에 권한에서 가져오는 사용자정보 데이터가 있는데 이건 개인적으로 사용하기 때문에 넣은 로직

handlerExceptionResolver.resolveException
=> 이부분은 따로 ExceptionHandler를 만들기 위함
아까 만든 ExceptionType에 적힌 메시지들을 사용하기 위해서
````
