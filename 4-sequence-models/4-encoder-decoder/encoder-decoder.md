# Encoder-Decoder

📜 [Learning Phrase Representations using RNN Encoder–Decoder for Statistical Machine Translation](https://arxiv.org/pdf/1406.1078)

> In this paper, we propose a novel neural network model called RNN Encoder–Decoder that consists of two recurrent neural networks.

> One RNN encodes a sequence of symbols into a fixed length vector representation, and the other decodes the representation into another sequence of symbols.

> The encoder and decoder of the proposed model are jointly trained to maximize the conditional probability of a target sequence given a source sequence.

> Qualitatively, we show that the proposed model learns a semantically and syntactically meaningful representation of linguistic phrases.

Similar to the embeddings papers - it appears that this model creates a constraint to create a good sentence embedding model at the point where representations are created by the encoder, where these embeddings are forced to contain information about the meaning of a sentence, and then these embeddings are re-expressed in the target language with the decoder.

> Along this line of research on using neural networks for SMT, this paper focuses on a novel neural network architecture that can be used as a part of the conventional phrase-based SMT system.

> Additionally, we propose to use a rather sophisticated hidden
> unit in order to improve both the memory capacity
> and the ease of training.

### RNN Encoder-Decoder

**2. RNN Encoder-Decoder**

> The encoder is an RNN that reads each symbol of an input sequence x sequential. […] After reading the end of the sequence, the hidden state of the RNN is a summary $c$ of the whole input sequence.

> The decoder of the proposed model is another RNN which is trained to generate the output sequence by predicting the next symbol $y_t$ given the hidden state $h_{(t)}$. […] Both $y_t$ and $h_{(t)}$ are also conditioned on $y_{t-1}$ and on the summary $c$ of the input sequence.

> The two components of the proposed RNN Encoder–Decoder are jointly trained to maximize the conditional log-likelihood.

```math
\max_\theta \frac{1}{N}\sum_{n=1}^N \log p_\theta(y_n|x_n)
```

> Once the RNN Encoder–Decoder is trained, the model can be used in two ways. One way is to use the model to generate a target sequence given an input sequence. On the other hand, the model can be used to score a given pair of input and output sequences, where the score is simply a probability $p_\theta(y|x)$.

The model can be used to do actual generations, and also to score the likelihood that one sequence is the translation of another (used later in the Seq2Seq paper).

**3. Hidden Unit that Adaptively Remembers and Forgets**

> In addition to a novel model architecture, we also propose a new type of hidden unit that has been motivated by the LSTM unit but is much simpler to compute and implement.

They add a reset and update gate to a new unit in the RNN.

### Statistical Machine Translation

> In a commonly used statistical machine translation system (SMT), the goal of the system (decoder, specifically) is to find a translation $f$ given a source sentence $e$, which maximizes

```math
p(f|e) \propto p(e|f)p(f)
```

The $p(e|f)$ term is known as the _translation model_ and determines whether a given translation is likely to be equivalent to the source sentence.

The $p(f)$ term is the _language model_ and determines how grammatically and syntactically correct a sentence is in the target language.

> Most SMT systems model $\log p(f | e)$ as a log-linear model with addition features and corresponding weights

```math
\log p(f|e) = \sum_{n=1}^N w_nf_n(f,e) + \log Z(e)
```

> Recently, […] there has been interest in training neural networks to score the translated sentence (or phrase pairs) using a representation of the source sentence as an additional input.

**1. Scoring Phrase Pairs with RNN Encoder-Decoder**

> Here we propose to train the RNN Encoder–Decoder on a table of phrase pairs and use its scores as additional features in the log-linear model when tuning the SMT decoder.

In this section, they propose using the RNN Encoder-Decoder’s ability to produce scores for different translations as another factor to contribute to the broader structure of a more traditional SMT model.

> With a fixed capacity of the RNN Encoder–Decoder, we try to ensure that most of the capacity of the model is focused toward learning linguistic regularities.

### Experiments

**4. Word and Phrase Representations**

> It has been known for some time that continuous space language models using neural networks are able to learn semantically meaningful embeddings.

> From the visualization, it is clear that the RNN Encoder–Decoder captures both semantic and syntactic structures of the phrases.

![Screenshot 2024-05-15 at 6.02.30 PM.png](../../images/Screenshot_2024-05-15_at_6.02.30_PM.png)

### Conclusion

> The proposed RNN Encoder–Decoder is able to either score a pair of sequences (in terms of a conditional probability) or generate a target sequence given a source sequence.

> Our qualitative analysis of the trained model shows that it indeed captures the linguistic regularities in multiple levels i.e. at the word level as well as phrase level.