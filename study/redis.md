# Redis

Remote Dictionary Server 의 약자로 key-value 형태의 데이터 구조 저장소

![](https://i.imgur.com/qLOzsIo.png)
출처:[https://medium.com/frientrip/pub-sub-잘-알고-쓰자-de9dc1b9f739](https://medium.com/frientrip/pub-sub-%EC%9E%98-%EC%95%8C%EA%B3%A0-%EC%93%B0%EC%9E%90-de9dc1b9f739)

### Redis를 직접 사용해보자 !! ✨

🙋‍♀️ 테스트를 하기 위해서는 Redis를 해당 서버에 설치 및 설정해야 함!

-   msi 확장자 redis 설치 (port 정보는 application.yml에서 설정할 번호)
    [https://github.com/microsoftarchive/redis/releases](https://github.com/microsoftarchive/redis/releases)


◼ 버전 정보 및 Redis 의존성 설정

```
Spring boot 3.2.5
jdk 17
```

```groovy
implementation 'org.springframework.boot:spring-boot-starter-data-redis'
```

```yaml

server:

  port: 8080

  

spring:

  data:

    redis:

      port: 6379

      host: 127.0.0.1

```


◼ Config 설정 

```java

package com.example.redistest.config;

  

import org.springframework.beans.factory.annotation.Value;

import org.springframework.context.annotation.Bean;

import org.springframework.context.annotation.Configuration;

import org.springframework.data.redis.connection.RedisConnectionFactory;

import org.springframework.data.redis.connection.lettuce.LettuceConnectionFactory;

import org.springframework.data.redis.core.RedisTemplate;

import org.springframework.data.redis.serializer.StringRedisSerializer;

  

@Configuration

public class RedisConfig {

  

    @Value("${spring.data.redis.port}")

    private int port;

  

    @Value("${spring.data.redis.host}")

    private String host;

  

    @Bean

    public RedisConnectionFactory redisConnectionFactory() {

        return new LettuceConnectionFactory(host,port);

    }

  

    @Bean

    public RedisTemplate<String, Object> redisTemplate() {

        RedisTemplate<String, Object> redisTemplate = new RedisTemplate<>();

        redisTemplate.setConnectionFactory(redisConnectionFactory());

  

        // 직렬화

        redisTemplate.setKeySerializer(new StringRedisSerializer());

        redisTemplate.setValueSerializer(new StringRedisSerializer());

  

        // Hash 직렬화

        redisTemplate.setHashKeySerializer(new StringRedisSerializer());

        redisTemplate.setHashValueSerializer(new StringRedisSerializer());

  

        redisTemplate.setDefaultSerializer(new StringRedisSerializer());

        return redisTemplate;

    }

  

}

```

````
📎

yml에 정의했던 Redis의 host, port 정보를 가져와 connection 설정 및 데이터 직렬화
````
  

◼ 값을 넘길 DTO 생성

```java

package com.example.redistest;

  

import lombok.Data;

  

@Data

public class RedisDto {

    private String key;

    private String value;

}

```


◼ 컨트롤러 생성

```java

package com.example.redistest.controller;

  

import com.example.redistest.RedisDto;

import lombok.RequiredArgsConstructor;

import org.springframework.data.redis.core.RedisTemplate;

import org.springframework.data.redis.core.ValueOperations;

import org.springframework.web.bind.annotation.*;

  

@RestController

@RequestMapping(value = "/redis")

@RequiredArgsConstructor

public class RedisController {

    private final RedisTemplate<String, Object> redisTemplate;

  

    /* 조회 */

    @GetMapping(value = "/find")

    public String getRedisData(@RequestBody RedisDto redisDto) {

        ValueOperations<String, Object> valueOperations = redisTemplate.opsForValue();

        String value = String.valueOf(valueOperations.get(redisDto.getKey()));

  

        return value;

    }

  

    /* 등록/수정 */

    @PostMapping(value = "/upsert")

    public void updateRedisData(@RequestBody RedisDto redisDto) {

        ValueOperations<String, Object> valueOperations = redisTemplate.opsForValue();

        valueOperations.set(redisDto.getKey(), redisDto.getValue());

    }

  

    /* 삭제 */

    @PostMapping(value = "/delete")

    public void deleteRedisData(@RequestBody RedisDto redisDto) {

        redisTemplate.delete(redisDto.getKey());

    }

}

```

````
📎

레디스에 값을 등록/수정 , 조회, 삭제 로직 생성
````

💡 RestTemplate 메서드

| 메소드명          | 반환 오퍼레이션        | 자료구조       |
| ------------- | --------------- | ---------- |
| opsForValue() | ValueOperations | String     |
| opsForList()  | ListOperations  | List       |
| opsForSet()   | SetOperations   | Set        |
| opsForZSet()  | ZSetOperations  | Sorted Set |
| opsForHash()  | HashOperations  | Hash       |

### TEST


![](https://i.imgur.com/s6ONOW9.png)

````
📎

hello 키에 값 저장
````
  
![](https://i.imgur.com/sXLQ1IX.png)

````
📎

hello 키로 조회 후 값 확인
````

![](https://i.imgur.com/Dgt9F9Y.png)

````
📎

hello 키 삭제
````

![](https://i.imgur.com/A6kZH0S.png)

````
📎

조회하여 삭제된 것을 확인
````
