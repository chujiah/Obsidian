# Primitive Type (기초형)

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

