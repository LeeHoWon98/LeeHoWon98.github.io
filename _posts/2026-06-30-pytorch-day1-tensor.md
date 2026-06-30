---
layout: post
title: "[AI Day 1] PyTorch 시작하기 - Tensor란 무엇일까?"
date: 2026-06-30
categories: [AI, PyTorch]
tags: [AI, DeepLearning, PyTorch, Tensor]
image: /assets/images/pytorch-day1.png
---

# PyTorch 시작하기 - Tensor란 무엇일까?

오늘부터 AI 공부를 다시 시작했다.

단순히 이론만 공부하는 것이 아니라 **실제로 AI 서비스를 만들 수 있는 개발자**를 목표로 매일 학습한 내용을 정리해 보려고 한다.

오늘은 PyTorch의 가장 기본이 되는 **Tensor**에 대해 공부했다.

---

# AI와 딥러닝의 관계

먼저 AI가 어떻게 구성되는지 이해하는 것이 중요하다.

```text
AI
 └── Machine Learning
      └── Deep Learning
           └── Transformer
                └── GPT
```

- **AI** : 사람처럼 문제를 해결하도록 만드는 기술
- **Machine Learning** : 데이터를 통해 규칙을 학습하는 기술
- **Deep Learning** : 인공신경망을 이용해 복잡한 패턴을 학습하는 기술

최근의 ChatGPT와 같은 생성형 AI도 모두 딥러닝을 기반으로 만들어졌다.

---

# Tensor란?

Tensor는 **PyTorch에서 사용하는 기본 데이터 구조**이다.

Python에서는 리스트를 사용하지만

```python
a = [1,2,3]
```

PyTorch에서는 Tensor를 사용한다.

```python
import torch

a = torch.tensor([1,2,3])
```

Tensor를 사용하는 이유는 다음과 같다.

- GPU를 이용한 빠른 연산
- 자동 미분(Autograd) 지원
- 효율적인 행렬 연산
- 딥러닝 모델 학습에 최적화

---

# Tensor의 차원

Tensor는 다양한 차원을 표현할 수 있다.

| 차원 | 예시 |
|------|------|
| 0차원 | `5` |
| 1차원 | `[1,2,3]` |
| 2차원 | `[[1,2],[3,4]]` |
| 3차원 | 이미지 여러 장 |
| 4차원 | 딥러닝에서 사용하는 이미지 Batch |

---

# 이미지 데이터는 왜 (32, 3, 224, 224)일까?

딥러닝을 공부하다 보면 가장 많이 보는 형태가 바로

```python
(32, 3, 224, 224)
```

이다.

각 숫자는 다음을 의미한다.

| 값 | 의미 |
|-----|------|
| 32 | Batch Size (한 번에 학습하는 이미지 개수) |
| 3 | RGB Channel |
| 224 | 이미지 높이(Height) |
| 224 | 이미지 너비(Width) |

예를 들어

1024×768 크기의 이미지를 모델에 넣기 전에

```
1024 × 768

↓

224 × 224
```

처럼 크기를 맞춘 후 학습하게 된다.

---

# Tensor의 기본 속성

## dtype

Tensor 안에 저장된 데이터의 자료형이다.

```python
print(x.dtype)
```

예시

```python
torch.int64
torch.float32
torch.bool
```

---

## shape

Tensor의 **각 차원의 크기**를 나타낸다.

```python
x = torch.tensor([[1,2,3],
                  [4,5,6]])

print(x.shape)
```

출력

```python
torch.Size([2,3])
```

이는

- 행 2개
- 열 3개

라는 의미이다.

---

# Tensor 연산

## 덧셈

```python
A + B
```

---

## 뺄셈

```python
A - B
```

---

## 원소별 곱셈

```python
A * B
```

같은 위치의 원소끼리 곱한다.

예를 들어

```
1 × 11
2 × 12
3 × 13
...
```

처럼 계산된다.

---

## 행렬 곱셈

```python
torch.matmul(A, B)
```

또는

```python
A @ B
```

두 방식은 동일하다.

행렬 곱셈은

```
행(Row)

×

열(Column)
```

을 이용하여 계산한다.

딥러닝에서는 입력 데이터와 가중치를 계산할 때 이 연산이 가장 많이 사용된다.

---

# 실습 코드

```python
import torch

A = torch.tensor([[1,2,3],
                  [4,5,6],
                  [7,8,9]])

B = torch.tensor([[11,12,13],
                  [14,15,16],
                  [17,18,19]])

print("덧셈")
print(A + B)

print("뺄셈")
print(A - B)

print("원소별 곱셈")
print(A * B)

print("행렬 곱셈")
print(A @ B)
```

---

# 오늘 공부하면서 느낀 점

처음에는 Tensor가 단순히 Python 리스트를 대체하는 자료형이라고 생각했다.

하지만 공부하면서 Tensor는 GPU 연산과 자동 미분을 지원하기 때문에 **딥러닝을 위해 설계된 자료형**이라는 점을 알게 되었다.

또한 `A * B`와 `A @ B`는 비슷해 보이지만 실제로는 전혀 다른 연산이라는 점도 이해하게 되었다.

---

# 마무리

오늘은 PyTorch의 가장 기본이 되는 Tensor를 공부했다.

다음에는

- Tensor의 Shape 변경
- Reshape
- View
- Squeeze
- Unsqueeze

등 Tensor를 더욱 자유롭게 다루는 방법을 공부할 예정이다.

꾸준히 학습하면서 AI 프로젝트를 직접 만들 수 있는 수준까지 성장하는 것이 목표이다.