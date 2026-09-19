---
layout: post
title: "NLP-basics"
author: "Jayadeep"
categories: [NLP]
tags: [documentation,sept26]
image:
---

For the past few months I have been busy. I have to move from Germany to India permenantly. Now that I have started to focus again, I am learning AI Engineering, starting with the foundations of NLP (Natural language Processing). In this blog. I will go through concepts like:

* TOC
{:toc}

- Machines and humans read and process text differently. Humans read words but machines read text as numbers. So when we want a machine (Ex: models) to process text, first step we need to do is to convert text into something that a machine can process.

## Text Processing
- There are 2 important things that a NLP systems opens with. 
    - Where does the word start? what is the root word?
    - How do we treat similar words same when it helps and treat them different when it doesnt help?
- The first question is answered by the concept called **Tokenization**
    - It is the process of splitting text into smaller units called **tokens**
    - These tokens are deliberatly described vaguly as they can be, words, characters (for languages without white spaces), sub words
    - This also helps to convert unstructured format into structured format
- For exaple: The sentence "I am Iron man", when broken down into tokens become ['I', 'am', 'Iron', 'man'].
- Types of tokenizations:
    - word tokenization: Common method, where text is divided into individual words
    - Character Tokenization: This is helpful when the language is without a clear boundaries and without white spaces. Each character is considered as a seperate token (Ex: Chinese, Japanese)
    - Sub-word Tokenization: strikes a balance between word and character
    - Sentence Tokenization: when there is a large document, it is often helpful to split sentences into tokens.  
- Libraries like NLTK(Natural Language Tool Kit) and SpaCy offer tools for many linguistic tasks.

## Stemming
- when a word has different forms like "Run", "Running". It is often mean similar in content and it is helpful to reduce them into their base form by removing their suffixes.
- It helps improve tent processing and analysis efficient
- It is fast, aggressive and dumb
- Ex: Running -> Run
    organisation -> organ (failure).
- Several stemmer's are available in NLTK library.

## Lemmatization:
- Lemmatization is a technique in NLP to reduce a word to its base (or) dictionary form
- It takes meaning and part of speech into account while reducing the word to its base form
- Due to this reason, it is more accurate than "stemming"
- It is slower, accurate and needs a look-up-table.
- when Pos (Part of speech) (described below) tag is associated with each wold then the accuracy of the Lemmatizes increases. - By default it assumes every word is noun, but to Lemmatize a verb, Pos tagging it is necessary.\

## POS Tagging:
- It is the concept used for tagging the word for an accurate lemmatization.
- It is a fundamental task in NLP, where we assign each word with its gramatical categody (verb, noun, adjective, adverb)
- Now a days, this task is basically a 'classification of token' task by a NN on the top of a pre trained transformer. 
- But before the development of Deep Learning, classic NLP handled the task using a rule based tagging based on predefined grammatical rules.
- We can use NLTK and SpaCy libraries to download pretrained models to POS tag our text. These models are trained using large datasets and ML techniques.

## Bag of words (Bow), TF-IDF. Text Representation:
- To convert words that a machine can understand, we need numbers.
- How to convert a variable length Stream of tokens into fixed size numerical form?
- Bag of words is a simple answer that works. It simply counts the number of words and make a vector.
- Each document is represented as a vector where each element shows the frequency of the words from the vocabulary in that document.
TF-IDF
- It reweighs Bow. A word that appears in every document is uninformative. so scale it down. - A word that rarely occurs but frequent in single document is signal. so scale it up.

## Word Embeddings:
- BoW and TFIDF doesn’t know the difference between "Dog" and "puppy". They create different vectors for each word. Word2Vec represents words as vectors in a continuous space.
- We need a representation where similar words with similar meaning falls closers in space. It captures semantic relationships between words.
- Word2Vec gives that space. It is a 2layer neural network with a trillion token training runs  published in 2013.
- Gensim library can be used to implement the following two architectures.
- There are two main architectures:
    1. CBOW (Continuous bag of words):
        - The CBOW models predicts the current word given context word within a specific window. 
        - The input layer has the context words, output layer has the current predicted word.
        - The **hidden layer** contains the dimension that we want to represent the current word in.
        - Input[Cat] -> output[(the, sat, on)]
    2. Skip gram:
        - It is the opposite of the CBOW.
        - It predicts the context words given the current word within the specific window.
        - Again, the hidden layer contains the number of dimensions that we want to represent the current word in the Input layer
        - Input[(the, sat,on)] -> Output[cat]
- These weights of the hidden layer are called "Embeddings".

## Seq2Seq model:
- The classification task (ex: word2vec) maps a sequence to fixed length vector. But for tasks like translation from one language to another, a variable length sequence has to be mapped to another variable length sequence.
- This was achieved by a simple model called seq2seq (sequence to sequence) model.
- This model has an architecture of an encoder-decoder with **RNN's** 
- Encoder:
    - This RNN processes the input sequence token by token.
    - Encodes entire sequence into a fixed length **context vector**, this context vector has the summary of the input sequence
    > Context Vector: A fixed size numerical representation that summarizes the entire meaning of the I/p sentence and acts as an information bridge between encoder and decoder.
    - The hidden state (ht) is updated at each time step by combining the current input with the previous hidden state at h(t−1)
    - $$h_t  = \phi{}(W_{hh} h_{t−1}+W_{xh} h_t )$$
    - $h_t$ is the hidden state at timestamp $t$ for the encoder
    - $\phi{}$ is the activation function
    - $W$ is the weight matrix of the network.
    - The final hidden state of the encoder becomes the context vector or some times called thought vector.
- Decoder:
    - Takes the context vector as input
    - Generates the output token by token. The next token is predicted based on the context vector.
    - The decoder input and the initial hidden state $s_0$ is the final context vector from the encoder. hfinal   and generates the output token $y_t$ at a time
    - At  each decoding step, the hidden state is updated 
    - $$s_t  = \phi{}(W_{ss} s_{t−1}+W_{ys} y_{t−1} )$$
    - $s_t$ is the hidden state at time $t$ for the decoder
- To predict the actual output token, the current hidden state weights are multiplied by the massive weight matrix of the target vocabulary. This produces the raw score for each word in the target text and then a softmax function converts these raw scores into a probability dist over all the target vocabulary.
![seq2seq]({{"/assets/img/transformers/seq2seq.PNG"|relative_url}})

## Attention Mechanism:
- When the length of the input sentence increases from 5 words to 80 words, the seq2seq model performance drops drastically. 
- This is due to the fact that the context vector has to fit all the information into a fixed size vector. The more the encoder summarizes the more difficult it is for the decoder to generate the output accurately.
- This is where attention mechanism comes in, we now not only pass the final hidden state to the decoder, we keep all the encoder hidden states.
- The decoder at every step computes a weighted average of all the encoder hidden states. This weighted average is now the context vector.
- It also allows the models to focus on the most important parts of the input data by assigning different weights to different elements, this helps to prioritise relevant information and generate next word with more accuracy.
- Encoder: 
    -  Processes the input sequence and converts into a series of hidden states.
    - For example, we have an input with tokens $$x_0,x_1,x_2,x_3$$, the encoder generates the hidden states $$h_0,h_1,h_2,h_3$$. 
    - The hidden states capture the information from both the current input and the previous hidden state: $$h_t=f(h_{t−1},x_t)$$
- Attention Mechanism:
    - Attention mechanism determines how much each encoder hidden state is important to generate a particular word in the output.
    - The main goal of the attention mechanism is to generate a context vector $C_t$, with the most relavent information.
    - First, the decoder current hidden state $s_t$ and each encoder hidden state hi are used to compute a alignment score. $e_{t,i}=g(s_t,h_i)$
    - This is the score which is then normalised using a softmax function to produce attention weights (probabilities) indicating the importance of each encoder hidden state. Meaning: the more weight a hidden state has the more relevant it is for the current word generation.
    - $\alpha_{t,i}=softmax(e_{t,i})$
    - This probabilities are then used to generate a context vector. It is a weighted sum of all the encoder hidden states. Meaning: taking the probabilities we weigh each encoder states and sum them, this ensures that the relevant hidden states get more weight than others.
    - $C_t= \sum_{i=0}^{T_i}{\alpha_{t,i},h_i}$
- Decoder:
    - Now that the context vector is generated, the decoder uses its own previous hidden state along with the context vector to generate the next output word.
    - $y_t=g(y_{t−1}, S_t,C_t)$