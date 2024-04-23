---
title: Efﬁcient Estimation of Word Representations in Vector Space
date: 2023-03-02
tags: DeepLearning AI NLP WordRepresentation
---

# Efﬁcient Estimation of Word Representations in Vector Space

- Reference: 
	- Mikolov, Tomas, et al. "[Efficient estimation of word representations in vector space.](https://arxiv.org/abs/1301.3781)" _arXiv preprint arXiv:1301.3781_ (2013).
	- [Neural Networks for Language Modelling slide](http://cbl.eng.cam.ac.uk/pub/Intranet/MLG/ReadingGroup/NeuralNetworksForLanguageModelling.pdf)  by Chunyang Wu

---

# 1. Introduction

- In 2013, many NLP systems treat ==words as atomic units==
	- ==No notion of similarity== between words
	- Eg. Simple ==N-gram== model
		- Image from [Introduction to n-gram language models](https://www.depends-on-the-definition.com/introduction-n-gram-language-models/) ![[Pasted image 20230225174028.png]]
		- N-gram from ChatGPT $\rightarrow$
			- In natural language processing (NLP), an ==N-gram model== is a statistical language model that predicts the ==probability of a word given its context, by analyzing sequences of N words (or tokens)== in a piece of text.
			- The "N" in "N-gram" refers to the number of words in each sequence. For example, in a ==bi-gram== model (N=2), the model analyzes ==pairs of consecutive words==, while in a tri-gram model (N=3), it looks at triplets of consecutive words.
			- The basic idea behind an N-gram model is that the probability of a word appearing in a particular context can be estimated based on the frequency of the N-gram sequence that contains that word. For example, if we have a bi-gram model that has been trained on a large corpus of text, and we want to predict the probability of the word "cat" appearing after the word "black", we would look at all the instances where "black" appears in the corpus, and calculate the frequency of the ==bi-gram "black cat"== among those instances.
			- N-gram models are widely used in various NLP tasks, including speech recognition, machine translation, and text classification. They are relatively simple and computationally efficient, but they can suffer from ==data sparsity issues==, especially when dealing with ==rare or unseen N-grams==. This is because the probability estimates of N-grams that are not present in the training data would be zero, which can lead to inaccurate predictions. To address this issue, various smoothing techniques have been proposed to adjust the probability estimates of rare or unseen N-grams.
		- Probabilistic understanding of N-gram  ![[Pasted image 20230302074524.png]]
		- Why N-gram is not good enough? ![[Pasted image 20230302074823.png]]![[Pasted image 20230302074843.png]]
	- But, not enough data (\#words ~ only billions) in the existing corpora $\rightarrow$ Limited progress in performance

- Emergence of more complex models on much larger data set
	- That outperform simple (eg. N-gram) models
	- ==Distributed representations of words== ~ Neural network based language models

---

## 1.1 Goal of this work

- Goal = Learning ==high quality word vectors== 
	- From huge ==data sets with billions of words== 
	- And ==millions of words in the vocabulary==
-  None of previous architectures has been successfully trained on more than
	- A few hundred millions of words
	- With a modest dimensionality of word vectors between 50-100
- Recently, we proposed techniques 
	- For ==measuring the quality of the resulting vector representations==, 
	- With the expectation that not only will ==similar words tend to be close== to each other, 
	- But that words can have ==multiple degrees of similarity==
- Work offset technique
	- Simple algebraic operations on word vectors
	- Ref: [The Amazing Power of Word Vectors](https://www.kdnuggets.com/2016/05/amazing-power-word-vectors.html) and [T. Mikolov, W.T. Yih, G. Zweig. Linguistic Regularities in Continuous Space Word Representations. NAACL HLT 2013.](https://aclanthology.org/N13-1090.pdf)  ![[Pasted image 20230225174959.png]]
- This paper
	- Maximize the ==accuracy of these vector operations==
	- By developing new model architectures that
		- Preserve the ==linear regularities among words==
	- Design new comprehensive test set for ==measuring both syntactic and semantic regularities==

---

## 1.2 Previous Work

- Neural network language model (NNLM) with a non-linear hidden layer
	- Learns word vector representation and statistical language model
	- Ref: Bengio, Yoshua, Réjean Ducharme, and Pascal Vincent. ["A neural probabilistic language model."](https://proceedings.neurips.cc/paper/2000/hash/728f206c2a01bf572b5940d7d9a8fa4c-Abstract.html) _Advances in neural information processing systems_ 13 (2000).

![[Pasted image 20230302075046.png]]

- Learn word vectors using NN $\rightarrow$ The vectors are used to train the NNLM
	- Ref: Mikolov, Tomas, et al. ["Neural network based language models for highly inflective languages."](https://ieeexplore.ieee.org/abstract/document/4960686/?casa_token=pMFGTDDC_Y0AAAAA:6yCKYFkp9uNZ_Bq1E_pufeWFMrMiAnas4wgWcTnds19qInfrrDKGhALiGgIEzyUvu5hNoCfnyd0O) _2009 IEEE international conference on acoustics, speech and signal processing_. IEEE, 2009.
- Proved that
	- Word vector $\rightarrow$ performance and simplicity of NLP applications $\uparrow$
- But, expensive training!

---

# 2. Model Architectures

- Previously well known models
	- Latent Semantic Analysis (LSA): Poor in preserving linear regularities than NNs
	- Latent Dirichlet Allocation (LDA): Very expensive
	- Distributed representations of words learned by neural networks: Winner! Will focus on it!

- Training complexity?
	- $O=E\times T\times Q$
	- $E$ : \#Training epochs (3 ~ 50)
	- $T$ : \#Words in the training set (Up to one billion)
	- $Q$ : Defined further for each model architecture
	- Trained using SGD and BP

---

## 2.1 Feedforward Neural Net Language Model (NNLM)

- Architecture
	- Input layer: 
		- N previous words are encoded using 1-of-V coding or one-hot encoding (V = size of the vocabulary)
		- Size of input matrix = 
			- $V\times N$ with ...
			- Column vector dim = V with only one non-zero value which is "1".
	- Projection layer:
		- Dimensionality $N\times D$ using shared projection matrix
		- Only N inputs are active! $\rightarrow$ Cheap!
		- Details 
			- Projection matrix : $D\times V$ 
			- Projection operation: ($D\times V$)$\times$($V\times N$)
			- Practically, its not matrix-vector multiplication. Just $D\times N$ times of table lookup ![[Pasted image 20230302075140.png]]
	- Hidden (H with tanh) and output (V with softmax) layers

- Computational complexity of FNNLM
	- Commonly N = 10
	- P = 500 ~ 2000
	- H = 500 ~ 1000
	- Output = V
		- Why so huge ?
		- Hidden layer computes probability distribution over all the words in the vocabulary, resulting in an output layer with dimensionality V
	- $O=N\times D+N\times D \times H + H\times V$
	- $H\times V$ is dominantly expensive, can we make it cheaper?
		- Hierarchical version of softmax
		- Use not-normalized models during training
		- Binary tree representation of the vocabulary: Output neurons $V$ $\rightarrow$ $log_2 V$

- In this paper, Used ...
	- Hierarchical softmax
	- Vocabulary $\rightarrow$ Represented as a Huffman binary tree 
		- Huffman encoding? $\rightarrow$ To replace ==frequently occurring symbols== in the data set with ==shorter codes== and less frequently occurring symbols with longer codes
		- Complexity: $log_2(Unigram~ perplexity(V ))$ < $log_2 V$ $\rightarrow$ 2$\times$ speedup

---

## 2.2 Recurrent Neural Net Language Model (RNNLM)

- Overcome limitations of feedforward NNLM (FNNLM)
	- FNNLM specifies the context length ($N$)
	- RNNLM can exploit information from initial words in a long sentence
		- ~ Short-term memory
		- Information from the past can be represented by the hidden layer state that gets updated based on the current input and the state of the hidden layer in the previous time step
- Architecture
	- Input, hidden, output layers
	- No projection layer

![[Pasted image 20230302080444.png]]

- Computational complexity of RNNLM
	- $Q=H\times H + H \times V$
	- Word representation $D$ = Same dimesionality as the hidden layer $H$
	- $H \times V$ can be efficiently reduced to $H \times log_2V$ using hierarchical softmax

---

- In progress