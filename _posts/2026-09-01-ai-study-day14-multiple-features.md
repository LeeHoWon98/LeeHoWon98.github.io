---
layout: post
title: "[AI Day 14] 여러 Feature와 nn.Linear 이해하기"
date: 2026-09-01
categories: [AI, PyTorch]
tags: [AI, PyTorch, Linear, Feature, Weight, Tensor, Gradient]
---

# [AI 수학] Day 14 - 여러 Feature와 nn.Linear 이해하기

## 들어가며

지난 시간에는 PyTorch의 `nn.Linear`를 이용하여 선형회귀를 구현했다.

```python
model = nn.Linear(1, 1)
```

이 모델은 하나의 입력값을 받아 하나의 결과를 출력한다.

수학적으로는 다음과 같은 형태였다.

```text
ŷ = wx + b
```

이번에는 여기서 한 단계 확장하여 **입력 Feature가 여러 개인 경우**를 공부했다.

입력이 여러 개가 되면 Weight는 어떻게 변하는지, Tensor의 Shape은 어떻게 구성되는지, 그리고 `nn.Linear`의 숫자가 무엇을 의미하는지 알아보았다.

---

## 1. Feature란?

지금까지는 하나의 입력값 `x`를 사용했다.

예를 들어

```text
x = 1
x = 2
x = 3
```

처럼 하나의 정보를 이용해 결과를 예측했다.

하지만 실제 문제에서는 하나의 정보만 사용하는 경우보다 여러 정보를 함께 사용하는 경우가 많다.

예를 들어 집값을 예측한다고 생각해보자.

```text
집 크기
방 개수
건물 연식
역과의 거리
```

등 여러 정보를 사용할 수 있다.

이처럼 모델이 예측을 위해 사용하는 각각의 입력 정보를 **Feature(특성)**라고 한다.

예를 들어

```text
집 크기 = 80
방 개수 = 3
```

이라면 하나의 데이터를

```text
[80, 3]
```

처럼 표현할 수 있다.

이 데이터는 Feature가 2개인 데이터이다.

---

## 2. Feature가 여러 개라면 Weight도 여러 개 필요하다

입력 Feature가 하나일 때 모델은

```text
ŷ = wx + b
```

였다.

하지만 입력이

```text
x₁
x₂
```

두 개라면 각각의 입력이 결과에 미치는 영향이 다를 수 있다.

따라서 Weight도 각각 필요하다.

```text
x₁ → w₁

x₂ → w₂
```

모델은 다음과 같이 확장된다.

```text
ŷ = w₁x₁ + w₂x₂ + b
```

여기서

```text
w₁
→ x₁이 결과에 미치는 영향

w₂
→ x₂가 결과에 미치는 영향

b
→ 전체적인 기준점을 조정
```

한다고 이해할 수 있다.

즉, **각 Feature에 대응하는 Weight가 존재한다.**

---

## 3. 숫자를 이용해서 계산해보기

다음과 같은 값이 있다고 해보자.

```text
x₁ = 2
x₂ = 3

w₁ = 4
w₂ = 5

b = 1
```

모델은

```text
ŷ = w₁x₁ + w₂x₂ + b
```

이므로 값을 넣으면

```text
ŷ = (4 × 2) + (5 × 3) + 1
```

이다.

계산하면

```text
8 + 15 + 1
= 24
```

따라서

```text
ŷ = 24
```

가 된다.

입력이 여러 개가 되더라도 각각의 입력에 자신의 Weight를 곱하고 마지막에 Bias를 더한다는 기본적인 원리는 동일하다.

---

## 4. nn.Linear(2, 1)

지난 시간에는

```python
model = nn.Linear(1, 1)
```

을 사용했다.

`nn.Linear`의 기본 구조는 다음과 같다.

```text
nn.Linear(입력 Feature 개수, 출력 Feature 개수)
```

따라서 입력 Feature가 두 개이고 출력이 하나라면

```python
model = nn.Linear(2, 1)
```

이라고 작성한다.

구조를 나타내면

```text
입력 Feature 2개
       ↓
  nn.Linear
       ↓
출력 Feature 1개
```

이다.

내부적으로는

```text
x₁ ── w₁ ──┐
             ├── 합산 + b ──→ ŷ
x₂ ── w₂ ──┘
```

와 같은 계산이 이루어진다.

즉,

```python
nn.Linear(2, 1)
```

은 내부적으로

```text
w₁
w₂
b
```

를 학습하게 된다.

---

## 5. 학습 데이터 만들기

이번에는 실제 규칙을 다음과 같이 설정했다.

```text
y = 2x₁ + 3x₂ + 1
```

이 규칙을 이용해 데이터를 만들어보자.

첫 번째 데이터는

```text
x₁ = 1
x₂ = 1
```

이다.

그러면

```text
y = 2×1 + 3×1 + 1
  = 6
```

두 번째 데이터는

```text
x₁ = 2
x₂ = 1

y = 2×2 + 3×1 + 1
  = 8
```

세 번째 데이터는

```text
x₁ = 1
x₂ = 2

y = 2×1 + 3×2 + 1
  = 9
```

네 번째 데이터는

```text
x₁ = 2
x₂ = 2

y = 2×2 + 3×2 + 1
  = 11
```

따라서 전체 데이터는 다음과 같다.

| x₁ | x₂ | y |
|---:|---:|---:|
| 1 | 1 | 6 |
| 2 | 1 | 8 |
| 1 | 2 | 9 |
| 2 | 2 | 11 |

---

## 6. Tensor로 표현하기

PyTorch에서는 다음과 같이 데이터를 만들 수 있다.

```python
x = torch.tensor([
    [1.0, 1.0],
    [2.0, 1.0],
    [1.0, 2.0],
    [2.0, 2.0]
])
```

여기서 각 줄이 하나의 데이터이다.

```text
[1, 1] → 첫 번째 데이터
[2, 1] → 두 번째 데이터
[1, 2] → 세 번째 데이터
[2, 2] → 네 번째 데이터
```

그리고 각각의 데이터에는 값이 두 개 들어 있다.

즉,

```text
Feature = 2개
```

이다.

정답은 다음과 같이 만든다.

```python
y = torch.tensor([
    [6.0],
    [8.0],
    [9.0],
    [11.0]
])
```

---

## 7. Tensor의 Shape 이해하기

Shape을 확인해보자.

```python
print(x.shape)
print(y.shape)
```

결과는 다음과 같다.

```text
torch.Size([4, 2])
torch.Size([4, 1])
```

먼저

```text
x.shape = [4, 2]
```

는

```text
4개의 데이터
각 데이터마다 Feature 2개
```

라는 의미이다.

그리고

```text
y.shape = [4, 1]
```

은

```text
4개의 데이터
각 데이터마다 출력값 1개
```

라는 의미이다.

이를 `nn.Linear`와 연결하면 다음과 같다.

```text
x.shape = [4, 2]
              ↑
         Feature 2개

              ↓

       nn.Linear(2, 1)

              ↓

y.shape = [4, 1]
              ↑
          출력 1개
```

따라서 `nn.Linear`의 입력 Feature 수는 입력 Tensor의 마지막 차원과 연결된다.

---

## 8. 모델 생성

이번 모델은 입력 Feature가 2개이고 출력은 하나이다.

따라서

```python
model = nn.Linear(2, 1)
```

을 사용한다.

모델 내부의 Weight와 Bias는 다음과 같이 확인할 수 있다.

```python
print(model.weight)
print(model.bias)
```

이번에는 입력 Feature가 두 개이기 때문에 Weight도 두 개가 존재한다.

학습이 잘 이루어진다면

```text
w₁ ≈ 2
w₂ ≈ 3
b ≈ 1
```

에 가까워져야 한다.

왜냐하면 실제 데이터의 규칙이

```text
y = 2x₁ + 3x₂ + 1
```

이기 때문이다.

---

## 9. Feature가 늘어나도 학습 과정은 동일하다

Feature가 하나에서 두 개로 늘어났지만 학습 과정은 이전과 동일하다.

```python
optimizer.zero_grad()

y_pred = model(x)

loss = criterion(y_pred, y)

loss.backward()

optimizer.step()
```

각 과정의 의미는 다음과 같다.

```text
optimizer.zero_grad()
→ 이전 Gradient 초기화

model(x)
→ 현재 Weight와 Bias를 이용해 예측

criterion(y_pred, y)
→ 예측값과 정답을 비교하여 Loss 계산

loss.backward()
→ Gradient 계산

optimizer.step()
→ Weight와 Bias 수정
```

Feature가 늘어났다고 해서 우리가 각각의 Weight를 직접 수정할 필요는 없다.

PyTorch가 모델 내부의 모든 Parameter를 관리한다.

---

## 10. 편미분과 다시 연결하기

현재 모델은

```text
ŷ = w₁x₁ + w₂x₂ + b
```

이다.

학습해야 하는 값은

```text
w₁
w₂
b
```

세 개이다.

따라서 Loss를 줄이기 위해서는 각각에 대한 Gradient가 필요하다.

```text
∂L/∂w₁

∂L/∂w₂

∂L/∂b
```

이전에 공부했던 편미분 관점에서 보면 각각의 Parameter가 Loss에 얼마나 영향을 주는지를 계산하는 것이다.

PyTorch에서는

```python
loss.backward()
```

를 실행하면 Autograd가 이 Gradient들을 자동으로 계산한다.

그리고

```python
optimizer.step()
```

이 계산된 Gradient를 이용해 각 Parameter를 수정한다.

즉, Feature와 Weight가 늘어나더라도 지금까지 공부했던

```text
편미분
↓
Gradient
↓
Backpropagation
↓
Optimizer
```

의 원리는 그대로 사용된다.

---

## 11. 전체 PyTorch 코드

오늘 학습한 내용을 전체 코드로 작성하면 다음과 같다.

```python
import torch
import torch.nn as nn

# =========================
# 1. 학습 데이터
# =========================

x = torch.tensor([
    [1.0, 1.0],
    [2.0, 1.0],
    [1.0, 2.0],
    [2.0, 2.0]
])

y = torch.tensor([
    [6.0],
    [8.0],
    [9.0],
    [11.0]
])

# =========================
# 2. 모델
# =========================

model = nn.Linear(2, 1)

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

for epoch in range(2000):

    # 이전 Gradient 초기화
    optimizer.zero_grad()

    # Forward
    y_pred = model(x)

    # Loss
    loss = criterion(y_pred, y)

    # Backward
    loss.backward()

    # Parameter 업데이트
    optimizer.step()

    if (epoch + 1) % 200 == 0:
        print(
            f"Epoch {epoch + 1} | "
            f"Loss: {loss.item():.6f}"
        )

# =========================
# 6. 학습 결과 확인
# =========================

print()
print("Weight:", model.weight)
print("Bias:", model.bias)
```

학습이 충분히 진행되면 결과는 대략

```text
Weight ≈ [2, 3]
Bias ≈ 1
```

에 가까워진다.

---

## 12. 새로운 데이터 예측하기

이번에는 학습에 사용하지 않았던 데이터를 넣어보자.

```text
x₁ = 3
x₂ = 4
```

실제 규칙은

```text
y = 2x₁ + 3x₂ + 1
```

이므로

```text
y = 2×3 + 3×4 + 1

  = 6 + 12 + 1

  = 19
```

이다.

학습된 모델에서도 확인할 수 있다.

```python
new_x = torch.tensor([
    [3.0, 4.0]
])

with torch.no_grad():
    prediction = model(new_x)

print("예측값:", prediction.item())
```

모델이 잘 학습되었다면 결과는

```text
약 19
```

에 가까워진다.

---

## 13. 입력이 늘어나면 출력도 늘릴 수 있다

이번에는

```python
nn.Linear(2, 1)
```

을 사용했다.

즉,

```text
입력 2개
↓
출력 1개
```

였다.

하지만 PyTorch에서는 출력 Feature 역시 늘릴 수 있다.

예를 들어

```python
nn.Linear(2, 3)
```

이라고 하면

```text
입력 Feature 2개
↓
Linear
↓
출력 Feature 3개
```

가 된다.

따라서 `nn.Linear`는 단순히 선형회귀에서만 사용하는 것이 아니라 앞으로 배우게 될 신경망에서도 중요한 역할을 한다.

---

## 오늘 배운 내용 정리

오늘은 하나의 Feature를 사용하던 선형 모델에서 여러 Feature를 사용하는 모델로 확장했다.

기존에는

```text
ŷ = wx + b
```

였다면 Feature가 두 개가 되면서

```text
ŷ = w₁x₁ + w₂x₂ + b
```

가 되었다.

핵심은 **각각의 Feature에 대응하는 Weight가 존재한다는 것**이다.

PyTorch에서는

```python
nn.Linear(2, 1)
```

을 이용하여

```text
입력 Feature 2개
↓
출력 Feature 1개
```

인 모델을 만들 수 있다.

또한 Tensor의 Shape과 `nn.Linear`의 관계도 중요하다.

```text
x.shape = [4, 2]
           ↑  ↑
      데이터  Feature

              ↓

       nn.Linear(2, 1)

              ↓

출력 Shape = [4, 1]
```

그리고 Feature가 늘어나더라도 학습 과정은 변하지 않는다.

```text
데이터 입력
↓
Forward
↓
Loss
↓
Backward
↓
Gradient 계산
↓
Optimizer
↓
Parameter 수정
```

PyTorch의 Autograd가 각 Weight와 Bias에 대한 Gradient를 자동으로 계산하기 때문에 Feature가 늘어나더라도 동일한 방식으로 학습할 수 있다.

---

## 마무리

이번 학습에서는 입력 Feature가 여러 개일 때 선형 모델이 어떻게 확장되는지 공부했다.

특히

```text
Feature가 늘어난다
↓
각 Feature에 대응하는 Weight가 생긴다
↓
각 Weight가 학습된다
```

라는 구조를 이해할 수 있었다.

또한 `nn.Linear(2, 1)`의 숫자가 단순한 설정값이 아니라 Tensor의 Shape과 직접 연결되어 있다는 점도 확인했다.

지금까지는 최종 출력 하나를 만드는 선형 모델을 사용했다.

하지만 `nn.Linear(2, 3)`처럼 출력 역시 여러 개로 만들 수 있다.

이 구조를 확장하면 여러 입력과 여러 출력이 연결되는 형태가 되고, 이후 배우게 될 신경망의 기본 구조와 연결된다.