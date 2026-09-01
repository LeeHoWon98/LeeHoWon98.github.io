---
layout: post
title: "[AI Day 13] nn.Linear로 구현하는 PyTorch 선형회귀"
date: 2026-08-31
categories: [AI, PyTorch]
tags: [AI, PyTorch, LinearRegression, Linear, MSELoss, Optimizer]
---

# [AI 수학] Day 13 - nn.Linear로 구현하는 PyTorch 선형회귀

## 들어가며

지난 시간에는 여러 개의 데이터를 이용하여 선형회귀 모델을 직접 구현해보았다.

사용했던 모델은 다음과 같았다.

```text
ŷ = wx + b
```

그리고 Weight와 Bias도 직접 만들었다.

```python
w = torch.tensor(0.0, requires_grad=True)
b = torch.tensor(0.0, requires_grad=True)
```

예측 역시 직접 계산했다.

```python
y_pred = w * x + b
```

Loss도 MSE의 원리를 이용하여 직접 작성했다.

```python
loss = ((y_pred - y) ** 2).mean()
```

이번에는 이러한 기능들을 직접 구현하지 않고 PyTorch에서 제공하는 기능을 사용해보았다.

주요 기능은 다음과 같다.

```text
nn.Linear
nn.MSELoss
model.parameters()
torch.no_grad()
```

코드는 달라지지만 내부에서 일어나는 학습의 원리는 이전과 동일하다.

---

## 1. 직접 만들었던 선형 모델

지금까지 사용했던 선형 모델은

```text
ŷ = wx + b
```

였다.

여기서

```text
w = Weight
b = Bias
```

이다.

이전에는 두 값을 직접 생성했다.

```python
w = torch.tensor(0.0, requires_grad=True)
b = torch.tensor(0.0, requires_grad=True)
```

그리고 예측값을 다음과 같이 계산했다.

```python
y_pred = w * x + b
```

이 방법은 선형회귀가 내부적으로 어떻게 동작하는지 이해하는 데 도움이 된다.

하지만 실제 PyTorch에서는 이러한 모델을 더 편리하게 만들 수 있다.

---

## 2. nn.Linear

PyTorch에는 선형 모델을 만들어주는 `nn.Linear`가 있다.

먼저 다음과 같이 불러온다.

```python
import torch.nn as nn
```

그리고 모델을 생성한다.

```python
model = nn.Linear(1, 1)
```

기본적인 구조는 다음과 같다.

```text
nn.Linear(입력 개수, 출력 개수)
```

따라서

```python
nn.Linear(1, 1)
```

은

```text
입력 1개
↓
Linear
↓
출력 1개
```

를 의미한다.

우리가 사용하고 있는 데이터는 하나의 x를 받아 하나의 y를 예측하므로 `1, 1`을 사용한다.

---

## 3. Weight와 Bias는 어디에 있을까?

이전에는

```python
w = ...
b = ...
```

처럼 Weight와 Bias를 직접 만들었다.

하지만

```python
model = nn.Linear(1, 1)
```

을 생성하면 PyTorch가 내부적으로 Weight와 Bias를 자동으로 생성한다.

즉,

```text
nn.Linear(1, 1)

내부적으로

Weight
Bias
```

를 가지고 있다.

실제로 다음과 같이 확인할 수 있다.

```python
print(model.weight)
print(model.bias)
```

따라서 `nn.Linear`를 사용한다고 해서 Weight와 Bias가 사라진 것은 아니다.

단지 우리가 직접 만들지 않고 **모델 내부에서 관리하도록 바뀐 것**이다.

---

## 4. model(x)

이전에는 예측할 때 다음과 같이 작성했다.

```python
y_pred = w * x + b
```

`nn.Linear`를 사용하면 다음과 같이 작성할 수 있다.

```python
y_pred = model(x)
```

코드는 짧아졌지만 내부에서는 여전히

```text
입력
↓
Weight와 연산
↓
Bias 추가
↓
예측값
```

이라는 과정이 일어난다.

즉,

```python
y_pred = model(x)
```

는 우리가 직접 구현했던

```python
y_pred = w * x + b
```

와 같은 역할을 한다.

수학적인 원리가 바뀐 것이 아니라 **PyTorch가 계산을 대신 관리해주는 것**이다.

---

## 5. nn.MSELoss

지난 시간에는 MSE를 직접 계산했다.

```python
loss = ((y_pred - y) ** 2).mean()
```

이 코드는

```text
예측값 - 정답
↓
오차 제곱
↓
전체 평균
```

을 계산한다.

PyTorch에는 MSE를 계산하는 기능도 이미 존재한다.

```python
criterion = nn.MSELoss()
```

그리고 다음과 같이 사용한다.

```python
loss = criterion(y_pred, y)
```

여기서 `criterion`은 특별한 명령어가 아니라 단순한 변수 이름이다.

즉,

```python
criterion = nn.MSELoss()
```

은

```text
MSE를 계산할 Loss 함수를 준비한다.
```

라는 의미이다.

그리고

```python
loss = criterion(y_pred, y)
```

를 통해 예측값과 정답 사이의 MSE를 계산한다.

---

## 6. model.parameters()

이전에는 Optimizer를 다음과 같이 만들었다.

```python
optimizer = torch.optim.SGD([w, b], lr=0.01)
```

`[w, b]`를 전달한 이유는 Optimizer에게

```text
w와 b를 학습시켜라.
```

라고 알려주기 위해서였다.

하지만 `nn.Linear`에서는 Weight와 Bias가 `model` 내부에 존재한다.

따라서 다음과 같이 작성한다.

```python
optimizer = torch.optim.SGD(
    model.parameters(),
    lr=0.01
)
```

`model.parameters()`는 모델 내부에서 학습해야 하는 Parameter를 가져온다.

현재 모델에서는

```text
Weight
Bias
```

가 이에 해당한다.

따라서 개념적으로 보면

```text
이전

[w, b]

↓

현재

model.parameters()
```

라고 이해할 수 있다.

---

## 7. 학습 데이터의 Shape

이번에는 다음 데이터를 사용한다.

```text
x = 1 → y = 3
x = 2 → y = 5
x = 3 → y = 7
x = 4 → y = 9
```

실제 규칙은

```text
y = 2x + 1
```

이다.

Tensor는 다음과 같이 만든다.

```python
x = torch.tensor([
    [1.0],
    [2.0],
    [3.0],
    [4.0]
])

y = torch.tensor([
    [3.0],
    [5.0],
    [7.0],
    [9.0]
])
```

이전처럼

```text
[1, 2, 3, 4]
```

가 아니라

```text
[[1],
 [2],
 [3],
 [4]]
```

형태를 사용한다.

왜냐하면 각각의 데이터가 입력 특성 하나를 가지고 있기 때문이다.

현재 x의 Shape을 확인하면

```python
print(x.shape)
```

결과는

```text
torch.Size([4, 1])
```

이다.

즉,

```text
4개의 데이터
각 데이터마다 1개의 입력 Feature
```

라고 이해할 수 있다.

---

## 8. 학습 과정

이제 실제 학습 과정을 살펴보자.

```python
for epoch in range(1000):

    optimizer.zero_grad()

    y_pred = model(x)

    loss = criterion(y_pred, y)

    loss.backward()

    optimizer.step()
```

각 코드는 다음 역할을 한다.

### optimizer.zero_grad()

```text
이전 학습에서 계산한 Gradient 초기화
```

여기서 Weight와 Bias 자체가 초기화되는 것은 아니다.

```text
Weight, Bias
→ 학습된 값 유지

Gradient
→ 초기화
```

이다.

### model(x)

```text
현재 Weight와 Bias를 이용해 예측
```

내부적으로 선형 연산이 수행된다.

### criterion(y_pred, y)

```text
예측값과 정답의 MSE 계산
```

### loss.backward()

```text
Loss를 기준으로 Gradient 계산
```

즉, Weight와 Bias를 어느 방향으로 수정해야 하는지 계산한다.

### optimizer.step()

```text
계산된 Gradient를 이용해 Weight와 Bias 수정
```

이 과정이 반복되면서 모델이 학습된다.

---

## 9. Day 12와 비교

Day 12와 Day 13의 코드를 비교하면 다음과 같다.

```text
Day 12                      Day 13

w, b 직접 생성       →      nn.Linear

w * x + b            →      model(x)

MSE 직접 계산         →      nn.MSELoss()

[w, b]               →      model.parameters()

loss.backward()      →      loss.backward()

optimizer.step()     →      optimizer.step()
```

코드의 형태는 달라졌지만 실제 학습 원리는 동일하다.

결국 모델은

```text
예측
↓
Loss 계산
↓
Gradient 계산
↓
Weight와 Bias 수정
↓
다시 예측
```

과정을 반복한다.

---

## 10. 전체 코드

오늘 배운 내용을 하나의 코드로 작성하면 다음과 같다.

```python
import torch
import torch.nn as nn

# =========================
# 1. 학습 데이터
# =========================

x = torch.tensor([
    [1.0],
    [2.0],
    [3.0],
    [4.0]
])

y = torch.tensor([
    [3.0],
    [5.0],
    [7.0],
    [9.0]
])

# =========================
# 2. 선형 모델
# =========================

model = nn.Linear(1, 1)

# =========================
# 3. Loss 함수
# =========================

criterion = nn.MSELoss()

# =========================
# 4. Optimizer
# =========================

optimizer = torch.optim.SGD(
    model.parameters(),
    lr=0.01
)

# =========================
# 5. 학습
# =========================

for epoch in range(1000):

    # 이전 Gradient 초기화
    optimizer.zero_grad()

    # Forward
    y_pred = model(x)

    # Loss
    loss = criterion(y_pred, y)

    # Backward
    loss.backward()

    # Weight와 Bias 업데이트
    optimizer.step()

    if (epoch + 1) % 100 == 0:
        print(
            f"Epoch {epoch + 1} | "
            f"Loss: {loss.item():.6f}"
        )

# =========================
# 6. 학습 결과 확인
# =========================

print()
print("학습된 Weight:", model.weight.item())
print("학습된 Bias:", model.bias.item())

# =========================
# 7. 새로운 데이터 예측
# =========================

new_x = torch.tensor([[5.0]])

with torch.no_grad():
    prediction = model(new_x)

print("x=5 예측값:", prediction.item())
```

학습이 잘 이루어지면

```text
Weight ≈ 2
Bias ≈ 1
```

에 가까워진다.

그리고 `x=5`를 입력하면

```text
약 11
```

이라는 결과를 얻을 수 있다.

---

## 11. torch.no_grad()

학습이 끝난 후 새로운 데이터를 예측할 때는 다음과 같이 작성했다.

```python
with torch.no_grad():
    prediction = model(new_x)
```

학습 중에는 Gradient가 필요하다.

```text
예측
↓
Loss
↓
Gradient
↓
Weight 수정
```

하지만 학습이 끝나고 단순히 결과만 예측할 때는 Weight를 수정하지 않는다.

따라서 Gradient를 계산할 필요가 없다.

`torch.no_grad()`는

```text
현재 계산에서는 Gradient를 추적하지 않는다.
```

라는 의미이다.

이를 통해 불필요한 Gradient 계산을 줄일 수 있다.

---

## 12. 직접 구현하는 것과 PyTorch를 사용하는 것

이번 학습에서 가장 중요하게 느껴진 부분은 PyTorch가 새로운 학습 원리를 사용하는 것이 아니라는 점이다.

Day 12에서 직접 구현했던

```text
ŷ = wx + b
```

와

```text
MSE
Gradient
Optimizer
```

의 원리가 그대로 사용되고 있다.

PyTorch는 우리가 직접 작성했던 부분들을

```text
nn.Linear
nn.MSELoss
model.parameters()
```

와 같은 기능으로 편리하게 제공한다.

따라서 이러한 코드를 단순히 외우기보다는 내부적으로 어떤 계산이 이루어지는지 이해하는 것이 중요하다.

---

## 오늘 배운 내용 정리

오늘은 직접 구현했던 선형회귀를 PyTorch가 제공하는 기능으로 바꾸어보았다.

핵심 내용을 정리하면 다음과 같다.

```text
nn.Linear(1, 1)
→ Weight와 Bias를 가진 선형 모델

model(x)
→ 현재 Weight와 Bias를 이용한 예측

nn.MSELoss()
→ 평균 제곱 오차 계산

model.parameters()
→ 모델이 학습해야 하는 Parameter

loss.backward()
→ Gradient 계산

optimizer.step()
→ Parameter 수정

torch.no_grad()
→ Gradient 추적 없이 예측
```

특히

```text
직접 구현한 수학
↓
PyTorch가 제공하는 기능
```

으로 연결해서 이해하는 것이 중요했다.

---

## 마무리

Day 12까지는 선형회귀의 내부 동작을 이해하기 위해 Weight, Bias, MSE를 직접 만들었다.

이번에는 같은 학습 과정을 `nn.Linear`와 `nn.MSELoss`를 이용해 구현했다.

코드는 훨씬 간단해졌지만 내부적인 흐름은 여전히

```text
데이터
↓
모델
↓
예측
↓
Loss
↓
Backpropagation
↓
Optimizer
↓
Parameter 수정
```

으로 이어진다.

이번 학습을 통해 지금까지 공부했던 미분, Gradient, Backpropagation, Optimizer가 실제 PyTorch 코드에서 어떻게 사용되는지 조금 더 명확하게 연결할 수 있었다.