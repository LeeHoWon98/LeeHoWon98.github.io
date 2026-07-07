---
layout: post
title: "[AI Day 02] Tensor의 Shape와 Reshape 이해하기"
date: 2026-07-07
categories: [AI, PyTorch]
tags: [AI, DeepLearning, PyTorch, Tensor, Shape, Reshape]
---

# Tensor의 Shape와 Reshape 이해하기

지난 글에서는 PyTorch의 기본 자료형인 **Tensor**와 기본 연산을 살펴보았다.

이번에는 Tensor를 자유롭게 다루기 위해 꼭 알아야 하는 **shape, reshape, view, squeeze, unsqueeze, device**에 대해 공부했다.

---

# Shape란?

`shape`는 Tensor의 **각 차원의 크기**를 나타내는 속성이다.

예를 들어

```python
import torch

A = torch.tensor([[1,2,3],
                  [4,5,6]])

print(A.shape)
```

출력

```python
torch.Size([2,3])
```

이는

- 행(Row) : 2
- 열(Column) : 3

을 의미한다.

즉, `shape`는 Tensor가 어떤 구조를 가지고 있는지 알려준다.

---

# dtype이란?

`dtype`은 Tensor 내부 원소들의 **자료형(Data Type)** 을 의미한다.

```python
print(A.dtype)
```

출력 예시

```python
torch.int64
```

대표적인 자료형은 다음과 같다.

| dtype | 설명 |
|--------|------|
| torch.int64 | 정수형 |
| torch.float32 | 실수형 |
| torch.bool | 논리형(True/False) |

---

# reshape()

`reshape()`는 Tensor의 모양을 변경하는 함수이다.

```python
import torch

A = torch.arange(12)

print(A)
```

출력

```python
tensor([0,1,2,3,4,5,6,7,8,9,10,11])
```

모양을 변경하면

```python
B = A.reshape(3,4)

print(B)
```

출력

```python
tensor([[0,1,2,3],
        [4,5,6,7],
        [8,9,10,11]])
```

---

## reshape() 사용 시 주의할 점

원소의 개수는 반드시 동일해야 한다.

가능한 경우

```python
.reshape(3,4)
.reshape(2,6)
.reshape(4,3)
```

불가능한 경우

```python
.reshape(5,5)
```

원소 개수가 맞지 않기 때문에 오류가 발생한다.

---

# view()

`view()`도 Tensor의 모양을 변경하는 함수이다.

```python
A = torch.arange(12)

B = A.view(3,4)
```

`reshape()`와 매우 비슷하지만 차이점이 있다.

| reshape | view |
|-----------|------|
| 필요하면 새로운 메모리를 생성 | 기존 메모리를 공유 |
| 대부분의 경우 사용 가능 | 연속된(Contiguous) Tensor에서만 가능 |

실무에서는 둘 다 사용하지만, 처음에는 `reshape()`를 사용하는 것이 이해하기 쉽다.

---

# unsqueeze()

`unsqueeze()`는 **차원을 하나 추가하는 함수**이다.

예를 들어

```python
A = torch.tensor([1,2,3])
```

shape는

```python
torch.Size([3])
```

이다.

### unsqueeze(0)

```python
A.unsqueeze(0)
```

shape

```python
torch.Size([1,3])
```

### unsqueeze(1)

```python
A.unsqueeze(1)
```

shape

```python
torch.Size([3,1])
```

차원을 추가하는 위치에 따라 결과가 달라진다.

---

# squeeze()

반대로 `squeeze()`는 **크기가 1인 차원을 제거**한다.

```python
A = torch.tensor([[1,2,3]])

print(A.shape)
```

출력

```python
torch.Size([1,3])
```

이후

```python
A.squeeze()
```

출력

```python
torch.Size([3])
```

---

# device

Tensor는 CPU뿐 아니라 GPU에서도 계산할 수 있다.

```python
device = "cuda" if torch.cuda.is_available() else "cpu"

A = torch.tensor([1,2,3]).to(device)

print(A.device)
```

출력 예시

```python
cuda:0
```

또는

```python
cpu
```

`cuda`는 Tensor가 GPU에서 연산되고 있다는 의미이다.

---

# 실습 코드

```python
import torch

A = torch.arange(12)

print("원본")
print(A)

print(A.reshape(3,4))

print(A.reshape(2,6))

print(A.reshape(4,3))

B = torch.tensor([1,2,3])

print(B.unsqueeze(0))

print(B.unsqueeze(1))

C = torch.tensor([[1,2,3]])

print(C.squeeze())
```

---

# 오늘 공부하면서 느낀 점

오늘은 Tensor의 모양을 자유롭게 변경하는 방법을 배웠다.

특히 `reshape()`는 데이터를 다른 형태로 표현할 때 매우 자주 사용되며, `unsqueeze()`와 `squeeze()`는 딥러닝 모델의 입력 형태를 맞출 때 자주 사용된다는 것을 알게 되었다.

또한 `shape`는 Tensor의 구조를, `dtype`은 Tensor 내부 데이터의 자료형을 의미한다는 점을 다시 정리할 수 있었다.

---

# 마무리

오늘 배운 내용은 앞으로 CNN, Transformer 등 다양한 딥러닝 모델을 구현할 때 계속 사용되는 기본 개념이다.

다음 글에서는 **PyTorch의 자동 미분(Autograd)** 을 공부하며, 모델이 어떻게 스스로 학습하는지 알아볼 예정이다.