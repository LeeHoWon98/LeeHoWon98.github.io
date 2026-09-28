---
layout: post
title: "[AI Day 16] 활성화 함수(ReLU)와 다층 신경망 이해하기"
date: 2026-09-27
categories: [AI, PyTorch]
tags: [AI, DeepLearning, PyTorch, ReLU, Activation, NeuralNetwork]
---

# Day 16. 활성화 함수(ReLU)와 다층 신경망

지난 시간에는 `nn.Linear()`를 이용해 여러 개의 입력 Feature를 여러 개의 출력 Feature로 변환하는 방법을 공부했다.

오늘은 Linear Layer를 여러 개 연결하는 방법과 활성화 함수가 필요한 이유를 알아보았다.

이번 학습의 핵심은 다음과 같다.

1. Linear Layer만 여러 개 연결하면 발생하는 문제
2. 활성화 함수가 필요한 이유
3. ReLU의 동작 원리와 미분
4. PyTorch를 이용한 다층 신경망 구현
5. 비선형 데이터 학습

---

## 1. Linear Layer 복습

PyTorch에서 Linear Layer는 다음과 같이 정의한다.

```python
import torch
import torch.nn as nn

layer = nn.Linear(2, 3)
```

여기서 각각의 숫자는 다음을 의미한다.

- 입력 Feature: 2개
- 출력 Feature: 3개

예를 들어 입력 데이터가 다음과 같다고 가정하자.

```python
x = torch.tensor([
    [1.0, 2.0],
    [3.0, 4.0],
    [5.0, 6.0],
    [7.0, 8.0]
])

print(x.shape)
```

실행 결과:

```text
torch.Size([4, 2])
```

이는 데이터가 4개이고 각 데이터가 2개의 Feature를 가지고 있다는 의미다.

Linear Layer를 통과시키면 다음과 같다.

```python
y = layer(x)

print(y.shape)
```

실행 결과:

```text
torch.Size([4, 3])
```

입력과 출력의 Shape은 다음과 같이 변한다.

```text
입력: [4, 2]
       ↓
nn.Linear(2, 3)
       ↓
출력: [4, 3]
```

여기서 중요한 사실은 Batch Size가 유지된다는 것이다.

Linear Layer는 각 데이터의 Feature를 변환하지만 데이터의 개수 자체를 변경하지 않는다.

---

## 2. Linear Layer만 여러 개 연결하면?

신경망을 구성할 때 Linear Layer를 여러 개 연결하면 복잡한 문제를 해결할 수 있을 것이라고 생각했다.

하지만 Linear Layer만 연결하면 한계가 존재한다.

다음 예제를 살펴보자.

```text
첫 번째 Layer

h = 2x + 1

두 번째 Layer

y = 3h + 2
```

첫 번째 Layer의 결과를 두 번째 Layer에 대입하면 다음과 같다.

```text
y = 3(2x + 1) + 2

y = 6x + 3 + 2

y = 6x + 5
```

결국 두 개의 Layer를 사용했지만 하나의 식으로 표현할 수 있다.

```text
y = 6x + 5
```

이는 여러 개의 Linear Layer를 연결해도 중간에 비선형 연산이 없다면 하나의 Linear Layer와 동등한 형태로 표현할 수 있다는 의미다.

정확히 말하면 PyTorch의 `nn.Linear()`는 Weight와 Bias를 사용하는 아핀 변환이다.

이러한 구조만으로는 복잡한 비선형 관계를 표현하는 데 한계가 있다.

이를 해결하기 위해 활성화 함수를 사용한다.

---

## 3. 활성화 함수란?

활성화 함수(Activation Function)는 신경망에 비선형성을 추가하는 함수다.

일반적인 뉴런의 계산 과정은 다음과 같다.

```text
입력
 ↓
Weight와 Bias 계산
 ↓
활성화 함수
 ↓
출력
```

수식으로 표현하면 다음과 같다.

```text
z = wx + b

y = f(z)
```

여기서 `f()`가 활성화 함수다.

활성화 함수를 사용하면 단순한 선형 관계뿐만 아니라 복잡한 비선형 관계도 학습할 수 있다.

대표적인 활성화 함수에는 ReLU, Sigmoid, Tanh 등이 있다.

이번 시간에는 가장 기본적으로 사용되는 ReLU를 공부했다.

---

## 4. ReLU란?

ReLU(Rectified Linear Unit)는 입력값이 음수이면 0을 출력하고, 양수이면 입력값을 그대로 출력하는 활성화 함수다.

수식은 다음과 같다.

```text
ReLU(x) = max(0, x)
```

예를 들어 다음과 같은 입력이 있다고 가정하자.

```text
입력: [-3, -1, 0, 2, 5]
```

ReLU를 적용하면 다음과 같다.

```text
출력: [0, 0, 0, 2, 5]
```

음수는 모두 0으로 변환되고 양수는 그대로 유지된다.

이를 PyTorch로 구현하면 다음과 같다.

```python
import torch
import torch.nn as nn

relu = nn.ReLU()

x = torch.tensor([
    -3.0,
    -1.0,
    0.0,
    2.0,
    5.0
])

y = relu(x)

print(y)
```

실행 결과:

```text
tensor([0., 0., 0., 2., 5.])
```

ReLU는 계산이 단순하고 여러 신경망에서 널리 사용된다.

---

## 5. ReLU와 미분

ReLU 역시 역전파 과정에서 미분이 필요하다.

ReLU의 미분값은 입력 구간에 따라 달라진다.

```text
x < 0  → 미분값 0

x > 0  → 미분값 1
```

입력이 음수인 구간에서는 ReLU의 출력이 항상 0이므로 기울기도 0이다.

반면 입력이 양수인 구간에서는 입력값을 그대로 출력하므로 기울기가 1이다.

입력이 정확히 0인 지점에서는 수학적으로 미분이 정의되지 않는다.

PyTorch는 해당 지점의 Gradient를 0으로 처리한다.

역전파에서는 체인룰에 따라 Gradient를 계산한다.

```text
이전 Layer에서 전달된 Gradient
              ↓
        ReLU의 미분값
              ↓
             곱셈
              ↓
       다음 Gradient
```

따라서 ReLU의 입력이 음수이면 해당 경로의 Gradient가 0이 된다.

이러한 특성 때문에 일부 뉴런이 계속 음수만 출력하면 학습이 어려워지는 Dying ReLU 문제가 발생할 수 있다.

---

## 6. Linear Layer와 ReLU 연결하기

이제 Linear Layer 사이에 ReLU를 추가해 보자.

```python
import torch
import torch.nn as nn

layer1 = nn.Linear(2, 3)

relu = nn.ReLU()

layer2 = nn.Linear(3, 1)

x = torch.tensor([
    [1.0, 2.0],
    [3.0, 4.0],
    [5.0, 6.0],
    [7.0, 8.0]
])

hidden = layer1(x)

activated = relu(hidden)

output = layer2(activated)

print("입력:", x.shape)
print("첫 번째 Layer:", hidden.shape)
print("ReLU:", activated.shape)
print("최종 출력:", output.shape)
```

실행 결과:

```text
입력: torch.Size([4, 2])
첫 번째 Layer: torch.Size([4, 3])
ReLU: torch.Size([4, 3])
최종 출력: torch.Size([4, 1])
```

전체 구조는 다음과 같다.

```text
입력
[4, 2]
   ↓
Linear(2, 3)
   ↓
[4, 3]
   ↓
ReLU
   ↓
[4, 3]
   ↓
Linear(3, 1)
   ↓
[4, 1]
```

여기서 ReLU는 Tensor의 Shape을 변경하지 않는다.

입력값에 비선형 연산을 적용할 뿐이다.

---

## 7. nn.Sequential 사용하기

PyTorch에서는 `nn.Sequential()`을 이용해 여러 Layer를 간단하게 연결할 수 있다.

앞에서 작성했던 신경망을 다음과 같이 표현할 수 있다.

```python
import torch
import torch.nn as nn

model = nn.Sequential(
    nn.Linear(2, 3),
    nn.ReLU(),
    nn.Linear(3, 1)
)

x = torch.tensor([
    [1.0, 2.0],
    [3.0, 4.0],
    [5.0, 6.0],
    [7.0, 8.0]
])

output = model(x)

print(output)
print(output.shape)
```

실행 결과의 Shape은 다음과 같다.

```text
torch.Size([4, 1])
```

`nn.Sequential()`은 등록한 Layer를 순서대로 실행한다.

따라서 신경망의 구조를 간결하게 작성할 수 있다.

---

## 8. 비선형 데이터 학습하기

이번에는 ReLU를 사용하는 신경망으로 비선형 관계를 학습해 보았다.

학습할 함수는 다음과 같다.

```text
y = x1² + x2²
```

이 함수는 입력값의 제곱을 사용하므로 비선형 관계를 가진다.

단순한 Linear Layer만으로는 이러한 관계를 정확하게 표현하기 어렵다.

따라서 은닉층과 ReLU를 사용하는 신경망을 구성했다.

### 전체 코드

```python
import torch
import torch.nn as nn

# 재현 가능한 실험을 위한 시드 설정
torch.manual_seed(42)

# 학습 데이터 생성
x = torch.rand(200, 2) * 2 - 1

# 정답 생성
y = x[:, 0:1] ** 2 + x[:, 1:2] ** 2

# 신경망 구성
model = nn.Sequential(
    nn.Linear(2, 32),
    nn.ReLU(),
    nn.Linear(32, 1)
)

# 손실 함수
criterion = nn.MSELoss()

# Optimizer
optimizer = torch.optim.Adam(
    model.parameters(),
    lr=0.01
)

# 학습
for epoch in range(1000):

    optimizer.zero_grad()

    prediction = model(x)

    loss = criterion(prediction, y)

    loss.backward()

    optimizer.step()

    if (epoch + 1) % 100 == 0:
        print(
            f"Epoch {epoch + 1} | "
            f"Loss: {loss.item():.6f}"
        )

# 새로운 데이터 예측
new_x = torch.tensor([
    [0.5, 0.5]
])

model.eval()

with torch.no_grad():
    result = model(new_x)

print("예측값:", result.item())
print("실제값:", 0.5)
```

### 코드 분석

**1. 데이터 생성**

```python
x = torch.rand(200, 2) * 2 - 1
```

200개의 데이터를 생성한다.

각 데이터는 2개의 Feature를 가지며, 값은 0 이상 1 미만에서 생성된 후 -1 이상 1 미만 범위로 변환된다.

**2. 정답 생성**

```python
y = x[:, 0:1] ** 2 + x[:, 1:2] ** 2
```

각 데이터의 첫 번째 Feature와 두 번째 Feature를 제곱한 후 더한다.

**3. 신경망 구성**

```python
model = nn.Sequential(
    nn.Linear(2, 32),
    nn.ReLU(),
    nn.Linear(32, 1)
)
```

입력 Feature 2개를 32개의 은닉 뉴런으로 변환한다.

이후 ReLU를 적용하고 최종적으로 출력값 1개를 계산한다.

**4. Loss 계산**

```python
criterion = nn.MSELoss()
```

예측값과 실제값의 평균제곱오차를 계산한다.

**5. 역전파**

```python
loss.backward()
```

체인룰을 이용한 자동 미분으로 각 Parameter의 Gradient를 계산한다.

**6. Parameter 수정**

```python
optimizer.step()
```

계산한 Gradient를 이용해 Weight와 Bias를 수정한다.

이번에는 기존에 사용했던 SGD 대신 Adam Optimizer를 사용했다.

Adam 역시 Gradient를 이용해 Parameter를 수정하는 최적화 알고리즘이다.

### 예측 결과

새로운 입력값이 다음과 같다고 가정하자.

```text
x1 = 0.5
x2 = 0.5
```

실제 정답은 다음과 같다.

```text
y = 0.5² + 0.5²

y = 0.25 + 0.25

y = 0.5
```

학습이 진행되면 모델은 0.5에 가까운 값을 예측하도록 훈련된다.

단, 신경망의 예측값은 근삿값이므로 정확히 0.5가 출력된다는 보장은 없다.

---

## 9. 오늘의 핵심 정리

오늘은 활성화 함수와 다층 신경망을 공부했다.

가장 중요한 내용은 다음과 같다.

1. Linear Layer만 여러 개 연결해도 하나의 아핀 변환으로 합칠 수 있다.
2. 활성화 함수는 신경망에 비선형성을 추가한다.
3. ReLU는 음수를 0으로 만들고 양수를 그대로 유지한다.
4. ReLU는 Tensor의 Shape을 변경하지 않는다.
5. ReLU 역시 역전파 과정에서 미분이 필요하다.
6. `nn.Sequential()`을 이용하면 여러 Layer를 간단하게 연결할 수 있다.

특히 이번 학습을 통해 Linear Layer와 활성화 함수를 함께 사용해야 복잡한 비선형 관계를 표현할 수 있다는 점을 이해했다.

또한 이전에 공부했던 체인룰과 역전파가 실제 신경망 학습에서 어떻게 활용되는지도 확인할 수 있었다.

다음 학습에서는 이러한 구조를 바탕으로 다층 신경망의 학습 과정을 더욱 자세히 살펴볼 예정이다.