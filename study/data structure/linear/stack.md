# Stack

![](https://i.imgur.com/tRS7esH.png)

````java
Stack<Number> st = new Stack<>();
````

`쌓다` 라는 의미의 스택은 데이터를 아래부터 쌓아 올린 형태의 자료구조이다.
가장 마지막에 추가된 데이터가 먼저 삭제되는 `LIFO(Last In First Out)` 인 후입선출의 구조를 가지고 있다.

| 메서드                      |                            |
| ------------------------ | -------------------------- |
| boolean empty()          | 스택이 비어있는지 확인               |
| Object peek()            | 스택 최상단에 있는 객체를 반환 (조회의 개념) |
| Object pop()             | 스택 최상단에 있는 객체를 꺼냄 (삭제의 개념) |
| Object push(Object item) | 스택에 객체를 저장                 |
| int search(Object o)     | 스택에 주어진 위치를 찾아서 객체 반환      |

````
💡

peek() 와 pop()의 경우 비어있을 경우 Exception이 발생함
search()의 경우 찾지 못할 경우 -1 반환 , 배열과 달리 위치는 1부터 시작이 된다.
````

