---
layout: post
title: "[AI Day 10] PyTorch Autograd와 Optimizer로 이해하는 학습 과정"
date: 2026-08-20
categories: [AI, PyTorch]
tags: [AI, PyTorch, Autograd, Backpropagation, Gradient, Optimizer, SGD]
---

# [AI 수학] Day 10 - PyTorch Autograd와 Optimizer로 이해하는 학습 과정

## 들어가며

지난 시간에는 **역전파(Backpropagation)**를 공부하면서 편미분과 체인룰이 실제 AI 학습에서 어떻게 사용되는지 알아보았다.

간단하게 정리하면 역전파는

```text
Loss
↓
Gradient 계산
↓
가중치가 Loss에 얼마나 영향을 주는지 확인
```

하는 과정이다.

하지만 실제 신경망에는 수많은 가중치가 존재하기 때문에 모든 미분을 사람이 직접 계산할 수는 없다.

PyTorch에서는 이를 **Autograd**라는 기능을 통해 자동으로 계산한다.

이번에는 PyTorch에서 Gradient를 계산하고 실제 가중치가 수정되는 과정까지 공부했다.

---

## 1. Autograd란?

Autograd는 PyTorch에서 **미분과 Gradient 계산을 자동으로 처리하는 기능**이다.

우리가 직접 계산했던

```text
편미분
↓
체인룰
↓
역전파
↓
Gradient 계산
```

과정을 PyTorch가 자동으로 처리해준다.

간단한 예제를 생각해보자.

```text
ŷ = wx
```

입력과 정답을 다음과 같이 설정한다.

```text
x = 2
w = 3
y = 8
```

예측값은

```text
ŷ = 3 × 2
  = 6
```

이다.

손실 함수를

```text
Loss = (ŷ - y)²
```

로 사용하면

```text
Loss = (6 - 8)²
     = 4
```

가 된다.

---

## 2. requires_grad=True

PyTorch에서는 다음과 같이 Tensor를 만들 수 있다.

```python
import torch

w = torch.tensor(3.0, requires_grad=True)
```

여기서

```python
requires_grad=True
```

는 PyTorch에게

> 이 Tensor에 대한 Gradient를 계산할 필요가 있다.

라고 알려주는 설정이다.

우리 예제에서 `x`는 입력값이고 `y`는 정답이기 때문에 학습을 통해 변경하려는 값이 아니다.

학습하려는 값은 `w`이다.

따라서 다음과 같이 만들 수 있다.

```python
x = torch.tensor(2.0)
y = torch.tensor(8.0)

w = torch.tensor(3.0, requires_grad=True)
```

---

## 3. 순전파와 Loss 계산

먼저 현재 가중치를 이용해 예측값을 계산한다.

```python
y_pred = w * x
```

현재 값에서는

```text
3 × 2 = 6
```

이므로 예측값은 `6`이다.

그다음 Loss를 계산한다.

```python
loss = (y_pred - y) ** 2
```

계산하면

```text
(6 - 8)² = 4
```

이므로 현재 Loss는 `4`이다.

여기까지는

```text
입력
↓
예측
↓
Loss
```

방향으로 계산했기 때문에 **순전파(Forward)** 과정이라고 볼 수 있다.

---

## 4. loss.backward()

이제 Gradient를 계산해야 한다.

PyTorch에서는 다음 한 줄을 사용한다.

```python
loss.backward()
```

`backward()`를 실행하면 PyTorch가 Loss에서부터 계산 과정을 거꾸로 따라가며 Gradient를 계산한다.

우리가 직접 계산한다면

```text
∂Loss/∂w
```

를 구하는 과정이다.

현재 예제에서는 체인룰을 이용하면

```text
∂Loss/∂w
=
∂Loss/∂ŷ × ∂ŷ/∂w
```

이다.

각각 계산하면

```text
∂Loss/∂ŷ = 2(ŷ-y)

             = 2(6-8)

             = -4
```

그리고

```text
ŷ = wx
```

를 `w`에 대해 편미분하면 `x`는 상수로 보기 때문에

```text
∂ŷ/∂w = x
```

이다.

현재 `x=2`이므로

```text
∂ŷ/∂w = 2
```

따라서

```text
∂Loss/∂w
=
-4 × 2

= -8
```

이다.

PyTorch에서는 이 계산을

```python
loss.backward()
```

가 자동으로 수행한다.

---

## 5. 계산된 Gradient 확인하기

`backward()`를 실행하면 계산된 Gradient는 Tensor의 `.grad`에 저장된다.

```python
print(w.grad)
```

현재 예제에서는

```text
tensor(-8.)
```

이 출력된다.

즉,

```text
w.grad = -8
```

이다.

중요한 점은 `backward()`가 **가중치를 수정하는 것은 아니라는 것**이다.

```text
w = 3
w.grad = -8
```

처럼 Gradient만 계산된 상태이다.

---

## 6. Optimizer란?

Gradient를 계산했다면 이제 실제 가중치를 수정해야 한다.

PyTorch에서는 **Optimizer**를 사용한다.

예를 들어 SGD를 사용하면

```python
optimizer = torch.optim.SGD([w], lr=0.1)
```

처럼 작성한다.

여기서

```text
SGD
→ 가중치를 수정할 방법

[w]
→ 수정할 가중치

lr=0.1
→ Learning Rate(학습률)
```

을 의미한다.

Optimizer는 Loss와는 다른 개념이다.

```text
Loss
→ 얼마나 틀렸는지 계산

Optimizer
→ Gradient를 이용해 가중치를 어떻게 수정할지 결정
```

---

## 7. optimizer.step()

실제로 가중치를 수정하려면 다음 코드를 실행한다.

```python
optimizer.step()
```

현재

```text
w = 3
Gradient = -8
Learning Rate = 0.1
```

이다.

SGD는 우리가 배운 경사하강법과 같은 원리로 가중치를 수정한다.

```text
새로운 가중치
=
기존 가중치 - 학습률 × Gradient
```

따라서

```text
새로운 w
=
3 - 0.1 × (-8)

= 3.8
```

이 된다.

즉,

```python
optimizer.step()
```

을 실행하면 실제로

```text
w

3.0 → 3.8
```

로 변경된다.

---

## 8. backward()와 optimizer.step()의 차이

두 함수의 역할을 구분하는 것이 중요하다.

```python
loss.backward()
```

는

```text
Gradient 계산
```

을 담당한다.

반면

```python
optimizer.step()
```

은

```text
Gradient를 이용한 실제 가중치 수정
```

을 담당한다.

따라서 전체 학습 흐름은

```text
예측
↓
Loss 계산
↓
loss.backward()
↓
Gradient 계산
↓
optimizer.step()
↓
가중치 수정
```

이 된다.

---

## 9. 전체 실습 코드

지금까지의 내용을 하나의 코드로 작성하면 다음과 같다.

```python
import torch

# 입력값과 정답
x = torch.tensor(2.0)
y = torch.tensor(8.0)

# 학습할 가중치
w = torch.tensor(3.0, requires_grad=True)

# SGD Optimizer 생성
optimizer = torch.optim.SGD([w], lr=0.1)

print("처음 가중치:", w.item())

# 1. 순전파
y_pred = w * x

# 2. Loss 계산
loss = (y_pred - y) ** 2

print("예측값:", y_pred.item())
print("Loss:", loss.item())

# 3. 역전파
loss.backward()

print("Gradient:", w.grad.item())

# 4. 가중치 수정
optimizer.step()

print("수정된 가중치:", w.item())
```

실행 결과는 다음과 같다.

```text
처음 가중치: 3.0
예측값: 6.0
Loss: 4.0
Gradient: -8.0
수정된 가중치: 3.8
```

---

## 10. 여러 번 학습시키기

실제 AI 학습에서는 이 과정을 한 번만 실행하지 않고 반복한다.

```python
import torch

x = torch.tensor(2.0)
y = torch.tensor(8.0)

w = torch.tensor(3.0, requires_grad=True)

optimizer = torch.optim.SGD([w], lr=0.1)

for epoch in range(5):

    # 이전 Gradient 초기화
    optimizer.zero_grad()

    # 순전파
    y_pred = w * x

    # Loss 계산
    loss = (y_pred - y) ** 2

    # 역전파
    loss.backward()

    print(f"--- Epoch {epoch + 1} ---")
    print("수정 전 w:", w.item())
    print("예측값:", y_pred.item())
    print("Loss:", loss.item())
    print("Gradient:", w.grad.item())

    # 가중치 수정
    optimizer.step()

    print("수정 후 w:", w.item())
    print()
```

학습을 반복하면 가중치는 점점 `4`에 가까워진다.

```text
3.0
↓
3.8
↓
3.96
↓
3.992
↓
...
↓
4.0
```

왜 `4`에 가까워질까?

우리 모델은

```text
ŷ = wx
```

이고 입력값은 `2`, 정답은 `8`이다.

정답을 정확하게 예측하려면

```text
8 = w × 2
```

여야 하므로

```text
w = 4
```

가 되어야 하기 때문이다.

가중치가 4에 가까워질수록 예측값도 8에 가까워지고 Loss는 0에 가까워진다.

---

## 오늘 배운 내용 정리

PyTorch의 기본 학습 과정은 다음과 같이 정리할 수 있다.

```text
requires_grad=True
→ Gradient를 계산할 Tensor 지정

        ↓

Forward
→ 예측값 계산

        ↓

Loss
→ 얼마나 틀렸는지 계산

        ↓

loss.backward()
→ 역전파를 통해 Gradient 계산

        ↓

w.grad
→ 계산된 Gradient 확인

        ↓

optimizer.step()
→ Gradient를 이용해 가중치 수정
```

특히 다음 세 가지를 구분하는 것이 중요하다.

```text
Loss
= 얼마나 틀렸는가?

backward()
= 어느 방향으로 수정해야 하는가?

optimizer.step()
= 실제 가중치를 수정한다.
```

---

## 마무리

이번에는 지금까지 수학으로 배웠던 미분, 편미분, 체인룰, 역전파, 경사하강법이 PyTorch에서 어떻게 사용되는지 확인했다.

처음에는

```python
loss.backward()
optimizer.step()
```

이라는 두 줄의 코드에 불과하지만, 내부적으로는 지금까지 배웠던 개념들이 연결되어 있다.

특히

```python
loss.backward()
```

는 역전파를 통해 Gradient를 계산하고,

```python
optimizer.step()
```

은 계산된 Gradient를 이용해 실제 가중치를 수정한다.

결국 AI 학습은

```text
예측
→ 틀린 정도 계산
→ Gradient 계산
→ 가중치 수정
→ 다시 예측
```

과정을 반복하면서 점점 더 좋은 값을 찾아가는 과정이라고 이해할 수 있다.