---
title: "C언어 기본 자료형 정리: int, char, float, double"
date: 2026-06-25 21:00:00 +0900
categories: [C Language, Basic]
tags: [c, data-type, int, char, float, double]
---

## 들어가며

C언어에서 **자료형(Data Type)** 은 변수가 어떤 종류의 데이터를 저장하는지 정하는 규칙이다.

예를 들어 나이처럼 정수만 저장해야 하는 값, 키나 몸무게처럼 소수점이 필요한 값, 문자 하나를 저장해야 하는 값은 각각 다른 자료형을 사용한다.

자료형을 제대로 이해하면 메모리를 효율적으로 사용하고, 출력 형식과 입력 형식을 올바르게 작성할 수 있다.

> 이 글에서는 C언어에서 가장 기본적으로 사용하는 `int`, `char`, `float`, `double` 자료형을 정리한다.

---
![C-language](/assets/img/post/c.png){: width="700" .shadow .rounded-10 }

## 1. 변수와 자료형

변수는 데이터를 저장하기 위한 메모리 공간에 이름을 붙인 것이다.

C언어에서는 변수를 만들 때 반드시 어떤 자료형인지 함께 작성해야 한다.

```c
int age = 20;
char grade = 'A';
float height = 175.5f;
double pi = 3.141592;
```

위 코드에서 각 변수는 다음과 같은 데이터를 저장한다.

| 변수 | 자료형 | 저장하는 값 |
|---|---|---|
| `age` | `int` | 정수 |
| `grade` | `char` | 문자 하나 |
| `height` | `float` | 실수 |
| `pi` | `double` | 더 정밀한 실수 |

---

## 2. 정수형: `int`

`int`는 소수점이 없는 정수를 저장할 때 사용하는 자료형이다.

```c
#include <stdio.h>

int main(void) {
    int age = 22;
    int score = 100;
    int temperature = -5;

    printf("age: %d\n", age);
    printf("score: %d\n", score);
    printf("temperature: %d\n", temperature);

    return 0;
}
```

출력 결과:

```text
age: 22
score: 100
temperature: -5
```

`int`를 출력할 때는 `%d`를 사용한다.

```c
int number = 10;

printf("%d", number);
```

### `int` 사용 예시

```c
int studentCount = 30;
int year = 2026;
int total = 100 + 200;
```

정수형은 나이, 점수, 개수, 날짜, 반복문 변수 등에 자주 사용한다.

---

## 3. 문자형: `char`

`char`는 문자 하나를 저장하는 자료형이다.

문자 하나는 반드시 작은따옴표 `' '`로 감싸야 한다.

```c
#include <stdio.h>

int main(void) {
    char grade = 'A';
    char answer = 'Y';
    char symbol = '#';

    printf("grade: %c\n", grade);
    printf("answer: %c\n", answer);
    printf("symbol: %c\n", symbol);

    return 0;
}
```

출력 결과:

```text
grade: A
answer: Y
symbol: #
```

`char`를 출력할 때는 `%c`를 사용한다.

```c
char alphabet = 'C';

printf("%c", alphabet);
```

### 문자와 문자열의 차이

```c
char ch = 'A';
```

위 코드는 문자 하나를 저장한다.

```c
char name[] = "HoWon";
```

위 코드는 여러 문자가 모인 문자열을 저장한다.

| 구분 | 작성 방법 | 예시 |
|---|---|---|
| 문자 | 작은따옴표 사용 | `'A'` |
| 문자열 | 큰따옴표 사용 | `"Hello"` |

> `char`는 문자 하나만 저장한다. `"A"`는 문자열이고 `'A'`는 문자이다.

---

## 4. 실수형: `float`

`float`는 소수점이 있는 실수를 저장할 때 사용한다.

```c
#include <stdio.h>

int main(void) {
    float height = 175.5f;
    float weight = 68.2f;

    printf("height: %.1f\n", height);
    printf("weight: %.1f\n", weight);

    return 0;
}
```

출력 결과:

```text
height: 175.5
weight: 68.2
```

실수를 출력할 때는 `%f`를 사용한다.

```c
float number = 3.14f;

printf("%f", number);
```

소수점 자리 수를 지정하고 싶다면 `%.nf` 형식을 사용한다.

```c
float number = 3.141592f;

printf("%.2f\n", number);
printf("%.4f\n", number);
```

출력 결과:

```text
3.14
3.1416
```

### 왜 `f`를 붙일까?

C언어에서 `3.14` 같은 실수 상수는 기본적으로 `double`로 처리된다.

따라서 `float` 변수에 값을 넣을 때는 아래처럼 `f`를 붙이는 습관을 들이면 좋다.

```c
float number = 3.14f;
```

---

## 5. 실수형: `double`

`double`도 소수점이 있는 값을 저장하는 자료형이다.

`float`보다 더 많은 자릿수를 정확하게 표현할 수 있다.

```c
#include <stdio.h>

int main(void) {
    double pi = 3.141592653589793;
    double average = 95.123456;

    printf("pi: %.15f\n", pi);
    printf("average: %.6f\n", average);

    return 0;
}
```

출력 결과:

```text
pi: 3.141592653589793
average: 95.123456
```

`double`도 `printf()`에서는 `%f`를 사용해 출력한다.

```c
double pi = 3.141592;

printf("%f", pi);
```

---

## 6. `float`와 `double` 차이

| 구분 | `float` | `double` |
|---|---:|---:|
| 일반적인 크기 | 4바이트 | 8바이트 |
| 정밀도 | 약 6~7자리 | 약 15~16자리 |
| 값 저장 예시 | `3.14f` | `3.141592653589793` |
| 사용 상황 | 간단한 실수 계산 | 더 정확한 실수 계산 |

정확도가 중요하지 않은 간단한 실수는 `float`를 사용할 수 있다.

하지만 실수 계산에서 오차를 줄이고 싶다면 보통 `double`을 사용하는 편이 좋다.

```c
float a = 3.14f;
double b = 3.141592653589793;
```

---

## 7. 자료형별 출력 형식 지정자

C언어에서 `printf()`를 사용할 때는 자료형에 맞는 형식 지정자를 사용해야 한다.

| 자료형 | 설명 | 출력 형식 |
|---|---|---|
| `int` | 정수 | `%d` |
| `char` | 문자 하나 | `%c` |
| `float` | 실수 | `%f` |
| `double` | 실수 | `%f` |

예시:

```c
#include <stdio.h>

int main(void) {
    int age = 22;
    char grade = 'A';
    float height = 175.5f;
    double pi = 3.141592;

    printf("age = %d\n", age);
    printf("grade = %c\n", grade);
    printf("height = %.1f\n", height);
    printf("pi = %.6f\n", pi);

    return 0;
}
```

출력 결과:

```text
age = 22
grade = A
height = 175.5
pi = 3.141592
```

---

## 8. `scanf()` 입력 형식 지정자

`scanf()`로 값을 입력받을 때도 자료형에 맞는 형식 지정자를 사용해야 한다.

```c
#include <stdio.h>

int main(void) {
    int age;
    char grade;
    float height;
    double pi;

    scanf("%d", &age);
    scanf(" %c", &grade);
    scanf("%f", &height);
    scanf("%lf", &pi);

    return 0;
}
```

입력 형식은 다음과 같다.

| 자료형 | `scanf()` 형식 지정자 |
|---|---|
| `int` | `%d` |
| `char` | `%c` |
| `float` | `%f` |
| `double` | `%lf` |

### `char` 입력에서 앞에 공백을 넣는 이유

```c
scanf(" %c", &grade);
```

`%c` 앞의 공백은 이전 입력에서 남아 있는 줄바꿈 문자 `\n`을 무시하기 위해 사용한다.

예를 들어 정수를 입력한 뒤 문자를 입력받으면, 엔터 키로 입력된 줄바꿈 문자가 남아 있을 수 있다.

```c
int age;
char grade;

scanf("%d", &age);
scanf(" %c", &grade);
```

이 경우 `" %c"`처럼 공백을 넣어 주는 것이 안전하다.

---

## 9. `sizeof`로 자료형 크기 확인하기

`sizeof` 연산자를 사용하면 자료형이나 변수의 크기를 확인할 수 있다.

```c
#include <stdio.h>

int main(void) {
    printf("int: %zu bytes\n", sizeof(int));
    printf("char: %zu bytes\n", sizeof(char));
    printf("float: %zu bytes\n", sizeof(float));
    printf("double: %zu bytes\n", sizeof(double));

    return 0;
}
```

일반적인 환경에서는 다음과 비슷한 결과가 나온다.

```text
int: 4 bytes
char: 1 bytes
float: 4 bytes
double: 8 bytes
```

> 자료형 크기는 컴파일러와 운영체제 환경에 따라 달라질 수 있다. 다만 대부분의 환경에서 `char`는 1바이트이고, `float`는 4바이트, `double`은 8바이트로 사용된다.

---

## 10. 정수형 확장 자료형

기본적인 정수형 외에도 더 작은 범위 또는 더 큰 범위의 정수를 저장하기 위한 자료형이 있다.

| 자료형 | 일반적인 크기 | 용도 |
|---|---:|---|
| `short` | 2바이트 | 비교적 작은 정수 |
| `int` | 4바이트 | 일반적인 정수 |
| `long` | 4바이트 이상 | 큰 정수 |
| `long long` | 8바이트 | 매우 큰 정수 |

예시:

```c
#include <stdio.h>

int main(void) {
    short smallNumber = 100;
    int normalNumber = 100000;
    long long bigNumber = 10000000000LL;

    printf("%d\n", smallNumber);
    printf("%d\n", normalNumber);
    printf("%lld\n", bigNumber);

    return 0;
}
```

`long long`을 출력할 때는 `%lld`를 사용한다.

---

## 11. 부호 없는 정수형: `unsigned`

정수형 앞에 `unsigned`를 붙이면 음수를 저장하지 않는 대신 양수 범위를 더 넓게 사용할 수 있다.

```c
unsigned int count = 100;
```

`unsigned int`는 음수를 저장할 수 없다.

```c
unsigned int number = -1;
```

위 코드는 의도하지 않은 매우 큰 값으로 저장될 수 있으므로 주의해야 한다.

일반적으로 개수, 크기, 인덱스처럼 음수가 필요 없는 값에 사용할 수 있다.

```c
unsigned int itemCount = 50;
```

---

## 12. 자료형 변환(Casting)

서로 다른 자료형끼리 연산하면 자동으로 자료형이 변환될 수 있다.

```c
#include <stdio.h>

int main(void) {
    int a = 10;
    int b = 3;

    double result = a / b;

    printf("%f\n", result);

    return 0;
}
```

출력 결과:

```text
3.000000
```

`a`와 `b`가 모두 `int`이므로 `10 / 3`의 결과는 정수 `3`이다.

소수점 결과를 얻고 싶다면 형 변환을 사용한다.

```c
#include <stdio.h>

int main(void) {
    int a = 10;
    int b = 3;

    double result = (double)a / b;

    printf("%f\n", result);

    return 0;
}
```

출력 결과:

```text
3.333333
```

`(double)a`는 `a`를 `double` 자료형으로 변환한다.

---

## 13. 핵심 정리

| 자료형 | 저장하는 값 | 출력 | 입력 |
|---|---|---|---|
| `int` | 정수 | `%d` | `%d` |
| `char` | 문자 하나 | `%c` | `%c` |
| `float` | 실수 | `%f` | `%f` |
| `double` | 정밀한 실수 | `%f` | `%lf` |

```c
int age = 22;
char grade = 'A';
float height = 175.5f;
double pi = 3.141592;
```

C언어에서 자료형은 변수의 성격을 정하는 가장 기본적인 요소다.

정수는 `int`, 문자 하나는 `char`, 일반적인 실수는 `float`, 더 높은 정밀도가 필요한 실수는 `double`을 사용한다. 또한 `printf()`와 `scanf()`에서는 자료형에 맞는 형식 지정자를 사용하는 것이 중요하다.