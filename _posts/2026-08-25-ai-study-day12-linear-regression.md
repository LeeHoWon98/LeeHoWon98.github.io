---
layout: post
title: "[AI Day 12] 여러 데이터로 배우는 선형회귀와 MSE"
date: 2026-08-25
categories: [AI, PyTorch]
tags: [AI, PyTorch, LinearRegression, MSE, Gradient, Optimizer]
---

# [AI 수학] Day 12 - 여러 데이터로 배우는 선형회귀와 MSE

## 들어가며

지난 시간에는 다음과 같은 모델에서 Weight와 Bias를 학습하는 과정을 공부했다.

```text
ŷ = wx + b
```

이번에는 데이터 하나가 아니라 **여러 개의 데이터를 이용해 모델을 학습**해보았다.

특히 이번 학습에서는

- 선형회귀
- 여러 데이터의 예측
- MSE
- Gradient 초기화
- 새로운 데이터 예측

을 중심으로 공부했다.

---

## 1. 선형회귀란?

다음과 같은 데이터가 있다고 해보자.

| x | y |
|---:|---:|
| 1 | 3 |
| 2 | 5 |
| 3 | 7 |
| 4 | 9 |

사람이 보면 다음과 같은 규칙을 쉽게 발견할 수 있다.

```text
y = 2x + 1
```

하지만 모델에게 이 공식을 직접 알려주지는 않는다.

대신

```text
(1, 3)
(2, 5)
(3, 7)
(4, 9)
```

라는 데이터만 제공한다.

모델은 학습을 반복하면서 데이터에 가장 잘 맞는 `w`와 `b`를 찾아간다.

이번에 사용하는 모델은 이전과 동일하다.

```text
ŷ = wx + b
```

학습이 잘 이루어진다면 최종적으로

```text
w ≈ 2
b ≈ 1
```

에 가까운 값을 찾게 된다.

---

## 2. 여러 데이터를 Tensor로 만들기

데이터 하나를 사용할 때는 다음과 같이 작성했다.

```python
x = torch.tensor(2.0)
```

여러 데이터를 사용하려면 Tensor 안에 여러 값을 넣는다.

```python
x = torch.tensor([1.0, 2.0, 3.0, 4.0])
y = torch.tensor([3.0, 5.0, 7.0, 9.0])
```

이제 모델은 네 개의 데이터를 이용해서 학습한다.

---

## 3. 처음에는 w와 b의 정답을 모른다

학습할 값은 다음과 같이 설정할 수 있다.

```python
w = torch.tensor(0.0, requires_grad=True)
b = torch.tensor(0.0, requires_grad=True)
```

처음에는

```text
w = 0
b = 0
```

으로 시작한다.

현재 모델은

```text
ŷ = 0x + 0
```

이므로 모든 입력에 대해 예측값은 0이다.

```text
예측값 = [0, 0, 0, 0]

정답   = [3, 5, 7, 9]
```

당연히 처음에는 많은 오차가 발생한다.

---

## 4. 여러 데이터의 Loss 계산

각 데이터의 오차를 제곱하면 다음과 같다.

```text
(0 - 3)² = 9
(0 - 5)² = 25
(0 - 7)² = 49
(0 - 9)² = 81
```

즉,

```text
[9, 25, 49, 81]
```

이라는 네 개의 값이 나온다.

하지만 학습에서는 전체 데이터의 오차를 하나의 값으로 나타내는 것이 필요하다.

그래서 평균을 계산한다.

```text
(9 + 25 + 49 + 81) / 4
= 41
```

이러한 방법을 **MSE(Mean Squared Error)**라고 한다.

```text
Mean    = 평균
Squared = 제곱
Error   = 오차
```

즉, 평균 제곱 오차이다.

---

## 5. PyTorch에서 MSE 계산하기

PyTorch에서는 다음과 같이 계산할 수 있다.

```python
loss = ((y_pred - y) ** 2).mean()
```

하나씩 보면

```text
y_pred - y
→ 예측값과 정답의 차이

** 2
→ 오차를 제곱

.mean()
→ 모든 오차의 평균
```

이다.

오차를 제곱하는 이유 중 하나는 양수와 음수 오차가 서로 상쇄되는 것을 방지하기 위해서이다.

예를 들어

```text
+2
-2
```

를 그냥 더하면 0이 된다.

하지만 제곱하면

```text
(+2)² = 4
(-2)² = 4
```

가 되어 두 오차가 사라지지 않는다.

---

## 6. backward()로 Gradient 계산

Loss를 계산한 다음에는

```python
loss.backward()
```

를 실행한다.

PyTorch의 Autograd가 자동으로

```text
∂Loss/∂w
∂Loss/∂b
```

를 계산한다.

즉,

```text
현재 w를 어느 방향으로 수정해야 하는가?

현재 b를 어느 방향으로 수정해야 하는가?
```

를 판단하기 위한 Gradient를 계산한다.

---

## 7. optimizer.step()으로 실제 값 수정

Gradient가 계산되면

```python
optimizer.step()
```

을 실행한다.

Optimizer는 Gradient와 Learning Rate를 이용해 실제 `w`와 `b`를 수정한다.

기본적인 원리는

```text
새로운 값
=
기존 값 - Learning Rate × Gradient
```

이다.

학습이 반복되면서

```text
w → 2에 가까워짐
b → 1에 가까워짐
Loss → 0에 가까워짐
```

이라는 변화가 나타난다.

---

## 8. zero_grad()는 왜 필요할까?

이번 학습에서 헷갈렸던 부분이

```python
optimizer.zero_grad()
```

였다.

처음에는 `optimizer.step()`으로 `w`와 `b`를 수정한 다음 `zero_grad()`를 실행하면 학습한 내용도 사라지는 것이 아닌가 생각했다.

하지만 `zero_grad()`는 **w와 b를 초기화하지 않는다.**

예를 들어 한 번 학습해서

```text
w = 0.35
b = 0.12
```

가 되었다고 하자.

그리고 이전 학습에서 계산한 Gradient가

```text
w.grad = -35
b.grad = -12
```

로 남아 있다고 해보자.

이때

```python
optimizer.zero_grad()
```

를 실행하면

```text
w = 0.35      ← 그대로
b = 0.12      ← 그대로

w.grad = 0    ← 초기화
b.grad = 0    ← 초기화
```

가 된다.

즉,

```text
w, b
→ 지금까지 학습한 값

w.grad, b.grad
→ 현재 학습 단계에서 계산된 수정 방향
```

이라고 구분할 수 있다.

---

## 9. 왜 Gradient를 초기화해야 할까?

PyTorch에서는 `backward()`를 여러 번 실행하면 Gradient가 기본적으로 누적된다.

예를 들어 이전 Gradient가

```text
w.grad = -10
```

인 상태에서 새로운 Gradient `-5`가 계산되면 의도하지 않게 이전 값과 누적될 수 있다.

그래서 새로운 학습을 시작하기 전에

```python
optimizer.zero_grad()
```

를 실행하여 이전 Gradient를 지운다.

중요한 것은

```text
zero_grad()
= 학습 결과 초기화 X
= Gradient 초기화 O
```

라는 것이다.

---

## 10. 전체 학습 과정

하나의 Epoch에서 학습 과정은 다음과 같다.

```text
optimizer.zero_grad()
↓
이전 Gradient 제거

ŷ = wx + b
↓
현재 w와 b로 예측

Loss 계산
↓
예측값과 정답 비교

loss.backward()
↓
새로운 Gradient 계산

optimizer.step()
↓
w와 b 수정
```

그리고 다음 Epoch가 시작되면 **수정된 w와 b를 그대로 사용한다.**

```text
Epoch 1

w, b로 예측
↓
Loss
↓
Gradient
↓
w, b 수정
        ↓
        ↓ 학습된 값 유지
        ↓
Epoch 2

Gradient만 초기화
↓
수정된 w, b로 다시 예측
↓
새로운 Loss
↓
새로운 Gradient
↓
w, b 다시 수정
```

따라서 `zero_grad()`를 사용해도 학습은 계속 이어진다.

---

## 11. 전체 PyTorch 코드

```python
import torch

# 학습 데이터
x = torch.tensor([1.0, 2.0, 3.0, 4.0])
y = torch.tensor([3.0, 5.0, 7.0, 9.0])

# 학습할 Weight와 Bias
w = torch.tensor(0.0, requires_grad=True)
b = torch.tensor(0.0, requires_grad=True)

# Optimizer
optimizer = torch.optim.SGD([w, b], lr=0.01)

for epoch in range(1000):

    # 이전 Gradient 초기화
    optimizer.zero_grad()

    # 예측
    y_pred = w * x + b

    # MSE Loss
    loss = ((y_pred - y) ** 2).mean()

    # Gradient 계산
    loss.backward()

    # w와 b 수정
    optimizer.step()

    # 100번마다 결과 확인
    if (epoch + 1) % 100 == 0:
        print(
            f"Epoch {epoch + 1} | "
            f"Loss: {loss.item():.6f} | "
            f"w: {w.item():.4f} | "
            f"b: {b.item():.4f}"
        )
```

학습이 진행되면

```text
w ≈ 2
b ≈ 1
```

에 가까워진다.

---

## 12. 새로운 데이터 예측하기

학습이 끝난 후에는 학습에 사용하지 않았던 새로운 데이터도 넣어볼 수 있다.

```python
new_x = torch.tensor(5.0)

prediction = w * new_x + b

print("x=5 예측값:", prediction.item())
```

원래 데이터의 규칙은

```text
y = 2x + 1
```

이므로 `x=5`라면

```text
y = 2 × 5 + 1
  = 11
```

이다.

모델이 제대로 학습되었다면 예측값도 11에 가까운 값이 나온다.

중요한 점은 모델에게

```text
y = 2x + 1
```

이라는 공식을 직접 알려주지 않았다는 것이다.

모델에게 제공한 것은

```text
(1, 3)
(2, 5)
(3, 7)
(4, 9)
```

라는 데이터뿐이다.

모델은 데이터를 이용한 반복 학습을 통해 `w`와 `b`를 찾아낸 것이다.

---

## 오늘 배운 내용 정리

오늘은 하나의 데이터가 아닌 여러 데이터를 이용하여 선형회귀를 학습해보았다.

전체적인 과정은 다음과 같다.

```text
여러 데이터 입력
↓
ŷ = wx + b
↓
여러 예측값 생성
↓
MSE로 전체 오차 계산
↓
loss.backward()
↓
w와 b의 Gradient 계산
↓
optimizer.step()
↓
w와 b 수정
↓
반복
```

그리고 반복 학습에서 중요한 부분이 하나 더 있었다.

```python
optimizer.zero_grad()
```

는 `w`와 `b`를 초기화하는 것이 아니다.

```text
w, b
→ 학습 결과이므로 유지

w.grad, b.grad
→ 이전 Gradient이므로 초기화
```

라고 구분해야 한다.

결국 모델은 여러 데이터를 반복해서 확인하면서 Loss가 작아지는 방향으로 `w`와 `b`를 수정하고, 데이터에 숨어 있는 규칙을 찾아간다.

---

## 마무리

이번에는 지금까지 배운 내용들이 실제 머신러닝의 형태로 조금 더 연결되었다.

```text
데이터
→ 예측
→ Loss
→ Gradient
→ 가중치 수정
→ 다시 예측
```

이라는 학습 과정은 이전과 동일하지만, 여러 데이터를 사용하면서 모델이 하나의 데이터에 맞추는 것이 아니라 **전체 데이터에 맞는 규칙을 찾아간다는 점**이 중요했다.

또한 `zero_grad()`는 학습 결과를 지우는 것이 아니라 이전 Gradient만 초기화한다는 점도 이해할 수 있었다.

다음 학습에서는 이러한 과정을 PyTorch가 제공하는 기능을 이용해 조금 더 실제 머신러닝 코드에 가까운 형태로 만들어보고자 한다.