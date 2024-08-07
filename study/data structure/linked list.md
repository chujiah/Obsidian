# 연결 리스트 (Linked list)

````java
LinkedList<Integer> list = new LinkedList<>();
````

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

````
📎

배열과 반대
````

## 요소 추가

| 메서드                                  |                             |
| ------------------------------------ | --------------------------- |
| void addFirst(Object obj)            | 연결리스트의 맨 앞에 객체 추가           |
| void addLast(Object obj)             | 연결리스트의 맨 뒤에 객체 추가           |
| boolean add(Object obj)              | 연결리스트의 마지막에 객체 추가           |
| void add(int index, Object obj)      | 지정된 위치에 객체 추가               |
| void addAll(Collection c)            | 주어진 컬렉션의 모든 객체를 마지막에 추가     |
| void addAll(int index, Collection c) | 지정한 위치부터 주어진 컬렉션의 모든 객체를 추가 |

## 요소 삭제

| 메서드                             |                                    |
| ------------------------------- | ---------------------------------- |
| Object removeFirst()            | 첫번째 노드 제거                          |
| Object removeLast()             | 마지막 노드 제거                          |
| Object remove()                 | 연결리스트의 첫번째 요소 제거                   |
| Object remove(int index)        | 지정된 위치 객체 제거                       |
| boolean remove(Object obj)      | 지정된 객체 제거                          |
| boolean removeAll(Collection c) | 지정한 컬렉션의 저장된 것과 동일한 노드 모두 제거       |
| boolean retainAll(Collection c) | 지정한 컬렉션의 저장된 객체와 공통된 것들만 남기고 모두 제거 |
| void clear()                    | 연결 리스트를 완전히 비움                     |

## 요소 검색

| 메서드                               |                            |
| --------------------------------- | -------------------------- |
| int size()                        | 연결 리스트에 저장된 객체 수 반환        |
| boolean isEmpty()                 | 연결 리스트가 비어있는지 확인           |
| boolean contains(Object obj)      | 지정된 객체가 포함되어있는지 확인         |
| boolean containsAll(Collection c) | 지정된 컬렉션의 모든 요소가 포함되어있는지 확인 |
| int indexOf(Object obj)           | 지정된 객체가 있는 위치를 반환          |
| int lastIndexOf(Object obj)       | 지정된 객체가 있는 마지막 위치 반환       |

## 요소 가져오기 / 변경

| 메서드                                  |                           |
| ------------------------------------ | ------------------------- |
| Object get(int index)                | 지정된 위치에 객체를 반환            |
| List subList(int findex, int tindex) | findex부터 tindex까지의 객체를 반환 |
| Object set(int index, Object obj)    | 지정한 위치의 객체를 주어진 객체로 변경    |
