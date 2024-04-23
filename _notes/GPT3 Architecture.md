---
title: GPT3 Architecture
date: 2023-03-08
tags: NLP Attention Decoder GPT
---

# GPT3 Architecture



- What is GPT3 and its architecture? : Answer from ChatGPT, one of children of GPT3
	- GPT-3 (==Generative Pre-trained Transformer 3==) is a language model developed by ==OpenAI==, which is designed to generate human-like text based on its training data. It is one of the largest and most powerful language models created to date, with ==175 billion parameters==.
	- GPT-3 uses a neural network architecture called a ==transformer==, which enables it to process and ==understand language== in a way that is similar to how humans do. This allows it to perform a variety of natural language processing (NLP) tasks, such as language translation, text summarization, and question-answering, among others.
	- GPT-3 has received a lot of attention due to its ability to ==generate highly convincing and coherent text==, which has many potential applications in areas such as ==content creation, chatbots, and virtual assistants==. However, it has also raised concerns about the potential misuse of such powerful language models, particularly in the areas of fake news and propaganda.

- GPT as a Children of the Transformer?
	- Basically, hugely ==augmented decoder stack== of transformer introduced in [[Attention is All You Need]]
	- In GPT, like traditional language models, ==outputs one token at a time==.

![[Pasted image 20230308062034.png]]
<figcaption> GPT-2 vs BERT vs Transformer XL by Jay Alamer </figcaption>

![[Pasted image 20230308063850.png]]
<figcaption> GPT-2 generates one token at one time by Jay Alamer </figcaption>


---

# High Level Description of GPT3

- GPT2 = A stack of ==12 decoders== with ==12 attention heads==
- GPT3 = A stack of ==96 decoders== with ==96 attention heads==

![[Pasted image 20230304081249.png]]
<figcaption> Architectures of GPT and Transformer </figcaption>

---

# Input/Output

![[Pasted image 20230304081430.png]]
<figcaption> Inputs and outputs of language models </figcaption>

- Input : A sequence of N words (a.k.a tokens). 
	- ==2048 words== for GPT-3
	- One input word at one time
- Output: ==A guess for the word== most likely to be put at the end of the input sequence. 

![[Pasted image 20230304081617.png]]
<figcaption> Sequence of language model outputs </figcaption>

- For GPT-3, output is not just a single guess, it's ==a sequence (length 2048) of guesses== (a probability for each likely word) 

![[Pasted image 20230304081725.png]]
<figcaption> Input/Output as a sequence in GPT3 </figcaption>

---

# Encoding of Word Sequence

- GPT3 as a ML algorithm, it operates on vectors of numbers
- How to convert words into vectors?
- ==GPT3's vocabulary = 50257== words
- ==One-hot encoding==: One word = 50257-dim vector with only one element is one and all zero for the other elements

![[Pasted image 20230304082248.png]]
<figcaption> One-hot encoding </figcaption>

- ==2048 words== $\rightarrow$ Matrix with ==2048x50257== Binary matrix

![[Pasted image 20230304082349.png]]
<figcaption> A sentence to a binary matrix using one-hot encoding </figcaption>


- GPT-3 uses byte-level ==Byte Pair Encoding (BPE)== tokenization for efficiency
	- ==Word as token== $\rightarrow$ Groups of characters (sub-words of a word) as token 
	- Check OpenAI's _[tokenizer tool](https://beta.openai.com/tokenizer)_

---

# Embedding of Encoded Word Sequence

- ==50257 dim-vector== is too big! 
- Let's Make it short!
- Need to learn an embedding function

![[Pasted image 20230304082838.png]]
<figcaption> Case when embedding dimension = 2 </figcaption>

- GPT uses ==12288 dimensions== as its embedding dimension!
- How to embed?
	- 2048 word-sequence in a vocabulary with 50257 words
	- $\rightarrow$ 2048x50257 Sequence-encoding matrix $A_E$
	- $\rightarrow$ Multiply with ==50257x12288 Embedding-weights matrix== : ==Learned== parameters! $A_E\times W_E$ 
	- $\rightarrow$ 2048x12288 Sequence-embedding matrix 
- About the matrix multiplication $A_E\times W_E = X_{TokenEmbedding}$
	- Not actually multiplication
	- ==Just simple table lookup==
	- Steps:
		- Get ==index $j$== of the non-zero element of the $i$-th row of Encoding matrix $A_E$
		- Find and ==fetch $j$-th row== of Weight matrix $W_E$
		- Put it in $i$-th row of Embedding matrix $X_{TokenEmbedding}$
- Q: What if embeddings are distributed across the system?
- Q: When is the  50257x12288 Embedding-weights matrix trained? Is it pretrained?

![[Pasted image 20230304083645.png]]
<figcaption> Sequence-encoding matrix to Sequence-embedding matrix </figcaption>

---

# Positional Encoding

![[Pasted image 20230304085755.png]]
<figcaption> The position information of i-th word in 2048 word sequence is sent to 12288 sine functions with different frequencies and converted to 12288-dimensional position vector </figcaption>

- 2048 word's ==position== $\rightarrow$ ==12288 sinusoidal functions==, each with a different frequency
- Since \#sine-frequency > \#words, each position will give unique position vector with relative positional information, just like Fourier transformation does.
- So, this vector can represent 
	- ==Unique== position of each word
	- Periodically ==relative== relations among words
- Now, we have positional encoding matrix $X_{PositionalEncoding}$ with (2048 $\times$ 12288) size

![[Pasted image 20230304090642.png]]
<figcaption> 2048 word information to 2048x12288 positional-encoding matrix </figcaption>

---

# Final Embedding Matrix

![[Pasted image 20230304091130.png]]
<figcaption> Sequance-Embedding matrix + Positional-Encoding matrix  = Final embedding matrix </figcaption>

- ==Final embedding matrix (2048x12288) $X$==
	- = Sequence-Embedding matrix (2048x12288)  $X_{TokenEmbedding}$
	- + Positional-Encoding matrix (2048x12288) $X_{PositionalEncoding}$

---

# Attention (Simplified)

- What is attention?
	- An operation that gives ...
	- Prediction ==which input tokens to focus== on and ==how much important== it is
	- for each output in the sequence

- Attention toy model
	- Sequence of 3 tokens (2048 in GPT3) with 512-dimensional embedding (12288 in GPT3)

![[Pasted image 20230304091803.png]]
<figcaption> Linear projection of input tokens to Q, K, and V </figcaption>

- Step1: Make $Q$, $K$, $V$ using 3 ==linear projection== matrices $W_q, W_k, W_v$
	- Learn 3 linear projections  $W_q, W_k, W_v$
		- 512(Embedding dimension, 12288 in GPT3) x64($QKV$'s columns, 124 in GPT3)
	- Apply them to the sequence embeddings
	- Generate 3 different matrices $Q$, $K$, and $V$
		- $Q$: ==Queries== =  $X\times W_q$
		- $K$: ==Keys===  = $X\times W_q$
		- $V$: ==Values===  = $X\times W_q$

![[Pasted image 20230304100756.png]]
![[Pasted image 20230304100842.png]]
<figcaption> Importance matrix : QK' followed by softmax </figcaption>

- Step2: ==Importance== matrix 
	- Multiply queries and keys 
	- $Q_{(3\times 64)}\times K^T_{(64\times 3)}$ 
		- In GPT3: $Q_{(2048\times 128)}\times K^T_{(128\times 2048)}$ 
	- Apply softmax function to the 3x3 matrix result
	- $Softmax~(Q_{(3\times 64)}\times K^T_{(64\times 3)})_{(3\times 3)}$
	- Meaning?
		- Only interaction between different tokens
		- Which tells the importance of each token to each other tokens

![[Pasted image 20230304100859.png]]
<figcaption> Adjust values with the importance matrix </figcaption>


- Step3: Make ==weighted values== $Y$
	- Multiply the importance matrix to the values
	- $Y=$ $Softmax(Q_{(3\times 64)}\times K^T_{(64\times 3)})_{(3\times 3)}\times V_{(3\times 64)}$

- Computation Steps: ==Save the previous Q and K history== !!
	1. The i-th row vector $x_i$ of $X$ matrix with 12288 dimension is fed as input
	2. The $Q(x_i)$, $K(x_i)$, $V(x_i)$ are calculated and used to make output vector $x_{i+1}$
	3. Then, you feed $x_{i+1}$ to the decoder stack for next output $x_{i+2}$.
		1. The role of $V(x_i)$ is completed, so remove $V(x_i)$
		2. Due to masked self-attention, all the previous $Q_{x_{j} ~where ~j\le i+1}$ and $K_{x_{j} ~where ~j\le i+1}$ including $Q(x_i)$, $K(x_i)$ are reused for next decoding steps 
		3. So, ==all the previous==  $Q_{x_{j} ~where ~j\le i+1}$ and $K_{x_{j} ~where ~j\le i+1}$ ==vectors== should be ==stored in the memory== until the end of sentence generation!!

---

# Multi-Head Attention

- GPT-3 used 96 multi-head attention with different $W_q, W_k, W_v$ projection weights
	- So, 96x3x(Embedding-dim)x($QKV$'s columns) parameters from linear projections

![[Pasted image 20230304101536.png]]
<figcaption> Multi-Head Attention in GPT-3 </figcaption>

- Use results of multi-head attention $Y$s as input again!
	- $Y$ = 2048 (Tokens) $\times$ 128 ($QKV$'s columns)  
	- Concatenate 96 $Y$s = 12288 = 96 $\times$ 2048 $\times$ 128
	- So, now you have 2048 (Tokens) $\times$ 12288 (Embedding-dim)   

- GPT-3 uses ==sparse attention== ?
- $QKV$ calculation using big weight matrix and split ?

---

# Feed Forward

- 1 Hidden layer 
![[Pasted image 20230304102559.png]]
<figcaption> Feed Forward Layer </figcaption>

- Input and output = (2048 $\times$ 12288) with 12288 input/output neurons
- Hidden layer = 49152 hidden neurons = (4 $\times$ 12288)

---

# Add & Norm

- After Multi-Head attention and the Feed Forward blocks
- Input + Output $\rightarrow$ Normalize (like ResNet)
- "Layer normalization was moved to the input of each sub-block, similar to a pre-activation residual network and an additional layer normalization was added after the final self-attention block"?
![[Pasted image 20230304103125.png]]
<figcaption> Add and Norm </figcaption>

![[Pasted image 20230304103430.png]]
<figcaption> Normalization methods: Layer-norm normalizes across all neurons per sample </figcaption>

---

# Decoding

- The output from the multi-head attention layer $\rightarrow$ Input for decoding
- Decoding is to invert
	- the 12288-dim embedding vector back
	- to the word vector with 50257-dim (i.e vocabulary size) 
	- filled with likelihood of each word in the vocabulary 
- Given probability matrix, $top-k$ will synthesize the final sentence output!
![[Pasted image 20230304103748.png]]
<figcaption> Simplified illustration of decoding </figcaption>

![[Pasted image 20230304104035.png]]
<figcaption> Final results! </figcaption>

---

# Full Architecture

![[Pasted image 20230304104444.png]]
<figcaption> Sequence Encoding and Embedding </figcaption>

- 96 Multi-heads
![[Pasted image 20230304104526.png]]
<figcaption> Multi-head Attention </figcaption>


![[Pasted image 20230304104553.png]]
<figcaption> After multi-head attention, Add/Norm, Feed Forward, and Add/Norm </figcaption>

- Decoding
![[Pasted image 20230304104617.png]]
<figcaption> Decoding of the final result from the decoder stack </figcaption>


- Full architecture in one picture
![[Pasted image 20230304104351.png]]
<figcaption> Full architecture of GPT3 </figcaption>

---

# Application: Summarization of An Article

## Machine translation 

- From English to French
![[Pasted image 20230308071520.png]]
<figcaption> Machine translation training example by Jay Alamar </figcaption>

## Summarization 

- If you train the wikipedia pages (with summaries and articles in it) to GPT,
![[Pasted image 20230308071857.png]]
<figcaption> Wikipedia summary example by Jay Alamar </figcaption>

- The model can generate "summary of a given article"!
![[Pasted image 20230308072131.png]]
<figcaption> Summarization training example by Jay Alamar </figcaption>

---

# Reference:

- [The GPT-3 Architecture, on a Napkin](https://dugas.ch/artificial_curiosity/GPT_architecture.html)
- [The Illustrated GPT-2 (Visualizing Transformer Language Models)](http://jalammar.github.io/illustrated-gpt2/)
- [[Attention is All You Need]]
- [[Efﬁcient Estimation of Word Representations in Vector Space]]
