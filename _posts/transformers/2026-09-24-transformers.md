---
layout: post
title: "Introduction to transformers"
author: "Jayadeep"
categories: [transformers]
tags: [documentation,sept26]
image:
---

After the NLP basics, I started learning about transformers. In this section I go on a deep dive into why transformers, their architecture and what happens in the encoder and decoder.

I am also working on a project. since I am searching for a job. I am trying to compare my cv with online job postings, using transformers and NLP basics, to automate finding a suitable job posting.

* TOC
{:toc}

## Why Transformers?
- When transformers are not invented before 2017. All the state of the art language models are based on RNN's (ex: seq2seq model). They have a major weakness of these models are the ability to parallelize along the time axis and vanishing gradient problem.
- Weakness1: The models hidden state 'n', depends on the output from hidden state 'n-1'. This introduces the bottle neck for computation time. The training time is scaled linearly with the sequence length.
- When working on gpus that are highly optimised for parallel computing in the modern day, this inability to parallelize the computation is huge waste of resources.
- Weakness 2:  The input sequence is squeezed by the encoder into a single fixed width context vector. Meaning before the decoder sees  the input sequence, it has already gone through n number of non-linearities. The meaning from to say 50 tokens back has been compressed 50 times through non linearities.
- Theses weaknesses were overcame by a paper in 2017 "Attention is all you need". It proposed that every position attend to every other position simultaneously (parallelly).
- **One big Matrix multiplication** can be performed instead of number of sequences
- This enables that training can be done much faster and can predict accurate outputs because the each word is attended by every other word simultaneously preserving more meaning from the input sequence.
- ![rnnvstransformer]({{"/assets/img/transformers/rnns vs transformer.png" | relative_url}})

## Architecture
- The architecture of a transformer is also a encoder decoder setup.
- During training the model will compute a matrix with the static word embeddings for each word. This special matrix is called **'STATIC WORD EMBEDDING MATRIX'**. 
- There are three other matrices that are generated during training. $ W_q,W_k,W_v $.
- The embedding matrix is representation of each vocabulary in mathematical vector form. They are assigned that particular vector by training the transformer on a huge training set and set the weights of the transformer for generating accurate embedding vectors for each word in the input sequence.
- A static word embedding matrix is of different dimensions with different transformer based architectures. Ex: BERT from google uses 768 dimensional vector to represent each word. While GPT from openAI uses 12228 dimensional vector for each word.
    > The dimension of a vector is nothing but the number of rows each vector contains. Each row represents a specific attribute of that word.
- ![vector-dimensions]({{"/assets/img/transformers/vector_dimensions.PNG" | relative_url}})

## Encoder
- Goal: To generate a context vector by taking the input sequence.
- Then pass the context vector to the decoder and then predict the next word.
- Step 1: Tokenize the input sequence
- Step2: Add Id's to each token. Then, the static word embeddings for each token is generated.
- Step3: Add a positional encoding to each static word embedding. In the proposed paper, they also proposed a formulae to get the positional encoding to each the token.
- Step4: Calculate the Context vector using the three matrices $"W_q, W_k, W_v"$. These are the **Query, Key and Value** matrices. There is a special block in the architecture to do this step, called an 'ATTENTION HEAD'. (There could be multiple attention head in a transformer.)
### QUERY, KEY and VALUE:
- In simple terms these three can be explained with an example.
> - Ex: In a library, we want to borrow a book called " Statistics for Data Scientist"(VALUE). We ask the librarian the QUERY, "I want to borrow the book Statistics for Datascientist".
> - Then the librarian searches for KEYs such as rows with the specified key words such as "computer science", "Math", "Datascience".
> - When we compare the QUERY and KEY to find the similarities that represent the Value more. We go to the row with highest possibility to find the specific book. In this case we choose the row in the library with the KEY 'Data science'. And now we search for the VALUE (book) and find the specific book.
- In transformers, the similarity between QUERY and KEY can be found with **cosine similarity**.
- Calculating QUERY vector for each token in the Input sequence.
    - For each Static encoding $"E_n"$ vector, the Query matrix $"W_q"$ (fine-tuned during training of the transformer) is multiplied to produce the QUERY vector $'Q_n'$.
    - The dimensions of these vectors and matrices depend on the specific transformer used as mentioned above.
    - ![Q_n_calculation]({{"/assets/img/transformers/Q_n_calculation.PNG" | relative_url}})
- Calculating Key vectors for each token.
    - For calculating the Key vectors we follow the same process as in $'Q_n'$ but we use the Key matrix $"K_n"$.
- Once both the QUERY and KEY vectors for each token is generated, we find the similarities between each vector. We use dot product for this step.
- For a specific token in a  sentence, Ex: I made a sweet Indian rice dish called ... For the token dish we calculate: $(QK^T)$. i.e. $"k_1.Q_7, k_2.Q_7, k_3.Q_7......K_8.Q_8"$.
- The above step gives the unnormalized scores. To make the calculations stable we normalise these scores and pass through softmax function. We get the normalised probabilities. $(Softmax(QK^T))$.
- ![unnormalised]({{"/assets/img/transformers/unnormalised.PNG" | relative_url}})
- This step is repeated for all the tokens. This gives the probabilities for all the tokens that are more relevant to the current query (current token).
- We can now weigh all the tokens by multiplying the VALUE vector produced by multiplying $W_V$ and $E_n$ to get $V_n$
- Finally the probabilities can be weighed by $(softmax((Q.K^T).V))$.
- The final **context vector** can be generated by adding all the vectors. This is also called as "CONTEXT AWARE EMBEDDING" $(E_c)$
- A specialised formulae is $Attention(Q,K,V) = softmax(\frac{QK^T}{\sqrt(d_k)}.V)$
- $'d_k'$ is the dimensions of the key vector to scale the calculations down and increase the stability.
### Multi Head Attention:
- The above calculation is all done in one attention head. There could be multiple attention heads running in parallel.
- Then the final context aware embedding is the average of all the $"E_c,n"$ from these attention heads.
- For example, each attention head could be working or focusing on different contexts like verbs, adjectives, cultural....
This improves the contextual understanding of each token
- ![multi head attention]({{"/assets/img/transformers/multi head attention.PNG" | relative_url}})

## Decoder:
- The decoder processes one token at a time. It is responsible for generating target sequence. 
- It uses the masked multi self-attention head to generate text sequentially.
- It uses the previous token and the current token to generate text but not the future tokens.
- Then the previous token is used as Query (Q) for the current step. The Key(K) and Values(V) come from Encoder. This is done in Encoder-Decoder cross Attention
