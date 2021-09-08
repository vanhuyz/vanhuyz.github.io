+++
author = "Huy Van"
date = 2018-08-21T08:38:53Z
description = ""
draft = true
slug = "untitled-2"
title = "(Untitled)"

+++


autoscale: true
slidenumbers: true
footer: Paper Reading Festival Summer 2018

## Hierarchical Neural Story Generation

### - Angela Fan, Mike Lewis, Yann Dauphin - Facebook AI
### @ACL2018

#### Presented by
#### Van Phu Quang Huy
#### 2018/08/19

---

# Overview (1/2)
### Task:
__**Story Generation**__ - creative system that can build coherent and fluent passages of text __**about a topic**__


Example of a story:

> A light breeze swept the ground, and carried with it still the distant scents of dust and time-worn stone. The Warrior led the way, heaving her mass of armour and muscle over the uneven terrain. She soon crested the last of the low embankments, which still bore the unmistakable fingerprints of haste and fear. She lifted herself up onto the top the rise, and looked out at the scene before her. [...]

---

# Overview (2/2)
## Challenges:
- Must remain thematically consistent across the complete document
 $$\rightarrow$$ requires modeling very long range dependencies
- Requires creativity, need a high level plot

## Ideas:
- First generates a sentence called the __**prompt**__ describing the topic for the story
- Then generates the story conditions on the prompt

![right fit](figure1.png)

---

# Contributions (1/2)

- Make a dataset (including prompts and stories) by collecting from Reddit's WRITINGPROMPTS forum.

- Introduce a __**fusion mechanism**__ (a seq2seq model trained on top of an pretrained seq2seq) to improve the relevance of the generated story to its prompt

---

# Contributions (2/2)

- Introduce a __**gated self-attention mechanism**__ on top of a convolutional architecture to improve efficiency of modeling long documents


- Introduce new evaluation metrics for story generation

---

# Writing Prompts Dataset

- Reddit's WRTTINGPROMPTS forum (www.reddit.com/r/WritingPrompts/)
  - Users write story premises or prompts, other users respond
  - Each prompt can have multiple story responses

- Collecting data and preprocessing
  - Scraped 3 years of data
  - Removed automated bot posts, deleted posts, announcements, short stories
  - Used NLTK for tokenization

![right fit](table1.png)

---

# Approach: Hierarchical Generation

![inline fit](hierarchical generation.jpg)

---

# 1. Prompt Generation

- Use a convolutional language model from [Dauphin et al. (2017)](https://arxiv.org/abs/1612.08083)

Right figure: illustrates a __**Gated Convolutional Neural Network (GCNN)**__ using _Gated Linear Units (GLU)_

- Why CNN instead of RNN?
Because CNN allows parallelization

![right fit](figure1-ref1.png)

---

# 2. Story Generation conditions on Prompt
- Based on the convolutional seq2seq model of [Gehring et al. (2017)](https://arxiv.org/abs/1705.03122) (right figure)
- Improve with:
  - Gated Multi-Scale Self-attention
  - Model Fusion

![right fit](figure1-ref2.png)

---

## Modeling Unbounded Context with Gated Multi-Scale Self-attention

Improve from [self-attention](https://arxiv.org/abs/1703.03130) mechanism with:

- Multi-scale attention
- Gated attention

---
## Reference: Attention

Attention function can be described as mapping a query and a set of key-value pairs to an output

$$
\mathrm{Attention}(Q,K,V) = \mathrm{softmax} (\frac{QK^T}{\sqrt{d_k}})V
$$

- $$Q$$: query, $$K$$: key, $$V$$: value, $$d_k$$: scaling factor

---
## Reference: Self-attention
**Example:** self-attention distribution for the word **“it”**

![inline fit](self-attention.png)

(Image from https://ai.googleblog.com/2017/08/transformer-novel-neural-network.html)

---

## Gated Attention

Use __**multi-head attention**__ similar to [Vaswani et al. (2017)](https://papers.nips.cc/paper/7181-attention-is-all-you-need.pdf) (right figure) to allow each head to attend to information at different positions.

However, **queries**, **keys**, **values** not given by linear projections but by gated deep neural nets with Gated Linear Unit (GLU).

![right fit](figure2-ref3.png)

---

## Multi-scale Attention

- Each head using a separate downsampling function in order to attend to different information

---

![inline fit](figure2.png)![inline fit](figure3.png)

- $$h_{0:t}^L$$: contains the hidden states up to time $$t$$ at layer $$L$$

- $$q, k,  v$$: gated downsampling networks
( $$q$$: query, $$k$$: key, $$v$$: value )

---

## Improving Relevance to Input Prompt with Model Fusion

- Train a seq2seq model that has access to the hidden states of a pretrained seq2seq model

(can be seen as a type of boosting or residual learning that allows the second model to focus on what the first model faled to learn, such as conditioning on the prompt)

![right fit](figure4.png)

---

## Generation
Use __**top-k random sampling**__ instead of beam search

- At each timestep, generate the probability of each word, then randomly sample from $$k=10$$ most likely candidates from this distribution

- (Beam search tends to produce common phrases and repetitive text from the training set)

---

# Experimental Setup
## Baselines

1. Gated CNN language model w/ and w/o self-attention
1. seq2seq: LSTMs, Conv seq2seq, Conv seq2seq w/ decoder self-attention
1. Ensemble: of 2 Conv seq2seq w/ self-attention
1. KNN: finds the closest prompt in the training set for each prompt in the test set

---

## Evaluation

- Automatic evaluation
  - Perplexity
  - Prompt ranking accuracy

- Human evaluation

---

# Results

---
### Generated by proposed model (1/2)
**Prompt:** Aliens start abducting humans

**Generated story:**
It has been two weeks, and the last of my kind has gone. It is only a matter of time until there will be nothing left. I’m not sure what the hell is going on... I can’t think. I can hear a distant scream. I think of a strange, alien sound. I try to speak, but am interrupted by something, something that feels like a drum, I ca not tell. I mean I’m just a little bit older than an average human. But I can, and I can feel the vibrations . I hear the sound of a ship approaching. The ground quakes at the force of the impact, and a loud explosion shatters the silence.

---

### Generated by proposed model (2/2)
**Prompt:** The scientists have discovered something terrible.

**Generated story:**
The scientist stood there, a little dazed as he stared.
“What is it?” He asked.
“This...this...Thing...This is a virus. A chemical that can destroy entire planet and it is a very small, complex, chemical that could destroy any planet.” The scientist replied. His lab assistant looked down at the tablet.
“I’ve just discovered it. I can’t believe it. It looks like it’s made of some sort of chemical that’s very dangerous.”
“ Well, there’s a virus on the ground. It’s very effective...” “ I can’t believe what it is.” he said, pointing to the scientist .
“ We don’t know what this thing is. We haven’t seen anything like it . We can’t even see anything like this. ” Dr. Jones stared at the scientist for a moment.
“What do you mean what does it do ?”
“It...It ’s a monster.”

---
### Generated by baseline model (1/2)
I’ve always been the curious type. Growing up in the ghetto, I’ve been around for more than a year now. I still haven’t heard it on my own. I’m not sure if I’m a good person. But I’m not the only one. I’ve been to the hospital, but it’s not my first time. I’ve been to my psychiatrist’s journals before. “Hey, are you ... are you okay?” I asked. “Yes” I said. “What’s wrong?” she replied, “I was just ... Doing something.” She said, “I’ve never been a bad person.”

---
### Generated by baseline model (2/2)
The man was an accountant. He had to be. He had to be the next president. I looked back over the top and saw that his wife was crying in the kitchen. I looked at the clock. It seemed to be coming slower, but I knew if I did it would not be long before I was in my own home. I wasn’t sure. I had a hard time finding the right words to say. I was about to leave when he suddenly became angry and began talking to me. “Hello, sir, I’m John. What is your name?” “My name is Manuel and I’m a journalist.” I said

---
## Perplexity (1/2)

![inline](table2.png)

---

## Perplexity (2/2)
![inline](table3.png)

---
## Prompt ranking

![inline](figure6.png)

---

## Human evaluation (1/2)

![inline](figure5.png)


---
## Human evaluation (2/2)

![inline](table4.png)

---
## Fusion model evaluation

![inline](figure8.png)

---

# Discussion

- Proposed model is capable of generating unique text without copying directly from training set compared to the baseline model
- Limitation:
  - Random sampling can produce errors: e.g. _can't_ is tokenized to _ca_ - _n't_ and the model occasionally produces the first token but misses the second
  - Repetition: generates similar text multiple times because the model focuses frequently on what it has recently produced
  - In generation of prompts: prompts are fairly generic compaird to human prompts, e.g. many prompts start with _the man_

---

# Conclusion

- new large scale **dataset** for hierarchical story generation
- **evaluation** metrics for story writing
- **models** to improve generation coherence and relationship with desired premise

- data+code: [github.com/pytorch/fairseq](https://github.com/pytorch/fairseq)

---

# Thank you!

