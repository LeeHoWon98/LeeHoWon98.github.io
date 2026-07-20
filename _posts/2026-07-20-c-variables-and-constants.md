---
title: "C언어 변수와 상수 완벽 정리: 선언, 초기화, const와 리터럴까지"
date: 2026-07-20
categories: [C Language, Basic]
tags: [c, variable, constant, const, literal, initialization]
---

## 들어가며

C언어로 프로그램을 작성할 때 가장 먼저 배우는 개념 중 하나가 **변수와 상수**이다.

프로그램은 숫자, 문자, 계산 결과와 같은 데이터를 처리하면서 동작한다. 이 데이터를 저장하기 위해 사용하는 것이 변수이고, 실행 중에 값을 바꾸지 않도록 고정할 때 사용하는 것이 상수이다.

```c
int age = 20;
const double PI = 3.141592;
```

위 코드에서 `age`는 값을 변경할 수 있는 변수이고, `PI`는 한 번 정한 뒤 값을 변경할 수 없는 상수이다.

> 이번 글에서는 변수의 선언과 초기화, 변수 이름 규칙, 상수 선언 방법, 리터럴과 심볼릭 상수의 차이까지 정리한다.

---

## 1. 변수란?

변수는 프로그램에서 사용할 값을 저장하기 위해 이름을 붙인 메모리 공간이다.

예를 들어 사용자의 나이를 저장해야 한다면 다음과 같이 변수를 만들 수 있다.

```c
int age = 20;
```

이 코드는 다음과 같은 의미를 가진다.

| 구성 요소 | 의미 |
|---|---|
| `int` | 변수에 저장할 데이터의 자료형 |
| `age` | 변수의 이름 |
| `20` | 변수에 저장할 값 |
| `=` | 오른쪽 값을 왼쪽 변수에 저장하는 대입 연산자 |

변수의 값을 출력해 보면 다음과 같다.

```c
#include <stdio.h>

int main(void) {
    int age = 20;

    printf("나이: %d\n", age);

    return 0;
}
```

출력 결과:

```text
나이: 20
```

---

## 2. 변수가 필요한 이유

프로그램 안에서 숫자를 직접 사용해도 계산은 가능하다.

```c
#include <stdio.h>

int main(void) {
    printf("%d\n", 10000 * 3);

    return 0;
}
```

하지만 `10000`과 `3`이 무엇을 의미하는지 코드만 보고 바로 파악하기 어렵다.

변수를 사용하면 값의 의미를 이름으로 표현할 수 있다.

```c
#include <stdio.h>

int main(void) {
    int price = 10000;
    int quantity = 3;
    int totalPrice = price * quantity;

    printf("총 가격: %d원\n", totalPrice);

    return 0;
}
```

출력 결과:

```text
총 가격: 30000원
```

변수를 사용하면 다음과 같은 장점이 있다.

- 값의 의미를 쉽게 파악할 수 있다.
- 동일한 값을 여러 번 사용할 수 있다.
- 값이 변경되었을 때 수정하기 쉽다.
- 계산 결과를 저장한 뒤 다시 사용할 수 있다.
- 코드의 가독성과 유지보수성이 높아진다.

---

## 3. 변수 선언

C언어에서는 변수를 사용하기 전에 먼저 선언해야 한다.

기본적인 선언 형식은 다음과 같다.

```c
자료형 변수이름;
```

예시:

```c
int age;
double height;
char grade;
```

이 코드는 변수를 만들기만 하고 아직 값을 저장하지 않은 상태이다.

여러 변수를 한 번에 선언할 수도 있다.

```c
int x, y, z;
```

다만 코드의 가독성을 위해 변수를 한 줄에 하나씩 선언하는 방식도 많이 사용한다.

```c
int x;
int y;
int z;
```

---

## 4. 변수 초기화

변수를 선언하면서 처음 값을 저장하는 것을 **초기화(Initialization)** 라고 한다.

```c
int age = 20;
```

변수 선언과 값 대입을 따로 작성할 수도 있다.

```c
int age;
age = 20;
```

두 코드 모두 결과는 같지만, 가능하면 변수를 선언할 때 초기화하는 것이 안전하다.

```c
#include <stdio.h>

int main(void) {
    int number;

    printf("%d\n", number);

    return 0;
}
```

초기화하지 않은 지역 변수에는 예측할 수 없는 값이 들어 있을 수 있다. 이러한 값을 흔히 **쓰레기 값**이라고 부른다.

따라서 다음처럼 초기값을 지정하는 습관을 들이는 것이 좋다.

```c
int number = 0;
double average = 0.0;
char grade = ' ';
```

---

## 5. 변수에 새로운 값 저장하기

변수는 프로그램 실행 중 값을 변경할 수 있다.

```c
#include <stdio.h>

int main(void) {
    int score = 70;

    printf("변경 전 점수: %d\n", score);

    score = 90;

    printf("변경 후 점수: %d\n", score);

    return 0;
}
```

출력 결과:

```text
변경 전 점수: 70
변경 후 점수: 90
```

처음 선언할 때는 자료형을 작성한다.

```c
int score = 70;
```

이미 선언된 변수에 새로운 값을 저장할 때는 자료형을 다시 작성하지 않는다.

```c
score = 90;
```

다음과 같이 작성하면 같은 이름의 변수를 다시 선언하는 것이므로 오류가 발생한다.

```c
int score = 70;
int score = 90;  // 오류
```

---

## 6. 대입 연산자의 방향

C언어에서 `=`는 수학의 등호가 아니라 **대입 연산자**이다.

```c
age = 20;
```

이 코드는 오른쪽 값 `20`을 왼쪽 변수 `age`에 저장한다는 뜻이다.

다음 코드도 가능하다.

```c
int a = 10;
int b = 20;

a = b;
```

`a = b`를 실행하면 `b`에 저장된 값이 `a`에 복사된다.

```c
#include <stdio.h>

int main(void) {
    int a = 10;
    int b = 20;

    a = b;

    printf("a = %d\n", a);
    printf("b = %d\n", b);

    return 0;
}
```

출력 결과:

```text
a = 20
b = 20
```

`b`의 값이 사라지거나 이동하는 것이 아니라, 값 `20`이 `a`에도 복사된다.

> 대입은 항상 오른쪽의 값을 계산한 뒤 왼쪽 변수에 저장한다.

---

## 7. 변수의 값을 이용한 계산

변수에는 계산 결과를 저장할 수 있다.

```c
#include <stdio.h>

int main(void) {
    int a = 10;
    int b = 20;
    int sum = a + b;

    printf("%d + %d = %d\n", a, b, sum);

    return 0;
}
```

출력 결과:

```text
10 + 20 = 30
```

변수 자신의 값을 이용해 값을 변경할 수도 있다.

```c
int number = 10;

number = number + 5;
```

처리 순서는 다음과 같다.

```text
1. 오른쪽의 number + 5를 계산한다.
2. 현재 number가 10이므로 결과는 15이다.
3. 계산 결과 15를 왼쪽 number에 저장한다.
```

최종적으로 `number`의 값은 `15`가 된다.

```c
#include <stdio.h>

int main(void) {
    int number = 10;

    number = number + 5;

    printf("%d\n", number);

    return 0;
}
```

출력 결과:

```text
15
```

이 코드는 복합 대입 연산자를 이용해 짧게 쓸 수도 있다.

```c
number += 5;
```

---

## 8. 서로 다른 자료형의 변수

변수는 저장할 값에 맞는 자료형으로 선언해야 한다.

```c
int age = 22;
double height = 175.5;
char grade = 'A';
```

| 자료형 | 저장하는 값 | 예시 |
|---|---|---|
| `int` | 정수 | `10`, `-5`, `2026` |
| `double` | 실수 | `3.14`, `175.5` |
| `char` | 문자 하나 | `'A'`, `'Y'` |
| `float` | 실수 | `3.14f` |

전체 예제:

```c
#include <stdio.h>

int main(void) {
    int age = 22;
    double height = 175.5;
    char grade = 'A';

    printf("나이: %d\n", age);
    printf("키: %.1f\n", height);
    printf("등급: %c\n", grade);

    return 0;
}
```

출력 결과:

```text
나이: 22
키: 175.5
등급: A
```

---

## 9. 변수 이름 규칙

C언어의 변수 이름은 개발자가 자유롭게 정할 수 있지만 몇 가지 규칙을 지켜야 한다.

### 사용할 수 있는 문자

변수 이름에는 다음 문자를 사용할 수 있다.

- 영문 대문자 `A`부터 `Z`
- 영문 소문자 `a`부터 `z`
- 숫자 `0`부터 `9`
- 밑줄 `_`

올바른 예시:

```c
int age;
int studentScore;
int total_price;
int number2;
```

### 숫자로 시작할 수 없다

```c
int 2number;  // 오류
```

숫자는 첫 글자가 아닌 위치에서는 사용할 수 있다.

```c
int number2;
```

### 공백을 사용할 수 없다

```c
int student score;  // 오류
```

다음처럼 작성해야 한다.

```c
int studentScore;
```

또는:

```c
int student_score;
```

### 특수문자를 사용할 수 없다

```c
int total-price;  // 오류
int score!;       // 오류
```

밑줄 `_`을 제외한 특수문자는 사용할 수 없다.

### 예약어를 사용할 수 없다

예약어는 C언어에서 이미 특별한 의미로 사용되는 단어이다.

```c
int int;     // 오류
int return;  // 오류
int if;      // 오류
```

대표적인 예약어는 다음과 같다.

```text
int, char, float, double, if, else, for, while, return, switch
```

### 대소문자를 구분한다

C언어는 대문자와 소문자를 서로 다른 문자로 구분한다.

```c
int age = 20;
int Age = 30;
int AGE = 40;
```

`age`, `Age`, `AGE`는 모두 서로 다른 변수이다.

---

## 10. 좋은 변수 이름 작성 방법

문법적으로 올바른 이름이라고 해서 모두 좋은 변수 이름은 아니다.

다음 변수는 무엇을 저장하는지 알기 어렵다.

```c
int a = 10000;
int b = 3;
int c = a * b;
```

의미가 드러나는 이름을 사용하면 코드가 훨씬 읽기 쉬워진다.

```c
int price = 10000;
int quantity = 3;
int totalPrice = price * quantity;
```

좋은 변수 이름은 다음 기준을 따르는 것이 좋다.

- 저장하는 값의 의미를 나타낸다.
- 지나치게 짧거나 모호한 이름을 피한다.
- 단어가 여러 개라면 일관된 표기법을 사용한다.
- 변수의 자료형보다 역할을 표현한다.

### 카멜 표기법

첫 단어는 소문자로 시작하고, 다음 단어부터 첫 글자를 대문자로 작성한다.

```c
int studentScore;
int totalPrice;
int userAge;
```

### 스네이크 표기법

단어 사이를 밑줄로 연결한다.

```c
int student_score;
int total_price;
int user_age;
```

프로젝트 안에서는 한 가지 표기법을 정해 일관되게 사용하는 것이 중요하다.

---

## 11. 상수란?

상수는 프로그램 실행 중 값이 변하지 않는 데이터이다.

숫자나 문자처럼 코드에 직접 작성한 값도 상수에 포함된다.

```c
10
3.14
'A'
"Hello"
```

또한 `const` 키워드를 사용해 이름이 있는 상수를 만들 수 있다.

```c
const double PI = 3.141592;
```

`PI`는 초기화한 뒤 값을 변경할 수 없다.

```c
const double PI = 3.141592;

PI = 3.14;  // 오류
```

상수는 변경되면 안 되는 값을 보호하고, 값의 의미를 이름으로 표현하기 위해 사용한다.

---

## 12. `const`를 이용한 상수 선언

`const`를 자료형 앞에 붙이면 값을 변경할 수 없는 변수가 된다.

```c
const int MAX_SCORE = 100;
const double PI = 3.141592;
```

전체 예제:

```c
#include <stdio.h>

int main(void) {
    const int MAX_SCORE = 100;
    int score = 85;

    printf("현재 점수: %d\n", score);
    printf("최대 점수: %d\n", MAX_SCORE);

    return 0;
}
```

출력 결과:

```text
현재 점수: 85
최대 점수: 100
```

상수는 일반적으로 선언과 동시에 초기화해야 한다.

```c
const int MAX_SCORE = 100;
```

다음처럼 초기화하지 않으면 이후에 값을 저장할 수 없다.

```c
const int MAX_SCORE;
MAX_SCORE = 100;  // 오류
```

---

## 13. 상수 이름을 대문자로 작성하는 이유

C언어 문법상 상수 이름을 반드시 대문자로 작성해야 하는 것은 아니다.

다음 코드도 정상적으로 동작한다.

```c
const int maxScore = 100;
```

하지만 일반적으로 상수는 변수와 쉽게 구분하기 위해 대문자와 밑줄을 사용한다.

```c
const int MAX_SCORE = 100;
const double TAX_RATE = 0.1;
const int MAX_STUDENT_COUNT = 30;
```

코드를 읽는 사람은 이름만 보고도 해당 값이 변경되지 않는 상수라는 것을 예상할 수 있다.

| 종류 | 일반적인 이름 |
|---|---|
| 변수 | `studentScore` |
| 상수 | `MAX_SCORE` |

---

## 14. 리터럴 상수

코드에 직접 작성된 값을 **리터럴(Literal)** 이라고 한다.

```c
int age = 20;
double pi = 3.14;
char grade = 'A';
char name[] = "HoWon";
```

여기에서 다음 값들이 리터럴이다.

| 리터럴 | 종류 |
|---|---|
| `20` | 정수 리터럴 |
| `3.14` | 실수 리터럴 |
| `'A'` | 문자 리터럴 |
| `"HoWon"` | 문자열 리터럴 |

### 정수 리터럴

```c
int decimal = 10;
int octal = 012;
int hexadecimal = 0xA;
```

`10`, `012`, `0xA`는 표기 방법은 다르지만 모두 10을 나타낸다.

### 실수 리터럴

```c
double pi = 3.14;
float rate = 1.5f;
```

실수 리터럴은 기본적으로 `double` 자료형으로 처리된다.

`float` 리터럴은 값 뒤에 `f`를 붙인다.

```c
float rate = 1.5f;
```

### 문자 리터럴

문자 하나는 작은따옴표로 감싼다.

```c
char grade = 'A';
```

### 문자열 리터럴

문자열은 큰따옴표로 감싼다.

```c
char message[] = "Hello";
```

---

## 15. `#define`을 이용한 상수

C언어에서는 전처리기 지시문인 `#define`을 사용해 상수를 정의할 수도 있다.

```c
#define PI 3.141592
#define MAX_SCORE 100
```

사용 예제:

```c
#include <stdio.h>

#define PI 3.141592

int main(void) {
    double radius = 5.0;
    double area = PI * radius * radius;

    printf("원의 넓이: %.2f\n", area);

    return 0;
}
```

출력 결과:

```text
원의 넓이: 78.54
```

`#define`은 컴파일 전에 코드 안의 이름을 지정한 값으로 치환한다.

```c
double area = PI * radius * radius;
```

전처리 과정에서 다음과 비슷하게 바뀐다.

```c
double area = 3.141592 * radius * radius;
```

`#define`에는 대입 연산자 `=`와 세미콜론 `;`을 사용하지 않는다.

```c
#define PI 3.141592
```

다음과 같이 작성하면 안 된다.

```c
#define PI = 3.141592;  // 잘못된 작성
```

---

## 16. `const`와 `#define`의 차이

`const`와 `#define` 모두 변경하지 않을 값을 표현할 때 사용할 수 있지만 동작 방식이 다르다.

| 구분 | `const` | `#define` |
|---|---|---|
| 처리 시점 | 컴파일 과정 | 전처리 과정 |
| 자료형 | 존재함 | 존재하지 않음 |
| 문법 | 일반 변수 선언과 유사 | 단순한 문자 치환 |
| 디버깅 | 비교적 쉬움 | 상대적으로 어려울 수 있음 |
| 범위 관리 | 변수의 범위 규칙 적용 | 전처리기 규칙 적용 |

`const` 예시:

```c
const double PI = 3.141592;
```

`#define` 예시:

```c
#define PI 3.141592
```

단순한 상수 값을 만들 때는 자료형을 명확히 표현할 수 있는 `const`를 우선 고려하는 것이 좋다.

다만 배열 크기, 조건부 컴파일, 매크로처럼 전처리 기능이 필요할 때는 `#define`이 사용된다.

---

## 17. 변수와 상수 비교

| 구분 | 변수 | 상수 |
|---|---|---|
| 값 변경 | 가능 | 불가능 |
| 선언 예시 | `int age = 20;` | `const int MAX_AGE = 100;` |
| 주요 목적 | 변하는 데이터 저장 | 고정값 보호 |
| 이름 표기 | 카멜 또는 스네이크 표기 | 주로 대문자와 밑줄 |
| 사용 예시 | 점수, 잔액, 개수 | 원주율, 최대값, 설정값 |

예제:

```c
#include <stdio.h>

int main(void) {
    const int MAX_SCORE = 100;
    int score = 80;

    printf("변경 전 점수: %d\n", score);

    score = 95;

    printf("변경 후 점수: %d\n", score);
    printf("최대 점수: %d\n", MAX_SCORE);

    return 0;
}
```

출력 결과:

```text
변경 전 점수: 80
변경 후 점수: 95
최대 점수: 100
```

`score`는 변수이므로 값이 변경되지만, `MAX_SCORE`는 상수이므로 변경할 수 없다.

---

## 18. 자주 발생하는 실수

### 초기화하지 않은 변수 사용

```c
int number;

printf("%d\n", number);
```

지역 변수를 초기화하지 않고 사용하면 예측할 수 없는 값이 출력될 수 있다.

```c
int number = 0;
```

### 선언한 자료형과 맞지 않는 값 저장

```c
int average = 95.7;
```

`int`에는 정수만 저장되므로 소수점 이하가 사라진다.

```c
double average = 95.7;
```

### 문자와 문자열 혼동

```c
char grade = "A";  // 오류
```

문자 하나는 작은따옴표를 사용한다.

```c
char grade = 'A';
```

### 상수 값 변경

```c
const int MAX_SCORE = 100;

MAX_SCORE = 200;  // 오류
```

### 대입과 비교 혼동

```c
int number = 10;
```

`=`는 값을 저장하는 대입 연산자이다.

조건문에서 두 값이 같은지 비교할 때는 `==`를 사용한다.

```c
if (number == 10) {
    printf("number는 10입니다.\n");
}
```

---

## 19. 변수와 상수를 활용한 예제

원의 넓이를 계산하는 프로그램을 작성해 보자.

```c
#include <stdio.h>

int main(void) {
    const double PI = 3.141592;
    double radius = 5.0;
    double area = PI * radius * radius;

    printf("반지름: %.1f\n", radius);
    printf("원의 넓이: %.2f\n", area);

    return 0;
}
```

출력 결과:

```text
반지름: 5.0
원의 넓이: 78.54
```

이 코드에서 각 값의 역할은 다음과 같다.

| 이름 | 종류 | 역할 |
|---|---|---|
| `PI` | 상수 | 변하지 않는 원주율 저장 |
| `radius` | 변수 | 원의 반지름 저장 |
| `area` | 변수 | 계산된 원의 넓이 저장 |

반지름은 계산할 때마다 달라질 수 있으므로 변수로 선언한다.

원주율은 계산 중 변경되면 안 되므로 상수로 선언한다.

---

## 핵심 정리

변수는 값을 저장하고 필요에 따라 변경할 수 있는 메모리 공간이다.

```c
int score = 80;

score = 90;
```

상수는 한 번 정한 값을 변경하지 못하도록 보호한다.

```c
const int MAX_SCORE = 100;
```

코드에 직접 작성한 숫자, 문자, 문자열은 리터럴이라고 한다.

```c
10
3.14
'A'
"Hello"
```

C언어에서 상수를 표현하는 대표적인 방법은 `const`와 `#define`이다.

```c
const double PI = 3.141592;
#define MAX_SCORE 100
```

변수와 상수를 적절히 사용하면 값의 의미가 명확해지고, 실수로 중요한 값이 변경되는 문제를 예방할 수 있다.