---
title: "파이썬 리스트(List) 정리: 기본 기능부터 Rotation(회전)까지"
date: 2026-06-25 01:00:00 +0900
categories: [Python, Data Structure]
tags: [python, list, rotation, deque, algorithm]
---

![Python List Features and Rotation](/assets/img/post/python.png){: width="700" .shadow .rounded-10 }

## 들어가며

오늘은 파이썬 리스트(list) 자료형에 대해서 정리를 해보고자 한다. 코딩 테스트는 물론 현업에서도 매우 유용하게 쓰이는 기능중에 하나이다.

파이썬에서 리스트(`list`)는 여러 데이터를 순서대로 저장하고 처리할 때 가장 자주 사용하는 자료형이다.

숫자, 문자열, 불리언처럼 서로 다른 자료형을 함께 저장할 수 있고, 데이터를 추가 · 삭제 · 정렬 · 회전하는 작업도 비교적 간단하게 처리할 수 있다.

> 이 글에서는 리스트의 기본 기능을 정리한 뒤, 알고리즘 문제에서 자주 등장하는 **Rotation(회전)** 구현까지 살펴본다.

---

## 1. 리스트(list)란?

리스트는 여러 개의 값을 순서대로 저장하는 자료형이다. 대괄호 `[]`를 사용해 만들 수 있다.

```python
numbers = [1, 2, 3, 4, 5]
names = ["Kim", "Lee", "Park"]
mixed = [1, "hello", 3.14, True]
empty = []
```

리스트의 주요 특징은 다음과 같다.

| 특징 | 설명 |
|---|---|
| 순서 존재 | 저장한 순서대로 값이 유지된다. |
| 인덱스 사용 | `0`부터 시작하는 번호로 값에 접근한다. |
| 수정 가능 | 생성한 뒤에도 값을 변경할 수 있다. |
| 크기 변경 가능 | 값을 추가하거나 삭제할 수 있다. |
| 중복 허용 | 같은 값을 여러 개 저장할 수 있다. |

---

## 2. 인덱싱과 슬라이싱

### 2.1 인덱싱 : 리스트 접근, 탐색

리스트는 `0`부터 시작하는 인덱스를 사용한다.

```python
numbers = [10, 20, 30, 40, 50]

print(numbers[0])
print(numbers[2])
print(numbers[-1])
```

출력 결과:

```text
10
30
50
```

반대로 음수를 넣게되면 리스트 뒤에서부터 값을 가져온다.

| 표현식 | 의미 | 결과 |
|---|---|---|
| `numbers[0]` | 첫 번째 값 | `10` |
| `numbers[2]` | 세 번째 값 | `30` |
| `numbers[-1]` | 마지막 값 | `50` |
| `numbers[-2]` | 뒤에서 두 번째 값 | `40` |

### 2.2 슬라이싱 : 리스트 자르기

슬라이싱은 리스트의 일부를 잘라서 가져오는 기능이다.

```python
numbers = [10, 20, 30, 40, 50]

print(numbers[1:4])
print(numbers[:3])
print(numbers[2:])
print(numbers[::-1])
```

출력 결과:

```text
[20, 30, 40]
[10, 20, 30]
[30, 40, 50]
[50, 40, 30, 20, 10]
```

슬라이싱의 기본 형식은 다음과 같다.

```python
리스트[시작:끝:간격]
```

> 끝 인덱스는 포함되지 않는다. 따라서 `numbers[1:4]`는 인덱스 `1`, `2`, `3`의 값을 가져온다.(리스트[시작 index : 끝 index + 1])

---

## 3. 값 추가하기

리스트에 값을 추가할 때는 주로 `append()`, `insert()`, `extend()`를 사용한다.

### 3.1 `append()`

`append()`는 리스트의 맨 뒤에 값 하나를 추가한다.

```python
numbers = [1, 2, 3]

numbers.append(4)

print(numbers)
```

```text
[1, 2, 3, 4]
```

### 3.2 `insert()`

`insert(인덱스, 값)`은 원하는 위치에 값을 삽입한다.

```python
numbers = [1, 2, 3]

numbers.insert(1, 100)

print(numbers)
```

```text
[1, 100, 2, 3]
```

### 3.3 `extend()`

`extend()`는 여러 값을 한 번에 추가한다. 리스트들을 연결하는것 또한 가능하다.

```python
numbers = [1, 2, 3]

numbers.extend([4, 5, 6])

print(numbers)
```

```text
[1, 2, 3, 4, 5, 6]
```

혹은

```python
numbers1 = [1, 2, 3]
numbers2 = [4, 5, 6]

numbers1.extend(numbers2)

print(numbers1)
```

```text
[1, 2, 3, 4, 5, 6]
```

### `append()`와 `extend()` 차이

```python
numbers = [1, 2, 3]

numbers.append([4, 5])

print(numbers)
```

```text
[1, 2, 3, [4, 5]]
```

`append()`는 `[4, 5]`라는 리스트 자체를 하나의 원소로 추가한다.

```python
numbers = [1, 2, 3]

numbers.extend([4, 5])

print(numbers)
```

```text
[1, 2, 3, 4, 5]
```

`extend()`는 전달한 리스트의 원소를 하나씩 추가한다.

---

## 4. 값 삭제하기

### 4.1 `remove()`

`remove(값)`은 특정 값을 삭제한다.

```python
numbers = [10, 20, 30, 20]

numbers.remove(20)

print(numbers)
```

```text
[10, 30, 20]
```

같은 값이 여러 개라면 가장 앞에 있는 값 하나만 삭제한다.

### 4.2 `pop()`

`pop()`은 값을 삭제하면서 삭제한 값을 반환한다.

```python
numbers = [10, 20, 30]

removed = numbers.pop(1)

print(removed)
print(numbers)
```

```text
20
[10, 30]
```

인덱스를 생략하면 마지막 값을 삭제한다.

```python
numbers = [10, 20, 30]

numbers.pop()

print(numbers)
```

```text
[10, 20]
```

### 4.3 `del`과 `clear()`

```python
numbers = [10, 20, 30]

del numbers[1]

print(numbers)
```

```text
[10, 30]
```

```python
numbers = [1, 2, 3]

numbers.clear()

print(numbers)
```

```text
[]
```

---

## 5. 자주 사용하는 리스트 메서드

```python
numbers = [3, 1, 4, 1, 5, 1]
```

| 메서드 | 설명 | 예시 |
|---|---|---|
| `len(numbers)` | 리스트 길이 반환 | `6` |
| `numbers.count(1)` | 특정 값 개수 반환 | `3` |
| `numbers.index(4)` | 특정 값의 위치 반환 | `2` |
| `numbers.sort()` | 원본 리스트 오름차순 정렬 | `[1, 1, 1, 3, 4, 5]` |
| `numbers.reverse()` | 원본 리스트 순서 뒤집기 | 역순 리스트 |
| `numbers.copy()` | 독립적인 복사본 생성 | 새 리스트 반환 |

### `sort()`와 `sorted()` 차이

```python
numbers = [5, 1, 4, 2, 3]

numbers.sort()

print(numbers)
```

```text
[1, 2, 3, 4, 5]
```

`sort()`는 원본 리스트를 직접 바꾼다.

```python
numbers = [5, 1, 4, 2, 3]

new_numbers = sorted(numbers)

print(numbers)
print(new_numbers)
```

```text
[5, 1, 4, 2, 3]
[1, 2, 3, 4, 5]
```

`sorted()`는 원본을 유지하고 정렬된 새 리스트를 반환한다.

---

## 6. 리스트 복사와 주의점

다음 코드는 복사가 아니라 같은 리스트를 함께 참조하는 코드다.

```python
a = [1, 2, 3]
b = a

b[0] = 100

print(a)
print(b)
```

```text
[100, 2, 3]
[100, 2, 3]
```

독립적인 하나의 리스트로 만들려면 `copy()` 또는 슬라이싱을 사용한다.

```python
a = [1, 2, 3]
b = a.copy()

b[0] = 100

print(a)
print(b)
```

```text
[1, 2, 3]
[100, 2, 3]
```

---

## 7. 리스트 컴프리헨션

리스트 컴프리헨션은 반복문을 짧고 간결하게 작성하는 방법이다.

```python
numbers = [1, 2, 3, 4, 5]

squares = [x * x for x in numbers]

print(squares)
```

```text
[1, 4, 9, 16, 25]
```

조건을 추가하면 원하는 값만 새 리스트에 저장할 수 있다.

```python
numbers = [1, 2, 3, 4, 5, 6]

even_numbers = [x for x in numbers if x % 2 == 0]

print(even_numbers)
```

```text
[2, 4, 6]
```

리스트 컴프리헨션은 짧고 간결하게 보일 수 있으나 구조가 복잡해진다면 그만큼 가독성이 떨어질 수 있는 단점도 존재한다.

---

## 8. Rotation(회전)이란?

Rotation은 리스트의 원소를 왼쪽 또는 오른쪽으로 일정 칸수만큼 이동시키는 작업이다.

```text
원본: [1, 2, 3, 4, 5]

왼쪽 2칸 회전: [3, 4, 5, 1, 2]

오른쪽 2칸 회전: [4, 5, 1, 2, 3]
```

Rotation은 원형 큐, 배열 이동, 문자열 이동, 시뮬레이션 문제에서 자주 사용된다.

Rotation에서 가장 효율적인 방법은 collections 모듈의 deque를 사용하는 것이지만 이번에는 리스트 자료형만 가지고 구현해보자.

---

## 9. 왼쪽 Rotation 구현

왼쪽으로 `k`칸 회전하려면 앞쪽 `k`개를 뒤로 보내면 된다.

```python
numbers = [1, 2, 3, 4, 5]
k = 2

result = numbers[k:] + numbers[:k]

print(result)
```

```text
[3, 4, 5, 1, 2]
```

동작을 나누어 보면 다음과 같다.

```python
numbers[k:]   # [3, 4, 5]
numbers[:k]   # [1, 2]
```

`numbers[k:] + numbers[:k]`의 결과는 다음과 같다.

```text
[3, 4, 5, 1, 2]
```

### `k`가 리스트 길이보다 큰 경우

`k`가 리스트 길이보다 클 수 있으므로 `%` 연산자로 줄여 주는 것이 안전하다.

```python
numbers = [1, 2, 3, 4, 5]
k = 7

k = k % len(numbers)

result = numbers[k:] + numbers[:k]

print(result)
```

```text
[3, 4, 5, 1, 2]
```

`7 % 5`의 결과가 `2`이기 때문에 왼쪽으로 2칸 회전한 결과와 같다.

---

## 10. 오른쪽 Rotation 구현

오른쪽으로 `k`칸 회전하려면 뒤쪽 `k`개를 앞으로 가져오면 된다.

```python
numbers = [1, 2, 3, 4, 5]
k = 2

result = numbers[-k:] + numbers[:-k]

print(result)
```

```text
[4, 5, 1, 2, 3]
```

동작을 나누어 보면 다음과 같다.

```python
numbers[-k:]  # [4, 5]
numbers[:-k]  # [1, 2, 3]
```

`numbers[-k:] + numbers[:-k]`의 결과는 다음과 같다.

```text
[4, 5, 1, 2, 3]
```

---

## 11. `deque`를 이용한 Rotation

리스트 슬라이싱으로 회전을 구현할 수 있지만, 앞과 뒤에서 데이터를 자주 추가 · 삭제하거나 회전해야 한다면 `deque`가 더 적합하다.

```python
from collections import deque

numbers = deque([1, 2, 3, 4, 5])

numbers.rotate(2)

print(numbers)
```

```text
deque([4, 5, 1, 2, 3])
```

`rotate(양수)`는 오른쪽 회전이다.

```python
from collections import deque

numbers = deque([1, 2, 3, 4, 5])

numbers.rotate(-2)

print(numbers)
```

```text
deque([3, 4, 5, 1, 2])
```

`rotate(음수)`는 왼쪽 회전이다.

| 코드 | 동작 |
|---|---|
| `numbers.rotate(2)` | 오른쪽으로 2칸 회전 |
| `numbers.rotate(-2)` | 왼쪽으로 2칸 회전 |

---

## 12. 리스트와 `deque` 사용 기준

| 상황 | 추천 |
|---|---|
| 인덱싱, 슬라이싱, 일반적인 데이터 처리 | `list` |
| 리스트 끝에 값 추가·삭제 | `list` |
| 앞쪽에서 값 추가·삭제가 자주 발생 | `deque` |
| 회전 작업을 자주 수행 | `deque` |

---

## 마무리

리스트는 파이썬에서 가장 기본적이면서 활용도가 높은 자료형이다.

인덱싱과 슬라이싱으로 원하는 값을 가져올 수 있고, `append()`, `remove()`, `sort()` 같은 메서드로 데이터를 편리하게 관리할 수 있다.

특히 Rotation은 배열과 큐 관련 문제에서 자주 등장한다. 간단한 회전은 슬라이싱으로 구현하고, 회전이나 양쪽 끝 연산이 반복된다면 `deque.rotate()`를 사용하는 것이 효율적이다.