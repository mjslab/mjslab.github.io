---
title: "논문 리뷰: Attention Is All You Need"
date: 2025-01-27 20:15:00 +0900
categories: [Research, Paper Review]
tags: [논문리뷰, Transformer, 딥러닝, NLP, Attention]
author: mjs
description: "딥러닝 분야에 혁신을 가져온 Transformer 아키텍처 논문을 리뷰합니다."
math: true
---

## 논문 정보

**제목:** Attention Is All You Need  
**저자:** Ashish Vaswani et al. (Google Brain)  
**발표:** NIPS 2017  
**링크:** [arXiv:1706.03762](https://arxiv.org/abs/1706.03762)

## 연구 배경

기존 sequence-to-sequence 모델들은 주로 RNN이나 CNN 기반이었습니다. 하지만 이들은 다음과 같은 한계가 있었습니다:

- **순차적 처리**: RNN의 경우 병렬화가 어려워 학습 속도가 느림
- **장거리 의존성**: 긴 시퀀스에서 정보 손실 발생
- **계산 복잡도**: 시퀀스 길이에 따른 계산량 증가

## 핵심 아이디어

### 1. Self-Attention 메커니즘

Transformer의 핵심은 **Self-Attention**입니다. 각 위치의 토큰이 시퀀스 내 모든 위치와 직접적으로 연결됩니다.

$$\text{Attention}(Q, K, V) = \text{softmax}\left(\frac{QK^T}{\sqrt{d_k}}\right)V$$

여기서:
- $Q$ (Query): 현재 위치에서 찾고자 하는 정보
- $K$ (Key): 각 위치의 식별자
- $V$ (Value): 실제 전달할 정보

### 2. Multi-Head Attention

단일 attention 대신 여러 개의 attention head를 병렬로 사용:

$$\text{MultiHead}(Q, K, V) = \text{Concat}(\text{head}_1, ..., \text{head}_h)W^O$$

각 head는 서로 다른 representation subspace에서 정보를 학습합니다.

### 3. Position Encoding

RNN 없이 위치 정보를 주입하기 위해 sinusoidal position encoding 사용:

$$PE_{(pos, 2i)} = \sin(pos/10000^{2i/d_{model}})$$
$$PE_{(pos, 2i+1)} = \cos(pos/10000^{2i/d_{model}})$$

## 아키텍처 분석

### Encoder Stack
```
Input Embedding + Position Encoding
↓
Multi-Head Attention
↓
Add & Norm
↓
Feed Forward Network
↓
Add & Norm
```

### Decoder Stack
```
Output Embedding + Position Encoding
↓
Masked Multi-Head Attention
↓
Add & Norm
↓
Multi-Head Attention (with Encoder)
↓
Add & Norm
↓
Feed Forward Network
↓
Add & Norm
↓
Linear & Softmax
```

## 실험 결과

### 1. 기계 번역 성능

| 모델 | WMT 2014 En-De | WMT 2014 En-Fr |
|------|----------------|----------------|
| ConvS2S | 25.16 | 40.46 |
| GNMT | 24.61 | 39.92 |
| **Transformer** | **28.4** | **41.8** |

### 2. 계산 효율성

- **학습 시간**: 기존 모델 대비 약 10배 빠름
- **병렬화**: GPU 활용도 크게 향상
- **메모리 효율성**: 긴 시퀀스 처리에서 우수

## 장점과 한계

### 장점
1. **병렬화 가능**: 전체 시퀀스를 동시에 처리
2. **장거리 의존성**: 직접적인 연결로 정보 손실 최소화
3. **해석 가능성**: Attention weight 시각화 가능
4. **확장성**: 다양한 태스크에 적용 가능

### 한계
1. **메모리 사용량**: Attention matrix가 시퀀스 길이의 제곱에 비례
2. **Position Encoding**: 고정된 길이 제한
3. **Inductive Bias 부족**: CNN/RNN 대비 구조적 편향 적음

## 영향과 후속 연구

### 직접적 영향
- **BERT** (2018): Encoder만 사용한 양방향 모델
- **GPT** (2018): Decoder만 사용한 생성 모델
- **T5** (2019): Text-to-Text Transfer Transformer

### 개선 방향
- **Sparse Attention**: 계산 복잡도 감소
- **Relative Position**: 더 유연한 위치 인코딩
- **Efficient Transformer**: 메모리 효율성 개선

## 구현 예제

```python
import torch
import torch.nn as nn
import math

class MultiHeadAttention(nn.Module):
    def __init__(self, d_model, n_heads):
        super().__init__()
        self.d_model = d_model
        self.n_heads = n_heads
        self.d_k = d_model // n_heads
        
        self.W_q = nn.Linear(d_model, d_model)
        self.W_k = nn.Linear(d_model, d_model)
        self.W_v = nn.Linear(d_model, d_model)
        self.W_o = nn.Linear(d_model, d_model)
        
    def scaled_dot_product_attention(self, Q, K, V, mask=None):
        scores = torch.matmul(Q, K.transpose(-2, -1)) / math.sqrt(self.d_k)
        
        if mask is not None:
            scores = scores.masked_fill(mask == 0, -1e9)
            
        attention_weights = torch.softmax(scores, dim=-1)
        output = torch.matmul(attention_weights, V)
        
        return output, attention_weights
```

## 결론

Transformer는 **"Attention Is All You Need"**라는 제목처럼, attention 메커니즘만으로도 강력한 sequence modeling이 가능함을 보여줬습니다. 

이 논문은:
- NLP 분야의 패러다임을 완전히 바꿨습니다
- 현재 대부분의 SOTA 모델들의 기반이 되었습니다
- CV, Audio 등 다른 분야로도 확장되고 있습니다

현재까지도 가장 영향력 있는 딥러닝 논문 중 하나로 평가받고 있으며, 앞으로도 계속 발전할 것으로 예상됩니다.

## 추천 후속 논문

1. **BERT**: Pre-training of Deep Bidirectional Transformers
2. **GPT-3**: Language Models are Few-Shot Learners  
3. **Vision Transformer**: An Image is Worth 16x16 Words
4. **Reformer**: The Efficient Transformer

test4

---

*이 논문에 대한 추가 질문이나 다른 논문 리뷰 요청이 있으시면 댓글로 남겨주세요!*