---
layout: post
title: "[AI Day 15] nn.Linear의 여러 출력과 뉴런, Shape 이해하기"
date: 2026-09-06
categories: [AI, PyTorch]
tags: [AI, PyTorch, Linear, NeuralNetwork, Neuron, Layer, Shape, Parameter]
---

# [AI 수학] Day 15 - nn.Linear의 여러 출력과 뉴런, Shape 이해하기

## 들어가며

지난 시간에는 입력 Feature가 여러 개인 선형 모델을 공부했다.

입력이 두 개이고 출력이 하나라면

```python
model = nn.Linear(2, 1)
```

을 사용했다.

내부 계산은 다음과 같다.

```text
ŷ = w₁x₁ + w₂x₂ + b
```

이번에는 여기서 한 단계 더 나아가 출력도 여러 개로 만들어보았다.

```python
model = nn.Linear(2, 3)
```

이를 통해 Weight와 Bias가 몇 개 필요한지 알아보고, `nn.Linear`가 뉴런과 Layer의 구조로 어떻게 연결되는지도 살펴보았다.

특히 처음 헷갈렸던

```text
입력 Shape [4, 2]
        ↓
nn.Linear(2, 3)
        ↓
출력 Shape [4, 3]
```

이 왜 성립하는지도 정리해보았다.

---

## 1. nn.Linear(2, 3)의 의미

`nn.Linear`의 기본적인 형태는 다음과 같다.

```text
nn.Linear(입력 Feature 수, 출력 Feature 수)
```

따라서

```python
model = nn.Linear(2, 3)
```

은

```text
입력 Feature = 2개
출력 Feature = 3개
```

라는 뜻이다.

즉, 하나의 데이터에 값 2개를 넣으면 결과로 값 3개를 만들어준다.

```text
[x₁, x₂]

    ↓

nn.Linear(2, 3)

    ↓

[y₁, y₂, y₃]
```

---

## 2. 출력 하나였을 때

먼저 이전에 사용했던

```python
nn.Linear(2, 1)
```

을 다시 생각해보자.

입력이

```text
x₁
x₂
```

두 개라면 하나의 출력은

```text
y = w₁x₁ + w₂x₂ + b
```

로 계산할 수 있다.

예를 들어

```text
x₁ = 2
x₂ = 3

w₁ = 4
w₂ = 5

b = 1
```

이라면

```text
y = 4×2 + 5×3 + 1
  = 8 + 15 + 1
  = 24
```

가 된다.

따라서 출력 하나를 만들기 위해서는

```text
Weight 2개
Bias 1개
```

가 필요하다.

---

## 3. 출력이 3개라면?

이번에는 출력이 세 개이다.

```text
y₁
y₂
y₃
```

각각의 출력은 서로 다른 계산 결과를 만들어야 한다.

따라서 각 출력마다 자신의 Weight와 Bias가 필요하다.

첫 번째 출력:

```text
y₁ = w₁₁x₁ + w₁₂x₂ + b₁
```

두 번째 출력:

```text
y₂ = w₂₁x₁ + w₂₂x₂ + b₂
```

세 번째 출력:

```text
y₃ = w₃₁x₁ + w₃₂x₂ + b₃
```

즉 하나의 입력

```text
[x₁, x₂]
```

에 대해 세 번의 서로 다른 선형 계산이 이루어진다.

---

## 4. Weight와 Bias는 몇 개일까?

`nn.Linear(2, 3)`에서는

```text
입력 = 2개
출력 = 3개
```

이다.

출력 하나를 만들 때 Weight가 2개 필요하다.

출력이 3개이므로

```text
Weight = 2 × 3
       = 6개
```

가 필요하다.

Bias는 출력마다 하나씩 필요하므로

```text
Bias = 3개
```

이다.

따라서 전체 학습 Parameter는

```text
Weight 6개
+
Bias 3개
=
총 9개
```

가 된다.

일반화하면

```text
nn.Linear(A, B)
```

의 Parameter 개수는

```text
Weight = A × B
Bias   = B

전체 Parameter = A×B + B
```

이다.

예를 들어

```python
nn.Linear(10, 5)
```

라면

```text
Weight = 10 × 5 = 50
Bias   = 5

전체 = 55개
```

의 Parameter를 가진다.

---

## 5. Weight의 Shape

다음 모델을 만들어보자.

```python
model = nn.Linear(2, 3)
```

그리고 Weight의 Shape을 확인한다.

```python
print(model.weight.shape)
```

결과는

```text
torch.Size([3, 2])
```

이다.

처음에는 조금 이상하게 느껴졌다.

모델은

```text
nn.Linear(2, 3)
```

인데 Weight Shape은

```text
[3, 2]
```

이기 때문이다.

PyTorch의 Linear Weight는

```text
[출력 Feature 수, 입력 Feature 수]
```

형태로 저장된다.

따라서

```text
nn.Linear(2, 3)

입력 = 2
출력 = 3
```

이면

```text
Weight Shape = [3, 2]
```

가 된다.

Weight를 구조적으로 보면 다음과 같다.

```text
[
    [w₁₁, w₁₂],
    [w₂₁, w₂₂],
    [w₃₁, w₃₂]
]
```

각 줄이 하나의 출력을 만들기 위한 Weight라고 생각할 수 있다.

---

## 6. Bias의 Shape

Bias도 확인할 수 있다.

```python
print(model.bias.shape)
```

결과는

```text
torch.Size([3])
```

이다.

출력이 3개이므로 각각의 출력에

```text
b₁
b₂
b₃
```

가 필요하기 때문이다.

따라서

```text
nn.Linear(2, 3)

Weight Shape = [3, 2]
Bias Shape   = [3]
```

이 된다.

---

## 7. 가장 헷갈렸던 Shape 이해하기

이번 학습에서 가장 헷갈렸던 부분은 다음 내용이었다.

```text
입력 Shape = [4, 2]

        ↓

nn.Linear(2, 3)

        ↓

출력 Shape = [4, 3]
```

왜 `[4, 3]`이 되는지 하나씩 살펴보자.

먼저 다음과 같은 입력 데이터가 있다고 하자.

```python
x = torch.tensor([
    [1.0, 2.0],
    [3.0, 4.0],
    [5.0, 6.0],
    [7.0, 8.0]
])
```

데이터를 나누어보면

```text
[1, 2] → 첫 번째 데이터
[3, 4] → 두 번째 데이터
[5, 6] → 세 번째 데이터
[7, 8] → 네 번째 데이터
```

이다.

따라서

```text
데이터 = 4개
각 데이터의 Feature = 2개
```

이다.

Shape은

```text
[4, 2]
 ↑  ↑
 │  └── 각 데이터의 Feature 개수
 │
 └───── 데이터 개수
```

가 된다.

---

## 8. nn.Linear가 바꾸는 것은 무엇일까?

모델은

```python
model = nn.Linear(2, 3)
```

이다.

이 모델은 **각각의 데이터에 들어 있는 값 2개를 받아 출력값 3개를 만든다.**

예를 들어 첫 번째 데이터

```text
[1, 2]
```

를 넣으면

```text
[1, 2]

   ↓

nn.Linear(2, 3)

   ↓

[출력1, 출력2, 출력3]
```

가 된다.

즉,

```text
2개의 Feature
↓
3개의 출력 Feature
```

로 변한다.

하지만 데이터 자체가 사라지거나 늘어나는 것은 아니다.

원래 데이터가 4개였으므로 네 개 모두 각각 변환된다.

```text
[1, 2] → [?, ?, ?]

[3, 4] → [?, ?, ?]

[5, 6] → [?, ?, ?]

[7, 8] → [?, ?, ?]
```

따라서 최종 결과는

```text
[
    [?, ?, ?],
    [?, ?, ?],
    [?, ?, ?],
    [?, ?, ?]
]
```

형태가 된다.

4개의 데이터가 있고 각 데이터마다 출력값이 3개이므로

```text
Shape = [4, 3]
```

이 되는 것이다.

---

## 9. [4, 2] → [4, 3]

이 부분을 한 번 더 정리하면 다음과 같다.

```text
입력

[
 [1, 2],
 [3, 4],
 [5, 6],
 [7, 8]
]

Shape = [4, 2]
         ↑  ↑
         │  └─ Feature 2개
         └──── 데이터 4개

           ↓

     nn.Linear(2, 3)

           ↓

출력

[
 [?, ?, ?],
 [?, ?, ?],
 [?, ?, ?],
 [?, ?, ?]
]

Shape = [4, 3]
         ↑  ↑
         │  └─ 출력 Feature 3개
         └──── 데이터 4개
```

여기서 중요한 것은 **4는 `nn.Linear`가 만든 숫자가 아니라는 것**이다.

원래 데이터가 4개였기 때문에 그대로 유지된다.

`nn.Linear(2, 3)`이 바꾸는 것은 각 데이터의 Feature 크기이다.

```text
각 데이터

[?, ?]

2개
 ↓

nn.Linear(2, 3)

 ↓

[?, ?, ?]

3개
```

따라서

```text
[4, 2]
   ↓
nn.Linear(2, 3)
   ↓
[4, 3]
```

이 된다.

---

## 10. Batch Size

여기서 Shape의 첫 번째 숫자

```text
[4, 2]
 ↑
 4
```

는 한 번에 처리하고 있는 데이터의 개수이다.

이러한 데이터 묶음을 **Batch**라고 하고, 데이터 개수를 **Batch Size**라고 한다.

현재는 데이터가 4개이므로

```text
Batch Size = 4
```

이다.

따라서 일반적으로 입력 Shape을

```text
[Batch Size, Feature 수]
```

라고 생각할 수 있다.

현재는

```text
[4, 2]
```

이고

```python
nn.Linear(2, 3)
```

을 통과하면

```text
[4, 3]
```

이 된다.

즉,

```text
[Batch Size, 입력 Feature]
             ↓
       nn.Linear(2, 3)
             ↓
[Batch Size, 출력 Feature]
```

가 된다.

---

## 11. 뉴런과 연결하기

`nn.Linear(2, 3)`을 다른 관점에서 생각해보면 출력 하나를 만드는 계산이 세 개 존재한다.

출력 하나의 계산은

```text
y = w₁x₁ + w₂x₂ + b
```

이다.

이를 하나의 뉴런처럼 생각할 수 있다.

따라서

```python
nn.Linear(2, 3)
```

은 간단하게

```text
입력 2개를 받는 뉴런 3개
```

가 있다고 생각할 수 있다.

구조를 나타내면

```text
        ┌─ 뉴런 1 → y₁
x₁ ─────┼─ 뉴런 2 → y₂
x₂ ─────┴─ 뉴런 3 → y₃
```

와 같은 형태가 된다.

각 뉴런은 자신만의 Weight와 Bias를 가진다.

---

## 12. Layer란?

여러 뉴런이 모여 있는 하나의 계산 단위를 **Layer(층)**라고 생각할 수 있다.

```python
layer = nn.Linear(2, 3)
```

은

```text
입력 2개

x₁  x₂
 ↓   ↓

┌─────────────┐
│ Linear Layer│
│             │
│ 뉴런 1      │
│ 뉴런 2      │
│ 뉴런 3      │
└─────────────┘

 ↓   ↓   ↓

y₁  y₂  y₃
```

와 같은 구조로 생각할 수 있다.

그리고 이러한 Layer를 여러 개 연결하면 신경망의 형태가 만들어지기 시작한다.

예를 들어

```python
layer1 = nn.Linear(2, 3)
layer2 = nn.Linear(3, 1)
```

이라면

```text
입력 2개
↓
Linear(2, 3)
↓
중간 출력 3개
↓
Linear(3, 1)
↓
최종 출력 1개
```

와 같은 구조가 된다.

---

## 13. 실제 코드로 Shape 확인하기

오늘 배운 내용을 직접 확인하기 위한 코드는 다음과 같다.

```python
import torch
import torch.nn as nn

# =========================
# 1. Linear Layer
# =========================

model = nn.Linear(2, 3)

# =========================
# 2. Weight와 Bias
# =========================

print("===== Weight =====")
print(model.weight)

print("\nWeight Shape:")
print(model.weight.shape)

print("\n===== Bias =====")
print(model.bias)

print("\nBias Shape:")
print(model.bias.shape)

# =========================
# 3. 입력 데이터
# =========================

x = torch.tensor([
    [1.0, 2.0],
    [3.0, 4.0],
    [5.0, 6.0],
    [7.0, 8.0]
])

print("\n===== Input =====")
print(x)

print("\nInput Shape:")
print(x.shape)

# =========================
# 4. Forward
# =========================

y = model(x)

print("\n===== Output =====")
print(y)

print("\nOutput Shape:")
print(y.shape)

# =========================
# 5. Parameter 개수
# =========================

weight_count = model.weight.numel()
bias_count = model.bias.numel()

print("\n===== Parameter =====")
print("Weight 개수:", weight_count)
print("Bias 개수:", bias_count)
print("전체 Parameter 개수:", weight_count + bias_count)
```

실행하면 실제 Weight와 Bias의 숫자는 랜덤하게 초기화되기 때문에 달라질 수 있다.

하지만 Shape은 다음과 같다.

```text
Weight Shape = [3, 2]

Bias Shape = [3]

Input Shape = [4, 2]

Output Shape = [4, 3]
```

Parameter 개수는

```text
Weight = 6개
Bias = 3개

전체 = 9개
```

이다.

---

## 오늘 배운 내용 정리

오늘은 `nn.Linear`에서 출력 Feature가 여러 개인 경우를 공부했다.

가장 중요한 내용은 다음과 같다.

```text
nn.Linear(2, 3)

입력 Feature = 2
출력 Feature = 3
```

출력 하나마다 입력 Feature 각각에 대한 Weight가 필요하기 때문에

```text
Weight = 2 × 3 = 6개
Bias = 3개

전체 Parameter = 9개
```

가 된다.

PyTorch에서 Weight Shape은

```text
[출력 Feature, 입력 Feature]
```

순서이므로

```text
nn.Linear(2, 3)

↓

Weight Shape = [3, 2]
```

이다.

그리고 데이터가 4개라면

```text
입력 Shape

[4, 2]
 ↑  ↑
 │  └─ 입력 Feature
 └──── Batch Size

       ↓

nn.Linear(2, 3)

       ↓

출력 Shape

[4, 3]
 ↑  ↑
 │  └─ 출력 Feature
 └──── Batch Size
```

가 된다.

`nn.Linear`는 데이터의 개수를 바꾸는 것이 아니라 **각 데이터의 Feature를 변환한다**는 점이 오늘 가장 중요했다.

---

## 마무리

처음에는

```text
nn.Linear(2, 3)
```

이라는 코드가 단순히 숫자 두 개를 전달하는 것처럼 보였다.

하지만 내부를 살펴보면

```text
입력 Feature
↓
Weight와 Bias
↓
여러 개의 출력
↓
뉴런
↓
Layer
```

라는 구조로 이어진다는 것을 알 수 있었다.

특히 Tensor의 Shape도 단순히 외우기보다는

```text
[데이터 개수, Feature 개수]
```

라고 해석하면 이해하기 쉽다.

현재까지의 흐름을 정리하면

```text
미분
↓
편미분
↓
Gradient
↓
Gradient Descent
↓
Backpropagation
↓
Autograd
↓
Weight / Bias
↓
선형회귀
↓
nn.Linear
↓
여러 Feature
↓
여러 출력
↓
뉴런과 Layer
```

까지 연결되었다.

다음에는 Linear Layer를 여러 개 연결했을 때 발생하는 문제와 이를 해결하기 위해 사용되는 **활성화 함수(Activation Function)**에 대해 공부해볼 예정이다.