# 연결 리스트 (Linked list)

````
배열은 순차적으로 이루어진 공간에 데이터를 나열하는 구조라면 연결 리스트는 여러 곳에 떨어져 있는 데이터를 화살표로 연결하여 관리하는 데이터 구조
````

## 연결 리스트의 핵심 요소

![](https://i.imgur.com/TZTFdOS.png)

### 노드 (Node)
- 데이터를 저장하는 `데이터 필드`와 다음 노드를 가리키는 `링크 필드`로 구성

![](https://i.imgur.com/6R3Gqll.png)

### 포인터 (Pointer)
- 이전 또는 다음 노드와의 연결 정보를 가지고 있는 공간

![](https://i.imgur.com/Hcp8HBg.png)

### 헤드 (Head)
- 연결 리스트에서 가장 처음 위치하는 노드

### 테일 (Tail)
- 연결 리스트에서 가장 마지막 위치하는 노드

## 연결 리스트의 종류



### 단일 연결 리스트 (Singly Linked List)

![](https://i.imgur.com/PrLwoht.png)

````
각 노드가 다음 노드를 가리키는 포인터 하나를 가지고 있음
````

### 이중 연결 리스트 (Doubly Linked List)

![](https://i.imgur.com/5NdsNz6.png)

````
각 노드가 다음 노드와 이전 노드를 가리키는 포인터 두개를 가지고 있음
````

### 원형 연결 리스트 (Circular Linked List)

![](https://i.imgur.com/8hJdNOx.png)

````
마지막 노드가 첫 번째 노드를 가리켜 원형으로 연결된 리스트
````

## 시간 복잡도

````
접근 : O(n)
탐색 : O(n)
삽입 : O(1)
삭제 : O(1)
````
