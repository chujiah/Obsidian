# Primitive Type (기본형)

<mark>실제 값을 담을 수 있는 타입</mark>, 객체가 아닌 값을 저장하며 메모리 사용량이 고정되어 있고 기본 연산을 빠르게 수행 (null 값 없음!)

## Integer Types (정수형)

| 정수형 타입 | 메모리 크기 | 데이터 표현 범위                                  |
| ------ | ------ | ------------------------------------------ |
| byte   | 1 byte | -128 ~ 127                                 |
| short  | 2 byte | -32768 ~ 32767                             |
| int    | 4byte  | -2147483648 ~ 2147483647                   |
| long   | 8byte  | -9223372036854775808 ~ 9223372036854775807 |
````
💡 long 변수에 값을 대입할 때는 int 자료형의 값보다 클 경우는 뒤에 L을 붙여야함
````

### 2/8/16진수

**2진수**

숫자 앞에 `0b` 또는 `0B`를 붙여서 표현

````java
int binaryNumber = 0b1010;
````

**8진수**

숫자 앞에 `0`을 붙여서 표현

````java
int octalNumber = 012;
````

**16진수**

숫자 앞에 `0x` 또는 `0X`를 붙여서 표현

````java
int hexNumber = 0xA;
````

**진법변환**

숫자 => 문자열

| 진수   | 코드                            |
| ---- | ----------------------------- |
| 2진수  | Integer.toBinaryString(int i) |
| 8진수  | Integer.toOctalString(int i)  |
| 16진수 | Integer.toHexString(int i)    |

문자열 => 숫자

Integer.parseInt(String s, int radix)

## Floating-Point Type (부동 소수점형)

| 실수형 타입 | 메모리 크기 | 표현 범위                            | 유효 자릿수                   |
| ------ | ------ | -------------------------------- | ------------------------ |
| float  | 4byte  | (3.4 X 10^-38) ~ (3.4 X 10^38)   | 소수점 약 6~7자리 대부분 정확히 표현   |
| double | 8byte  | (1.7 X 10^-308) ~ (1.7 X 10^308) | 소수점 약 15~16자리 대부분 정확히 표현 |
````
💡

float 변수에 값을 대입 시 F 또는 f 를 꼭 붙여주어야함
double 변수는 D 또는 d, 생략 가능함
````

````java
BicDecimal b1 = new BigDecimal("100.0");
BicDecimal b2 = new BigDecimal("99.0");

BigDecimal sum = b1.add(b2); // 더하기
BigDecimal difference = b1.subtract(b2); // 빼기
BigDecimal product = b1.multiply(b2) // 곱하기
BigDecimal quotient = b1.divibe(b2, 10, BigDecimal.ROUND_HALF_UP) // 소수점 이하 10자리 반올림 나누기
int comparison = b1.compareTo(b2); // 비교
````

````
📎

실수는 부정확한 연산의 한계가 있다.
컴퓨터는 이런 한계를 최소화하기 위해 부동 소수점 방식을 이용한다.
하지만 부동 소수점 방식으로도 정밀도의 제한이 있다.

이를 해결하기 위해서 BigDecimal 클래스를 사용한다.
````

## Boolean Type (논리형)

| 논리형 타입  | 메모리 크기 | 범위          |
| ------- | ------ | ----------- |
| boolean | 1 byte | true, false |

## Character Type (문자형)

| 문자형 타입 | 메모리 크기 | 범위        |
| ------ | ------ | --------- |
| char   | 2 byte | 0 ~ 65535 |

````java

char a1 = 'a';
char a2 = 97; // 아스키 코드
char a3 = '\u0061' // 유니코드

char a = 'C' - 2; // 103-2 'A'
````

### 아스키 코드

````
영문 알파벳을 사용하는 대표적인 문자 인코딩
````

![](https://i.imgur.com/7QRI8Bv.png)

### 유니코드

````
세계 모든 언어와 기호에 코드값을 부여한 것
````

````
📎

유니코드를 위한 가변길이 문자 인코딩 방식

대표적인 방식으론 UTF-8이 있다.
UTF-8은 유니코드 범위에 따라 가변적으로 2byte로 고정되는 EUC-KR과는 달리 1~4byte로 인코딩하는 가변 크기 인코딩 방식이다.
````

# Reference Type (참조형)

실제 값을 저장하는 것이 아닌 <mark>데이터가 저장된 메모리의 `주소 값`을 가지는 자료형</mark>, 객체를 참조하는 변수 타입

````
💡

기본형과 참조형의 차이

기본 자료형은 Stack에 직접 값을 할당하지만 
참조 자료형은 Stack에서 Heap의 주소를 참조하고 있고 실제 값은 Heap에 존재한다. 
````

| 참조 자료형     |                      |
| ---------- | -------------------- |
| String     | 문자열 저장 클래스           |
| ArrayList  | 동적 배열 클래스            |
| HashMap    | 키-값 쌍 저장 클래스         |
| HashSet    | 중복이 없는 고유한 요소 저장 클래스 |
| LinkedList | 연결 리스트 클래스           |
| Queue      | 큐를 나타내는 인터페이스        |
| Stack      | 스택을 나타내는 클래스         |

# Wrapper Class

| 기본 자료형  | 래퍼클래스     |
| ------- | --------- |
| byte    | Byte      |
| char    | Character |
| int     | Integer   |
| float   | Float     |
| double  | Double    |
| boolean | Boolean   |
| long    | Long      |
| short   | Short     |

````java
Integer a = null;
Integer b = 3; // 박싱
int c = b // 언박싱

List<int> list1 = new ArrayList<>(); // X 불가
List<Integer> list2 = new ArrayList<>(); // O 가능
````

````
📎

래퍼 클래스 경우에는 ==이 아닌 equals()을 사용하며 null 초기화가 가능하다.
산술연산은 불가능하여 제네릭 타입 내에서 사용이 가능하다.
````

