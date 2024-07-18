# Kafka

실시간으로  기록 스트림을 게시, 구독, 저장 및 처리할 수  있는 분산형 데이터  스트리밍 플랫폼

![](https://i.imgur.com/bQqkRlC.png)
출처 : [https://twofootdog.tistory.com/86](https://twofootdog.tistory.com/86)

### Kafka 용어

◼ Producer

- 메시지를 생산 후 브로커의 토픽 이름으로 전달

◼ Broker

- 아파치 카프카 애플리케이션이 설치되어 있는 서버 또는 노드

◼ Consumer

- 브로커의 토픽 이름으로 저장된 메시지를 전달 받음

◼ Zookeeper

- 분산 애플리케이션 관리를 위한 코디네이션 시스템

---
### Kafka를 직접 사용해보자 ✨

  
🙋‍♀️ 테스트를 하기 위해서는 Kafka를 해당 서버에 설치 및 설정해야 함!

1. docker 설치( 윈도우는 설치 시 docker-compose까지 자동)

   =>docker-compose -v 로 docker-compose 확인!



2. docker-compose.yml 작성

    ⇒ 컨테이너를 생성하기 위해 이미지, 포트, 볼륨, 환경변수 설정

    <mark>docker-compose는 tab을 인식 xx!! 공백 사용ooo</mark>

    ```java

    services:

      zookeeper:

        image: wurstmeister/zookeeper

        container_name: zookeeper

        ports:

          - "2181:2181"

      kafka:

        image: wurstmeister/kafka:2.12-2.5.0

        container_name: kafka

        ports:

          - "9092:9092"

        environment:

          KAFKA_ADVERTISED_HOST_NAME: 127.0.0.1

          KAFKA_ZOOKEEPER_CONNECT: zookeeper:2181

        volumes:

          - /var/run/docker.sock:/var/run/docker.sock

    ```

````
📎 

  |image| :  컨테이너로 실행할 도커 이미지 “{이미지명}:{태그}”

             ex) wurstmeister/zookeeper (wurstmeister/kafka 제공 이미지)

  |container_name| : 컨테이너 이름

  |ports| : 접근 포트 (컨테이너 외부:내부)

  |volumes| : 호스트 지정된 경로로 컨테이너 볼륨

  |environment| : 환경변수

  KAFKA_ADVERTISED_HOST_NAME : 본인의 docker host ip
 ````

3. 컨테이너 실행

    ⇒ yml 을 자신이 원하는 폴더에 넣은뒤 yml 경로의 cmd를 열어 실행

````
 ## 컨테이너 실행
 docker-compose up -d
 docker exec -it kafka /bin/bash/opt/kafka/config

 ## vi server.properties
    => Socket Server Setting 부분 listeners와 advertised.listeners의 주석 처리를 해제
      advertised.listeners 에는 공인 ip 적기
      밑에 auto.create.topics.enable=true 추가하여 자동으로 주제가 생성되도록 처리
 ````


◼ 버전 정보 및 카프카 의존성 설정

```
Spring boot 3.2.5
jdk 17
```

```groovy

implementation 'org.springframework.kafka:spring-kafka'

```

```yaml

spring:

  kafka:

    bootstrap-servers: 127.0.0.1:9092

    producer:

      key-serializer: org.apache.kafka.common.serialization.StringSerializer

      value-serializer: org.apache.kafka.common.serialization.StringSerializer

    consumer:

      key-deserializer: org.apache.kafka.common.serialization.StringDeserializer

      value-deserializer: org.springframework.kafka.support.serializer.ErrorHandlingDeserializer

      properties:

        spring:

          json:

            trusted:

              packages: "*"

          deserializer:

            value:

              delegate:

                class: org.apache.kafka.common.serialization.StringDeserializer

```

````
📎

yml에 다 지정했기 때문에 따로 config 작업은 필요하지 않음
```` 



◼ Controller 생성

```java

package com.example.kafkatest.controller;

  

import lombok.RequiredArgsConstructor;

import org.springframework.kafka.core.KafkaTemplate;

import org.springframework.stereotype.Controller;

import org.springframework.web.bind.annotation.PostMapping;

  

import java.util.Map;

  

@Controller

@RequiredArgsConstructor

public class TestController {

    private final KafkaTemplate<String, Object> kafkaTemplate;

  

    @PostMapping("/kafkatest")

    public void kafkaTest(String message) {

        System.out.println("Kafka send: " + message);

        kafkaTemplate.send("TOPIC_1", message);

    }

}

```

````
📎

주제를 TOPIC_1 으로 임시로 정한 후 메시지 전송
````
  

◼ Consumer 생성

```java

package com.example.kafkatest.kafka;

  

import org.springframework.kafka.annotation.KafkaListener;

import org.springframework.stereotype.Component;

  

@Service

public class KafkaConsumer {

  

    @KafkaListener(topics = "TOPIC_1", groupId = "foo")

    public void listenMessage(String message) {

        System.out.println("Kafka listen : " + message);

    }

}

```

````
📎

주제가 TOPIC_1인 메시지를 잘 전달 받았는지 확인
````

### TEST
  
![](https://i.imgur.com/j0ogNfs.png)


![](https://i.imgur.com/qhjz9ZA.png)


![](https://i.imgur.com/xpxAcW2.png)


🤩 카프카 관하여 정말 잘 정리해놓은 글!!

[https://velog.io/@jwpark06/Kafka-시스템-구조-알아보기](https://velog.io/@jwpark06/Kafka-%EC%8B%9C%EC%8A%A4%ED%85%9C-%EA%B5%AC%EC%A1%B0-%EC%95%8C%EC%95%84%EB%B3%B4%EA%B8%B0)

# Pub-Sub 모델
 
 메시지를 Topic으로 묶어서 보내는 Publisher 와 해당 Topic을 구독하여 메시지를 받는 Subscriber로 구성되어있다.

# Kafka와 Redis의 차이점

Redis의 경우 Publisher가 Channel에 메시지를 push할 경우 메시지를 저장하지 않고 Channel에 구독되어있는 Subscriber 모두에게 이벤트가 전송된다. 구독되어있는 Subscriber이 없는 경우엔 메시지는 아무도 보지 못하고 사라진다. (실시간)

Kafka의 경우 메시지를 Push할 경우 Topic의 Partition 메시지가 쌓이고 Topic을 구독하는 Subscriber들이 데이터를 가져간다. 그러므로 메시지를 발행한 이후에도 메시지를 가져올 수 있다. (실시간x)