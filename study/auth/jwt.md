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

### SecurityUserDetails

