---
layout: post
title: "[AI Day 11] Weight와 Bias, 두 개의 값을 학습시켜보자"
date: 2026-08-24
categories: [AI, PyTorch]
tags: [AI, PyTorch, Weight, Bias, Gradient, Backpropagation, SGD]
---

# [AI 수학] Day 11 - Weight와 Bias, 두 개의 값을 학습시켜보자

## 들어가며

지난 시간에는 PyTorch의 `Autograd`를 이용하여 Gradient를 계산하고,
Optimizer를 이용해 가중치를 수정하는 과정을 공부했다.

기존 모델은 다음과 같이 단순했다.

```text
ŷ = wx
```

이번에는 여기에 **Bias(편향)**를 추가한다.

```text
ŷ = wx + b
```

그리고 `w`와 `b` 두 값을 동시에 학습시키는 과정을 알아보았다.

---

## 1. Weight와 Bias

선형 모델은 다음과 같이 표현할 수 있다.

```text
ŷ = wx + b
```

여기서

```text
w = Weight(가중치)
b = Bias(편향)
```

이다.

Weight는 입력값이 결과에 미치는 정도를 조절하고,
Bias는 결과의 기본적인 위치를 조절한다.

일차함수

```text
y = ax + b
```

와 비교하면 이해하기 쉽다.

```text
w → 기울기와 비슷한 역할
b → y절편과 비슷한 역할
```

Bias가 없다면

```text
ŷ = wx
```

이므로 `x=0`일 때 결과는 항상 0이다.

하지만 Bias가 있다면

```text
ŷ = wx + b
```

이므로 `x=0`이어도

```text
ŷ = b
```

가 될 수 있다.

---

## 2. 이번에는 w와 b를 모두 학습한다

PyTorch에서는 다음과 같이 만들 수 있다.

```python
w = torch.tensor(1.0, requires_grad=True)
b = torch.tensor(0.0, requires_grad=True)
```

둘 다 학습해야 하기 때문에 `requires_grad=True`를 사용한다.

우리가 알고 싶은 것은 결국

```text
w가 Loss에 얼마나 영향을 주는가?

b가 Loss에 얼마나 영향을 주는가?
```

이다.

수학적으로는 각각

```text
∂L/∂w

∂L/∂b
```

를 계산하는 것이다.

---

## 3. 편미분 다시 이해하기

변수가 여러 개 있을 때 특정 변수 하나의 변화만 확인하는 것이 편미분이다.

예를 들어

```text
z = xy
```

에서 x에 대해 편미분한다고 하자.

이때 y는 고정한다.

만약 `y=3`이라면

```text
xy = 3x
```

와 같다.

x가 1 증가하면 결과는 3 증가한다.

따라서

```text
∂(xy)/∂x = y
```

이다.

반대로 y에 대해 편미분하면 x를 고정하므로

```text
∂(xy)/∂y = x
```

가 된다.

즉,

```text
어떤 변수에 대해 편미분한다
→ 그 변수의 변화만 본다
→ 나머지 변수는 고정한다
```

라고 이해할 수 있다.

---

## 4. Bias를 편미분하면 왜 1일까?

모델은

```text
ŷ = wx + b
```

이다.

이번에는 `b`에 대해 편미분해보자.

`b`의 변화만 보기 때문에 `w`와 `x`는 고정한다.

예를 들어

```text
w = 1
x = 2
```

라면 식은

```text
ŷ = 2 + b
```

가 된다.

b를 변화시켜보면

```text
b = 0 → ŷ = 2
b = 1 → ŷ = 3
b = 2 → ŷ = 4
```

b가 1 증가할 때 ŷ도 1 증가한다.

따라서 변화율은 1이다.

```text
∂ŷ/∂b = 1
```

여기서 중요한 것은 앞의 값을 단순히 무시하는 것이 아니다.

`b`에 대해 편미분하고 있기 때문에 `b`와 관계없이 고정되어 있는 `wx`의 변화량이 0이 되는 것이다.

---

## 5. 실제 예제

다음 값을 사용해보자.

```text
x = 2
y = 7

w = 1
b = 0
```

모델은

```text
ŷ = wx + b
```

이므로 첫 번째 예측은

```text
ŷ = 1 × 2 + 0
  = 2
```

이다.

정답은 7이므로 아직 차이가 크다.

---

## 6. Loss 계산

손실 함수는 이전과 동일하게 제곱 오차를 사용한다.

```text
L = (ŷ - y)²
```

현재 값에서는

```text
L = (2 - 7)²
  = 25
```

이다.

이제 Loss를 줄이기 위해 `w`와 `b`를 어떻게 수정해야 하는지 알아내야 한다.

---

## 7. w의 Gradient

우리가 원하는 것은

```text
∂L/∂w
```

이다.

계산 과정은

```text
w
↓
ŷ
↓
Loss
```

이므로 체인룰을 사용한다.

```text
∂L/∂w
=
∂L/∂ŷ × ∂ŷ/∂w
```

먼저

```text
L = (ŷ-y)²
```

이므로

```text
∂L/∂ŷ = 2(ŷ-y)
```

현재 `ŷ=2`, `y=7`이므로

```text
∂L/∂ŷ
= 2(2-7)
= -10
```

그리고

```text
ŷ = wx + b
```

를 w에 대해 편미분하면

```text
∂ŷ/∂w = x
```

현재 `x=2`이므로

```text
∂ŷ/∂w = 2
```

따라서

```text
∂L/∂w
= -10 × 2
= -20
```

이다.

---

## 8. b의 Gradient

이번에는

```text
∂L/∂b
```

를 구한다.

마찬가지로

```text
b
↓
ŷ
↓
Loss
```

이므로 체인룰을 사용한다.

```text
∂L/∂b
=
∂L/∂ŷ × ∂ŷ/∂b
```

앞에서

```text
∂L/∂ŷ = -10
```

을 구했다.

그리고 b에 대한 변화율은

```text
∂ŷ/∂b = 1
```

이었다.

따라서

```text
∂L/∂b
= -10 × 1
= -10
```

이다.

결국

```text
w의 Gradient = -20
b의 Gradient = -10
```

을 얻었다.

---

## 9. PyTorch의 backward()

위 계산을 PyTorch에서는 직접 할 필요가 없다.

```python
loss.backward()
```

를 실행하면 Autograd가 편미분과 체인룰을 이용하여 자동으로 Gradient를 계산한다.

결과는 각각

```python
print(w.grad)
print(b.grad)
```

에서 확인할 수 있다.

```text
w.grad = -20
b.grad = -10
```

즉, 우리가 직접 계산한 결과와 동일하다.

---

## 10. Optimizer로 w와 b 수정하기

이번에는 학습할 값이 두 개이므로 Optimizer에 둘 다 전달한다.

```python
optimizer = torch.optim.SGD([w, b], lr=0.01)
```

`[w, b]`는 Optimizer에게

```text
w와 b 모두 학습할 값이다.
```

라고 알려주는 것이다.

Gradient를 계산한 뒤

```python
optimizer.step()
```

을 실행하면 실제 값이 수정된다.

경사하강법의 기본 원리는

```text
새로운 값
=
기존 값 - 학습률 × Gradient
```

이다.

w의 경우

```text
1 - 0.01 × (-20)
= 1.2
```

b의 경우

```text
0 - 0.01 × (-10)
= 0.1
```

이므로

```text
w : 1.0 → 1.2
b : 0.0 → 0.1
```

로 변경된다.

---

## 11. 학습 전과 후

학습 전에는

```text
ŷ = 1 × 2 + 0
  = 2
```

였다.

한 번 학습한 후에는

```text
w = 1.2
b = 0.1
```

이므로

```text
ŷ = 1.2 × 2 + 0.1
  = 2.5
```

가 된다.

즉,

```text
학습 전 예측 = 2.0
학습 후 예측 = 2.5
정답          = 7.0
```

아직 정답에는 도달하지 않았지만 예측값이 정답 방향으로 이동했다.

이 과정을 반복하면서 Loss를 줄여나가는 것이 학습이다.

---

## 12. PyTorch 실습

전체 과정을 코드로 작성하면 다음과 같다.

```python
import torch

# 입력값과 정답
x = torch.tensor(2.0)
y = torch.tensor(7.0)

# 학습할 Weight와 Bias
w = torch.tensor(1.0, requires_grad=True)
b = torch.tensor(0.0, requires_grad=True)

# Optimizer
optimizer = torch.optim.SGD([w, b], lr=0.01)

for epoch in range(10):

    # 이전 Gradient 초기화
    optimizer.zero_grad()

    # 예측
    y_pred = w * x + b

    # Loss 계산
    loss = (y_pred - y) ** 2

    # Gradient 계산
    loss.backward()

    # w와 b 수정
    optimizer.step()

    print(
        f"Epoch {epoch + 1} | "
        f"예측: {y_pred.item():.4f} | "
        f"Loss: {loss.item():.4f} | "
        f"w: {w.item():.4f} | "
        f"b: {b.item():.4f}"
    )
```

여기서 학습 과정은 다음과 같다.

```text
예측
↓
Loss 계산
↓
loss.backward()
↓
w와 b의 Gradient 계산
↓
optimizer.step()
↓
w와 b 수정
↓
다시 예측
```

또한 PyTorch에서는 Gradient가 누적되기 때문에 반복 학습에서는

```python
optimizer.zero_grad()
```

를 사용하여 이전 Gradient를 초기화한다.

---

## 오늘 배운 내용 정리

오늘은 기존의

```text
ŷ = wx
```

모델에서 한 단계 발전하여

```text
ŷ = wx + b
```

를 학습했다.

핵심은 `w`와 `b`가 모두 학습 대상이라는 것이다.

```text
w
→ 입력값이 결과에 미치는 정도를 조절

b
→ 결과의 기본 위치를 조절
```

그리고 각각의 Gradient를 구한다.

```text
∂L/∂w
→ w가 Loss에 미치는 영향

∂L/∂b
→ b가 Loss에 미치는 영향
```

PyTorch에서는

```python
loss.backward()
```

가 두 Gradient를 자동으로 계산하고,

```python
optimizer.step()
```

이 계산된 Gradient를 이용해 실제 `w`와 `b`를 수정한다.

결국 전체 학습 과정은

```text
예측
→ Loss
→ Gradient 계산
→ Weight와 Bias 수정
→ 다시 예측
```

의 반복이라고 이해할 수 있다.

---

## 마무리

이번 학습에서 특히 중요했던 부분은 편미분이었다.

편미분은 어렵게 생각하기보다

> 특정 변수 하나의 변화만 확인하고 나머지 변수는 고정한다.

라고 이해하면 된다.

그리고 `w`와 `b` 각각의 변화가 최종 Loss에 어떤 영향을 주는지 체인룰을 통해 계산한다.

지금까지 하나의 가중치만 수정했다면, 이번에는 Weight와 Bias라는 여러 학습 대상의 Gradient를 각각 구하고 동시에 수정하는 과정을 확인할 수 있었다.

다음에는 하나의 데이터가 아니라 여러 개의 데이터를 이용하여 모델이 실제 관계를 찾아가는 과정을 공부해보고자 한다.