# QUEUE

![](https://i.imgur.com/Neb36rR.png)

````java
Queue<String> q = new LinkedList<>();
````

`Queue`는 Stack과 달리 `FIFO(First In First Out)` 으로 선입선출의 구조이다.
데이터가 들어오는 위치인 `rear`, 데이터가 나가는 위치인 `front`가 있다.

````java
Queue<String> q = new LinkedList<>();

// 추가
q.offer("1");
q.offer("2");
q.offer("3");
q.add("4");

// 삭제
q.poll();

// 맨 앞 요소 조회
String first = q.peek();

// 모든 요소 제거
q.clear();
````

| 메소드                |                                           |
| ------------------ | ----------------------------------------- |
| boolean offer(E e) | 큐의 맨 뒤에 지정한 요소 추가 (가득 찼을 경우 false 반환)     |
| boolean add(E e)   | 큐의 맨 뒤에 지정한 요소 추가 (가득 찼을 경우 Exception 반환) |
| E poll()           | 큐의 맨 앞에서 요소 삭제 후 반환 (비어 있을 경우 null 반환)    |
| E peek()           | 큐의 맨 앞 요소 반환 (비어 있을 경우 null 반환)           |
| clear()            | 큐의 모든 요소 제거                               |

`Queue`의 인터페이스 구현체는 여러가지가 있다.

### PriorityQueue (우선순위 큐)

````java
Queue<Integer> q = new PriorityQueue<>();
````

저장 순서와 관계없이 우선순위에 따라 데이터가 꺼내진다. (null 값 저장 불가)

````java

q.offer(3);
q.offer(4);
q.offer(2);
q.offer(5);
q.offer(1);

// 큐 차례대로 poll() 시
1,2,3,4,5

// 내림차순으로 꺼내고 싶다면
Queue<Integer> q = new PriorityQueue<>(Collections.reverseOrder());
````

넣은 순서와 관계없이 오름차순으로 차례대로 꺼내진다.

### Deque ✨

