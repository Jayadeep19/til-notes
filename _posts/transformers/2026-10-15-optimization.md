---
layout: post
title: "Transformer optimization (KV cache)"
author: "Jayadeep"
categories: [transformers]
tags: [documentation,oct26, types of attention]
image:
---
In this blog I explained the optimization techniques that are developed for an efficient transformer to balance the cost of computation, memory bottleneck with the quality of the transformer output. The topics covered are:
* TOC
{:toc}

- A transformer with a 70B parameters activate all the parameters everytime for every token.
- This costs huge computation time and increases the cost. To mitigate this to an extent several optimization techniques are developed.

## Mixture of Experts (MoE):
- The idea is to activate only the relevant parameters instead of activating all the parameters. Example a 671B parameter MOE transformer only activates 37B parameters per token. Making it faster than the 70B parameter transformer.
- MoE replaces each FFN with 'E' independent experts.
- A **router** decides how many experts (k) to pick for activation per token.
    - Now the Total parameters = E * FFN_size
    - Active parameters = k * FFN_size
    - Typically E = 256 and k = 8.
- A problem arrives when the router passes 90% of the tokens through only a few experts, the other experts starve. There are a few fixes that are proposed: 
    1. Auxiliary load balancing loss
    2. Expert capacity token dropping
    3. Auxiliary loss free balancing

## BottleNecks in transformers:
- There are two important types of **memories in a GPU**:
    1. High Bandwidth Memory (HBM): This is big memory but is slower. It is where the model weights, KV vectors from the previous tokens are stored.
    2. SRAM (Static Random-Access Memory): The actual memory that sits on the chip itself, where the actual computations carries on.
- Since this SRAM is small, during the computation the required vector, matrices, weight are carried into the SRAM temporarily, they stay there until the operation is performed.
- The problem here during the transformer training and inference is pretty obvious here. 
    - Training is a parallel job as we have seen, it is a big matrix multiplication which can be performed parallelly. It is also FLOP (Floating point operation)-bound. Meaning it is bound by the amount of computation a GPU can perform.
    - While Inference is a serial job and is memory bound. Why?
        - As in the memory hierarchy, the required weights, vectors has to be carried to the SRAM everytime a work has to be predicted. The actual calculation on the SRAAM is very fast however, the core waits most of the time that it actually performs the calculation.
- The Naive Auto regressor decoder does $$O(N^2)$$ operations to generate N-tokens. Meaning, to generate 4 tokens it performs 16 attention operations (Remember Scaled Dot product!!)
- On top of this decoder computations, the attention itself moves a lot of data to and from HBM. This could easily make attention calculation memory bound way before compute bound.

-  Fortunately, several optimization techniques are proposed during the years to offset these bottle necks. They are:
1. Flash attention
2. KV cache

## Flash Attention:
- The attention scores are computed every time a new token comes in (happens in SRAM). This involves moving the Q K, V matrices in and out everytime. Three trips necessary to HBM for attention calculation

```python
S = Q @ K^T    (HBM read, N*N, HBM write),
P = softmax(S) (HBM read, HBM write)
O = P @ V      (HBM read, HBM write)
```

- **Flash attention changes how the attention calculation is performed**. Instead of moving these matrices everytime, it **tiles** the attention computation so that the full N*N matrix never hits the HBM and 
- First, the matrices Q, K are transferred to SRAM in tiles. A matrix multiplication of Q and K is performed, followed by applying Softmax, then computing the matrix multiplication with V, and writing the result back to HBM.
- From the second tile onward, the result of the previous V matrix multiplication is retrieved from HBM, scaled appropriately, and the result of the current tile’s V matrix multiplication is added to it.
- As it can be seen that the trips to HBM is reduced to two reads and a write.
- Softmax distribution is calculated once all the values are available, but if we tile the Q,K vectors how do we compute the softmax distribution? It is achieved with something called **Online softmax**
- It keeps a running maximum and ,updating them incrementally as new blocks arrive. This avoids forming the full N*N matrix on the SRAM.
- All operations happen in a single GPU kernel with no intermediate writes to HBM. This is called as **Fused Kernels**

## KV Cache:

- This is the optimization used for our next bottelneck, the memory wall. when we try to predict the next token, we look at all the previous tokens. 
- We calculate K and V vectors for all the previous tokens everytime. This is a redundant step that can be minimised by storing the K and V vectors in the cache.
- For each new word, the model retrieves the stored keys and values and adds the new ones to it, instead of starting over
- ![kvcache]({{"/assets/img/transformers/kvcache.PNG" | relative_url}})
- ![kvcache2]({{"/assets/img/transformers/kvcache2.PNG" | relative_url}})
- The inference reduces from O(N^2) to O(N) per generation step.
- On the other hand, as the number of tokens increase, the more amount of cache it requires as we need to store more number of K and V vectors. 
- KVcache assumes that the sequence is huge (even when little in actual) and allocates a huge chuck of memory for each generation step.
- This unused memory sits idle and is wasted, to fix this **Paged attention** allocates a 16 batch token blocks worth of memory randomly.
- A page table maps logical positions to physical blocks. Lets us share KV across parallel samples.
> example: (KV cache vs Paged attention)
> - Imagine there is an apartment (our memory) and the customers who wants to buy the flats are our tokens.
> - Normal KVCache asks each customer to book the whole flat even when they only want 1 or 2 rooms. This causes unused spaces(memory in our case).
> - Paged Attention lets the customer choose any particular room randomly anywhere in the apartment. We keep a track of these booking via a register (page table).
> - In special cases, it can even let you share the booked room between different customers. For instance, if they are relatives or friends.

### Types of Attentions

|**Core and classic**|**Efficiency and kv optimization**|**Topology and sparse variations**|
|-------|--------|---------|
|Scaled dot product attention|Grouped Query attention(GQA)|Sliding window attention|
|Self-attention|Multi query attention(MQA)|Sparse / Block attention|
|Multi head attention|Multi head latent attention(MLA)|Linear attention|
|Cross attention|Flash attention|Differential attention|
||Paged attention||

- we have already seen Flash attention and Paged attention and since we are discussing about KV cache, I will write about the attentions that are closely related to kv cache.
- But in order to discus those, first we need to discuss Multi Head attention (MHA)

#### Multi Head Attention (MHA):
- Remember the [Scaled dot product attention]({{site.baseurl}}{% post_url /transformers/2026-09-24-transformers %}#scaled-dot-product-attention){:target="_blank"}, it is a big matrix multiplication that can be done parallelly in a gpu.
- However, we can use Multi Head attention to basically split the matmul into smaller chunks and through different heads.
- In MHA we work in the subspace of the number of heads, meaning for the same computation the context becomes enriched compare to a single big matrix multiplication, especially when the input sequence is long. 
- ![MHA]({{"/assets/img/transformers/MHA.PNG" | relative_url}})
- **Self attention vs cross attention**:
    - Self attention when the input sequence is one and each token attends to other token
    - Cross attention is when there are two sequences and tokens in one sequence attend to another sequence. Example translation

#### Grouped Query Attention (MQA):
- In multi head attention the K and V values are mostly redundant. meaning most of them repeats the same in each head.
- So In MQA, the Queries share the same K and V vectors across. So for a 4 headed attention and for each token, we only store 2 sets of vectors in the cache. Follow below example

|MHA|MQA|
|---|---|
|For every token, **we store 4 separate K vectors and 4 separate V vectors**|For every token Mqa **store only two vectors, 1K and 1V**. Every head shares the same K and V vectors for this token|
|For every token: 4K + 4V|Meaning: 8-2/8 = **75% storage space is saved**.|
||This also leads to faster inference. Because we need to read less date from the HBM for every decoding step|

- So, what is the quality loss for using the MQA:
    - Quality loss is measured by a metric called "Perplexity". It is the number that indicate how confident the model is in predicting the next token
    - Perplexity  = 1 meaning the model is very confident
    - Perplexity = 10 meaning the model is not confident at all
- It is observed that the increase in perplexity is very low when MQA is used instead of classic MHA
- ![MQA]({{"/assets/img/transformers/MQA.PNG" | relative_url}})

#### Grouped Query Attention (GQA):
- Instead of forcing all the queries to use the same key and value vectors, we make some heads share the same K,V while the other groups of heads share different K,V.
- This set up lets the user to tweak the properties of the transformer in between MHA and MQA.
- There is grouping hyper parameter "G", set this number to decide how many groups in the transformer.
- Turn 'G' up and you force more heads into a single group achieving MQA characteristics, while tuning it down brings the character towards MHA
- ![GQA]({{"/assets/img/transformers/GQA.PNG" | relative_url}})