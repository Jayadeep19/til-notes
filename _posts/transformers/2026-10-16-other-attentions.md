---
layout: post
title: "Other Attention Mechanisms"
author: "Jayadeep"
categories: [transformers]
tags: [documentation,oct26, types of attention]
image:
---

In the previous blog attention mechanisms closely related to kv cache are discussed. In this blog let us explore attention mechanisms that change the topology of the attention matrix itself. 

* TOC
{:toc}

- The attention mechanisms that we discussed previously does not change the arithimetic cost, every token still attend to every other token.
- The compute and memory required for a sequence of length N is still $O(N^2)$

## Sliding window attention:
- As the name suggests, we use a sliding window to restrict a tokens attention to a fixed size around its position.
- Window of size 2 and attention to tokens only in window
- ![SWA]({{"/assets/img/transformers/sliding window attention.PNG" | relative_url}})

- So, by using this attention what is the quality loss of our transformer?
    - It does losequality as we reduce the attention window in exchange for reduction of huge computation cost. To have a balance between quality and computation speed, a mix of swa layer and full attention layers can be used. Gemma 3 uses 5:1 mix of SWA:Global layers.

## Sparse/Block attention:
- A sparse atention is a kernel engineering task. We mask the score matrix and never load those zeros into the SRAM. This is the main advantage of the sparse attention.
- Ex: Deepseek, 2025 introduced this attention mechanism. They found that the score matrix from a full attention is almost all zeros except for the tokens nearby. so we mask the zeros and only attend to a choosen subset of tokens.All other connections are masked.
- The math is almost similar to Scaled dot product attention except we add a mask before we apply softmax.

- $$A = \frac{Q \cdot K^T}{\sqrt{d_k}} + M$$
- $$score = softmax(A)$$
- $$out = A \cdot V$$
- Where 'M' is the mask and $$M \in \{0, -\infty \} $$
- The matrix M has elements has value '0' to all the allowed positiions and '$-\infty$' to all the disallowed positions.
- The $-\infty$ when passed through the softmax becomes zero leaving only the terms with '0' in the mask matrix in the final output.
- This drops the complexity from $O(N^2)$ to $O(N.K)$ where k is the number of keys in the matrix. 

- **Variants of the sparse attention:**
    - *Sliding window* - Mistral uses this architecture
    - *strided/ Dialated* is another variant.
        - we skip a fixed stride in the sliding window mechanism. With a slide 2 and a window size of 2.
        - For the example shown in the sliding window above, with a stride 2 for token 4 the attention is focused on the token 1 and token 7. 
        - This gives a long reach at low cost. Useful especially when the sequence is longer.
        - alternating between local and strided variants between different layers gives long and local reach for the transformer 
    - *Global token* : (Longformer)
        - Tokens like [CLS] attended to every other token and each token attends to this global token for score calculation.
        - They act as an information hubs gathering info across the whole sequence.
    - Big bird is another variant (Blocks + global + random)

## Differential attention
- Regular attention has a 'focus problem' aka 'attention sink'.
- A normal attention has a softmax that attends/gives a non-zero score to every token. Meaning the score after the softmax is spread over every token with a non zero value.
- For short sequences this is not a problem. However, when the sequence is large the small non important and non zero scores pile up and mask the important scores of the important tokens.
- ![differential attention]({{"/assets/img/transformers/differential attention.PNG" | relative_url}})
- This differential transformers we calculate two maps(that share the noise) and subtract them from each other to cancel this non important noise.
- $$ A_1 = softmax(\frac{Q_1 \cdot K_1^T}{\sqrt{d_k}})$$
- $$ A_2 = softmax(\frac{Q_2 \cdot K_2^T}{\sqrt{d_k}})$$
- $$DiffAttn = (A_1 - \lambda \cdot A_2)$$
- where $\lambda$ is a learned scalar to control how much cancellation takes place.
- The output is a clean focused attention matrix, that can make the model less hallucinate and sharper.