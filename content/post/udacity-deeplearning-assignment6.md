+++
author = "Huy Van"
categories = ["Deep Learning", "Japanese"]
date = 2017-04-29T10:59:33Z
description = ""
draft = true
slug = "udacity-deeplearning-assignment6"
tags = ["Deep Learning", "Japanese"]
title = "DeepLearning勉強会 - Assignment 6: LSTMs - Problem 1, 2"

+++


# 概要
深層学習勉強会の課題6(問題1+2)です。今回はLSTMを使って、言語モデルを作ることです。

課題の難易度が急に高くなりますね。

## 準備

### データセットについて
学習データは[text8](http://mattmahoney.net/dc/textdata)というデータセットを使います。

```
$ unzip text8.zip
$ wc text8
0 17005207 100000000 text8
$ less text8
```

text8は17005207単語、100000000バイトというでかいテキストファイルです。最初の500文字を表示すると、

```
$ head -c500 text8
 anarchism originated as a term of abuse first used against early working class radicals including the diggers of the english revolution and the sans culottes of the french revolution whilst the term is still used in a pejorative way to describe any act that used violent means to destroy the organization of society it has also been taken up as a positive label by self defined anarchists the word anarchism is derived from the greek without archons ruler chief king anarchism as a political philoso%
```

になります。句点と数字がなく、小文字だけのテキストだとわかります。

* データをダウンロードして、学習セットとバリデーションセットに分けます

```python
valid_size = 1000
valid_text = text[:valid_size]
train_text = text[valid_size:]
train_size = len(train_text)
print(train_size, train_text[:64])
print(valid_size, valid_text[:64])

```

* 目的は次の文字を予測することです。そのため、辞書はa~zの26種類の文字とスペースだけです。

```
vocabulary = [' ', 'a', 'b',..., 'z']
```

`vocabulary_size`は27です。`a`のIDは1, `z`のIDは26, スペースのIDは0です。

```python
vocabulary_size = len(string.ascii_lowercase) + 1 # [a-z] + ' '
first_letter = ord(string.ascii_lowercase[0])

def char2id(char):
  if char in string.ascii_lowercase:
    return ord(char) - first_letter + 1
  elif char == ' ':
    return 0
  else:
    print('Unexpected character: %s' % char)
    return 0
  
def id2char(dictid):
  if dictid > 0:
    return chr(dictid + first_letter - 1)
  else:
    return ' '
```

### BatchGeneratorについて

```python
batch_size=64
num_unrollings=10

class BatchGenerator(object):
  def __init__(self, text, batch_size, num_unrollings):
    self._text = text
    self._text_size = len(text)
    self._batch_size = batch_size
    self._num_unrollings = num_unrollings
    segment = self._text_size // batch_size
    self._cursor = [ offset * segment for offset in range(batch_size)]
    self._last_batch = self._next_batch()
  
  def _next_batch(self):
    """Generate a single batch from the current cursor position in the data."""
    batch = np.zeros(shape=(self._batch_size, vocabulary_size), dtype=np.float)
    for b in range(self._batch_size):
      batch[b, char2id(self._text[self._cursor[b]])] = 1.0
      self._cursor[b] = (self._cursor[b] + 1) % self._text_size
    return batch
  
  def next(self):
    """Generate the next array of batches from the data. The array consists of
    the last batch of the previous array, followed by num_unrollings new ones.
    """
    batches = [self._last_batch]
    for step in range(self._num_unrollings):
      batches.append(self._next_batch())
    self._last_batch = batches[-1]
    return batches

def characters(probabilities):
  """Turn a 1-hot encoding or a probability distribution over the possible
  characters back into its (most likely) character representation."""
  return [id2char(c) for c in np.argmax(probabilities, 1)]

def batches2string(batches):
  """Convert a sequence of batches back into their (most likely) string
  representation."""
  s = [''] * batches[0].shape[0]
  for b in batches:
    s = [''.join(x) for x in zip(s, characters(b))]
  return s

train_batches = BatchGenerator(train_text, batch_size, num_unrollings)
valid_batches = BatchGenerator(valid_text, 1, 1)
```
コードはわかりにくいから１つの例を出しましょう。例えば、以下のテキストがあります。

```
sample_text = 'abcd efgh ijkl mnop qrst uvwx yz'
```

sample_textのサイズは32です。`batch_size=10, num_unrollings=5`にします。

```
batches = BatchGenerator(sample_text, 10, 5)
print(batches2string(batches.next()))
print(batches2string(batches.next()))
print(batches2string(batches.next()))
print(batches2string(batches.next()))
```

結果：

```
['abcd e', 'd efgh', 'fgh ij', ' ijkl ', 'kl mno', 'mnop q', 'p qrst', 'rst uv', ' uvwx ', 'wx yza']
['efgh i', 'h ijkl', 'jkl mn', ' mnop ', 'op qrs', 'qrst u', 't uvwx', 'vwx yz', ' yzabc', 'abcd e']
['ijkl m', 'l mnop', 'nop qr', ' qrst ', 'st uvw', 'uvwx y', 'x yzab', 'zabcd ', 'cd efg', 'efgh i']
['mnop q', 'p qrst', 'rst uv', ' uvwx ', 'wx yza', 'yzabcd', 'bcd ef', ' efgh ', 'gh ijk', 'ijkl m']
```

なぜこんな結果になるのか見てみましょう。
まず、テキストをsegment(区分)に分割します。

```
segment = self._text_size // batch_size
```

この場合、segmentのサイズは 32//10 = 3になります。

```
abc|d e|fgh| ij|kl |mno|p q|rst| uv|wx |yz
```

１番目のバッチの各要素は各segmentの最初の文字から始まり、長さ6(つまりnum_unrollings+1)になりますね。
２番目のバッチの各要素は１番目のバッチの各要素の最後の文字から始まります。
こういうルールでバッチを生成します。

### LSTM Cellについて

```python
def lstm_cell(i, o, state):
    """Create a LSTM cell. See e.g.: http://arxiv.org/pdf/1402.1128v1.pdf
    Note that in this formulation, we omit the various connections between the
    previous state and the gates."""
    input_gate = tf.sigmoid(tf.matmul(i, ix) + tf.matmul(o, im) + ib)
    forget_gate = tf.sigmoid(tf.matmul(i, fx) + tf.matmul(o, fm) + fb)
    update = tf.matmul(i, cx) + tf.matmul(o, cm) + cb
    state = forget_gate * state + input_gate * tf.tanh(update)
    output_gate = tf.sigmoid(tf.matmul(i, ox) + tf.matmul(o, om) + ob)
    return output_gate * tf.tanh(state), state
```

[公式](https://galapagos.qiita.com/vanhuyz/items/de83a65dd7038aec7c35#%E6%95%B0%E5%AD%A6%E7%9A%84%E3%81%AA%E8%AA%AC%E6%98%8E)にはbiasがなかったが、あってもなくても問題ないのか？

### サンプリングについて

```python
def logprob(predictions, labels):
  """Log-probability of the true labels in a predicted batch."""
  predictions[predictions < 1e-10] = 1e-10
  return np.sum(np.multiply(labels, -np.log(predictions))) / labels.shape[0]

def sample_distribution(distribution):
  """Sample one element from a distribution assumed to be an array of normalized
  probabilities.
  """
  r = random.uniform(0, 1)
  s = 0
  for i in range(len(distribution)):
    s += distribution[i]
    if s >= r:
      return i
  return len(distribution) - 1

def sample(prediction):
  """Turn a (column) prediction into 1-hot encoded samples."""
  p = np.zeros(shape=[1, vocabulary_size], dtype=np.float)
  p[0, sample_distribution(prediction[0])] = 1.0
  return p

def random_distribution():
  """Generate a random column of probabilities."""
  b = np.random.uniform(0.0, 1.0, size=[1, vocabulary_size])
  return b/np.sum(b, 1)[:,None]
```

以上の関数の中、`logprob`に注目してください。この関数は次の文字の確率のnegative logを計算します。確率が高いほど、negative logが小さくなります。確率の最大化 ≒ negative logの最小化です。

$$
\textrm{logprob} = -\frac{1}{N} \sum_{i=1}^N \ln p_{target_{i}}
$$

実際、次の文字（または単語）を予測するモデルにはperplexityという評価指数がよく使われています。

$$
\textrm{perplexity} = \exp(\textrm{logprob})
$$

要はperplexityが低いほどモデルが良いです。

### 学習

コードが長いので省略します。テンソルのサイズ変化はこんな風です。

```
input (batch_size x vocabulary_size)
↓ LSTM
batch_size x num_nodes
↓ classifier
batch_size x vocabulary_size
```

inputとoutputはone-hot encoding vectorで表現します。

### 結果

```
Average loss at step 7000 : 1.57905534983 learning rate: 1.0
Minibatch perplexity: 5.08
================================================================================
jague are officiencinels ored by film voon higherise haik one nine on the iffirc
oshe provision that manned treatists on smalle bodariturmeristing the girto in s
kis would softwenn mustapultmine truativersakys bersyim by s of confound esc bub
ry of the using one four six blain ira mannom marencies g with fextificallise re
 one son vit even an conderouss to person romer i a lebapter at obiding are iuse
================================================================================
Validation set perplexity: 4.25
```

## Problem 1

> You might have noticed that the definition of the LSTM cell involves 4 matrix multiplications with the input, and 4 matrix multiplications with the output. Simplify the expression by using a single matrix multiply for each, and variables that are 4 times larger.

サンプルコードのLSTM Cellの計算は全部8回の行列掛け算が必要です。２回掛け算だけで書き直しましょう。

アイデアとしては4つゲートのinput, previous output, biasのそれぞれの変数を連結して、でかい行列を作ります。結果として、でかいinput、でかいprevious output、でかいbiasが得られます。それらのでかい行列を掛け算・足し算して、もらった結果を分割して、outputを計算します。

```python
## Concat to big variables
ax = tf.concat(1, [ix, fx, cx, ox])
am = tf.concat(1, [im, fm, cm, om])
ab = tf.concat(1, [ib, fb, cb, ob])

def lstm_cell(i, o, state):
    """Create a LSTM cell. See e.g.: http://arxiv.org/pdf/1402.1128v1.pdf
    Note that in this formulation, we omit the various connections between the
    previous state and the gates."""
    
    big_matrix = tf.matmul(i, ax) + tf.matmul(o, am) + ab
    split0, split1, split2, split3 = tf.split(1, 4, big_matrix)
    
    input_gate = tf.sigmoid(split0)
    forget_gate = tf.sigmoid(split1)
    update = split2
    
    state = forget_gate * state + input_gate * tf.tanh(update)
    output_gate = tf.sigmoid(split3)
    
    return output_gate * tf.tanh(state), state
```

## Problem 2

> We want to train a LSTM over bigrams, that is pairs of consecutive characters like 'ab' instead of single characters like 'a'. Since the number of possible bigrams is large, feeding them directly to the LSTM using 1-hot encodings will lead to a very sparse representation that is very wasteful computationally.
a- Introduce an embedding lookup on the inputs, and feed the embeddings to the LSTM cell instead of the inputs themselves.
b- Write a bigram-based LSTM, modeled on the character LSTM above.
c- Introduce Dropout. For best practices on how to use Dropout in LSTMs, refer to this article.

サンプルにはunigramモデルを作りましたが、今回はbigramモデルを構築します。

### a- bigramのembeddingを作る

これは[課題５](https://galapagos.qiita.com/john-saman/items/701faef58b59efa16902)を参考しましょう。

#### データの整理

* まず、bigram辞書を作ります。サンプルコードにはスペースのIDは0ですが、今回僕のbigram辞書は`aa`から始めます。

```python
def build_bigram_dictionary():
  # 26 letters + space
  all_letters = string.ascii_lowercase + " "
  bigram_list = []
  dictionary = dict()
  dic_index = 0
  for i in all_letters:
    for j in all_letters:
      dictionary[i+j] = len(dictionary)
  reverse_dictionary = dict(zip(dictionary.values(), dictionary.keys())) 
  return dictionary, reverse_dictionary 

dictionary, reverse_dictionary = build_bigram_dictionary()

print(dictionary['aa'])
print(dictionary['sd'])
print(dictionary['  '])
print(reverse_dictionary[100])
print(len(dictionary))  
```

```
0
489
728
dt
729
```

* 次に、データセットをbigramに分割して、各bigramを辞書IDに変換します。

```python
def build_dataset(text):
  bigram_data = [a+b for a,b in zip(text[:len(text)],text[1:len(text)+1])][::2]
  data = [dictionary[i] for i in bigram_data]
  return data
data = build_dataset(text)
print(data[:10])
print([reverse_dictionary[i] for i in data[:10]])
```

```
[702, 351, 461, 197, 498, 716, 467, 170, 351, 517]
[' a', 'na', 'rc', 'hi', 'sm', ' o', 'ri', 'gi', 'na', 'te']
```


#### skip-gramの実装：
課題５とほとんど一緒です。

* batch生成

```python
import collections

data_index = 0

def generate_batch(batch_size, num_skips, skip_window):
  global data_index
  assert batch_size % num_skips == 0
  assert num_skips <= 2 * skip_window
  batch = np.ndarray(shape=(batch_size), dtype=np.int32)
  labels = np.ndarray(shape=(batch_size, 1), dtype=np.int32)
  span = 2 * skip_window + 1 # [ skip_window target skip_window ]
  buffer = collections.deque(maxlen=span)
  for _ in range(span):
    buffer.append(data[data_index])
    data_index = (data_index + 1) % len(data)
  for i in range(batch_size // num_skips):
    target = skip_window  # target label at the center of the buffer
    targets_to_avoid = [ skip_window ]
    for j in range(num_skips):
      while target in targets_to_avoid:
        target = random.randint(0, span - 1)
      targets_to_avoid.append(target)
      batch[i * num_skips + j] = buffer[skip_window]
      labels[i * num_skips + j, 0] = buffer[target]
    buffer.append(data[data_index])
    data_index = (data_index + 1) % len(data)
  return batch, labels

print('data:', [reverse_dictionary[di] for di in data[:8]])

for num_skips, skip_window in [(2, 1), (4, 2)]:
    data_index = 0
    batch, labels = generate_batch(batch_size=8, num_skips=num_skips, skip_window=skip_window)
    print('\nwith num_skips = %d and skip_window = %d:' % (num_skips, skip_window))
    print('    batch:', [reverse_dictionary[bi] for bi in batch])
    print('    labels:', [reverse_dictionary[li] for li in labels.reshape(8)])
```

```
data: [' a', 'na', 'rc', 'hi', 'sm', ' o', 'ri', 'gi']

with num_skips = 2 and skip_window = 1:
    batch: ['na', 'na', 'rc', 'rc', 'hi', 'hi', 'sm', 'sm']
    labels: ['rc', ' a', 'hi', 'na', 'rc', 'sm', 'hi', ' o']

with num_skips = 4 and skip_window = 2:
    batch: ['rc', 'rc', 'rc', 'rc', 'hi', 'hi', 'hi', 'hi']
    labels: ['hi', 'sm', 'na', ' a', 'rc', 'sm', ' o', 'na']
```


* skip-gram学習

とりあえず、`embedding_size = 128` にします。次元が729から128に圧縮しますね。

```python
import math

batch_size = 128
embedding_size = 128 # Dimension of the embedding vector.
skip_window = 1 # How many words to consider left and right.
num_skips = 2 # How many times to reuse an input to generate a label.
## We pick a random validation set to sample nearest neighbors. here we limit the
## validation samples to the words that have a low numeric ID, which by
## construction are also the most frequent. 
valid_size = 16 # Random set of words to evaluate similarity on.
valid_window = 100 # Only pick dev samples in the head of the distribution.
valid_examples = np.array(random.sample(range(valid_window), valid_size))
num_sampled = 64 # Number of negative examples to sample.

vocabulary_size =  len(dictionary)

graph = tf.Graph()

with graph.as_default(), tf.device('/cpu:0'):

  # Input data.
  train_dataset = tf.placeholder(tf.int32, shape=[batch_size])
  train_labels = tf.placeholder(tf.int32, shape=[batch_size, 1])
  valid_dataset = tf.constant(valid_examples, dtype=tf.int32)
  
  # Variables.
  embeddings = tf.Variable(
    tf.random_uniform([vocabulary_size, embedding_size], -1.0, 1.0))
  softmax_weights = tf.Variable(
    tf.truncated_normal([vocabulary_size, embedding_size],
                         stddev=1.0 / math.sqrt(embedding_size)))
  softmax_biases = tf.Variable(tf.zeros([vocabulary_size]))
  
  # Model.
  # Look up embeddings for inputs.
  embed = tf.nn.embedding_lookup(embeddings, train_dataset)
  # Compute the softmax loss, using a sample of the negative labels each time.
  loss = tf.reduce_mean(
    tf.nn.sampled_softmax_loss(softmax_weights, softmax_biases, embed,
                               train_labels, num_sampled, vocabulary_size))

  # Optimizer.
  # Note: The optimizer will optimize the softmax_weights AND the embeddings.
  # This is because the embeddings are defined as a variable quantity and the
  # optimizer's `minimize` method will by default modify all variable quantities 
  # that contribute to the tensor it is passed.
  # See docs on `tf.train.Optimizer.minimize()` for more details.
  optimizer = tf.train.AdagradOptimizer(1.0).minimize(loss)
  
  # Compute the similarity between minibatch examples and all embeddings.
  # We use the cosine distance:
  norm = tf.sqrt(tf.reduce_sum(tf.square(embeddings), 1, keep_dims=True))
  normalized_embeddings = embeddings / norm
  valid_embeddings = tf.nn.embedding_lookup(
    normalized_embeddings, valid_dataset)
  similarity = tf.matmul(valid_embeddings, tf.transpose(normalized_embeddings))
```

```python
num_steps = 100001

with tf.Session(graph=graph) as session:
  tf.initialize_all_variables().run()
  print('Initialized')
  average_loss = 0
  for step in range(num_steps):
    batch_data, batch_labels = generate_batch(
      batch_size, num_skips, skip_window)
    feed_dict = {train_dataset : batch_data, train_labels : batch_labels}
    _, l = session.run([optimizer, loss], feed_dict=feed_dict)
    average_loss += l
    if step % 2000 == 0:
      if step > 0:
        average_loss = average_loss / 2000
      # The average loss is an estimate of the loss over the last 2000 batches.
      print('Average loss at step %d: %f' % (step, average_loss))
      average_loss = 0
    # note that this is expensive (~20% slowdown if computed every 500 steps)
    if step % 10000 == 0:
      sim = similarity.eval()
      for i in range(valid_size):
        valid_word = reverse_dictionary[valid_examples[i]]
        top_k = 8 # number of nearest neighbors
        nearest = (-sim[i, :]).argsort()[1:top_k+1]
        log = 'Nearest to %s:' % valid_word
        for k in range(top_k):
          close_word = reverse_dictionary[nearest[k]]
          log = '%s %s,' % (log, close_word)
        print(log)
  final_embeddings = normalized_embeddings.eval()
```

```
Average loss at step 100000: 1.519835
Nearest to cy: ft, be, xt, pm, ds, cq, ry, oc,
Nearest to cf: sq, lx, ci, ev, xw, oe, ip, mj,
Nearest to dn: sv, bg, gg, vo, kd, gj, lc, sl,
Nearest to cl: pl, pr, gr, cr, zx, tw, ma, ag,
Nearest to ay: oy, iu, mv, qk, sx, ok, od, wv,
Nearest to cd: gr, kn, xi, xe, jq, v , xc, zy,
Nearest to ab: ib, ob, fw, ru, av, wh, ph, vn,
Nearest to as: qv, oy, zu, ek, ac, aw, nx, ys,
Nearest to dj: ki, ky, tt, id, hu,  m, hd, eb,
Nearest to ad: av, sd, yd, qg, jl, dx, qo, dp,
Nearest to cb: pe, nc, eb, bw, bu, qh, fo, vh,
Nearest to bn: qd, wv, hb, gw, fb, mw, ew, uw,
Nearest to ax: my, eg, yp, ak, wp, yr, iz, ag,
Nearest to az: sk, oa, av, tm, iz, zx, ph, iy,
Nearest to bl: kn, fl, gr, gl, br, tt, cl, pl,
Nearest to am: om, qg, ym, av, iv, az, cn, fl,
```

* tSNEで可視化

```python
from sklearn.manifold import TSNE
from matplotlib import pylab
%matplotlib inline

num_points = 400

tsne = TSNE(perplexity=30, n_components=2, init='pca', n_iter=5000)
two_d_embeddings = tsne.fit_transform(final_embeddings[1:num_points+1, :])

def plot(embeddings, labels):
  assert embeddings.shape[0] >= len(labels), 'More labels than embeddings'
  pylab.figure(figsize=(15,15))  # in inches
  for i, label in enumerate(labels):
    x, y = embeddings[i,:]
    pylab.scatter(x, y)
    pylab.annotate(label, xy=(x, y), xytext=(5, 2), textcoords='offset points',
                   ha='right', va='bottom')
  pylab.show()

words = [reverse_dictionary[i] for i in range(1, num_points+1)]
plot(two_d_embeddings, words)
```

![image](https://galapagos.qiita.com/files/538001b3-3a23-6dbc-e341-81ae1e75b7b1.png)

embedding完了！

### b- bigramのLSTMを作る
アイデア：

```
input (batch_size x embedding_size)
↓ LSTM
batch_size x num_nodes
↓ classifier
batch_size x vocabulary_size
```

* まず、unigram batch generatorをbigram batch generatorに直します。

```python
batch_size=64
num_unrollings=10

def id2onehot(id):
  one_hot = np.zeros(shape=(1, vocabulary_size), dtype=np.float)
  one_hot[0,id] = 1.0
  return one_hot

class BatchGenerator(object):
  def __init__(self, text, batch_size, num_unrollings):
    self._text = text
    self._text_size = len(text)
    self._batch_size = batch_size
    self._num_unrollings = num_unrollings
    segment = self._text_size // batch_size
    self._cursor = [ offset * segment for offset in range(batch_size)]
    self._last_batch = self._next_batch()
  
  def _next_batch(self):
    """Generate a single batch from the current cursor position in the data."""
    # batch = np.zeros(shape=(self._batch_size, embedding_size), dtype=np.float)
    batch = np.zeros(shape=(self._batch_size, vocabulary_size), dtype=np.float)

    for b in range(self._batch_size):
      word_id = dictionary[self._text[self._cursor[b]:self._cursor[b]+2]]
      batch[b] = id2onehot(word_id)
      self._cursor[b] = (self._cursor[b] + 2) % self._text_size
    return batch
  
  def next(self):
    """Generate the next array of batches from the data. The array consists of
    the last batch of the previous array, followed by num_unrollings new ones.
    """
    batches = [self._last_batch]
    for step in range(self._num_unrollings):
      batches.append(self._next_batch())
    self._last_batch = batches[-1]
    return batches

def characters(probabilities):
  """Turn a 1-hot encoding or a probability distribution over the possible
  bigram-characters back into its (most likely) bigram-character representation."""
  return [reverse_dictionary[c] for c in np.argmax(probabilities, 1)]
 
def batches2string(batches):
  """Convert a sequence of batches back into their (most likely) string
  representation."""
  s = [''] * batches[0].shape[0]
  for b in batches:
    s = [''.join(x) for x in zip(s, characters(b))]
  return s

train_batches = BatchGenerator(train_text, batch_size, num_unrollings)
valid_batches = BatchGenerator(valid_text, 1, 1)

print(batches2string(train_batches.next()))
print(batches2string(train_batches.next()))
print(batches2string(valid_batches.next()))
print(batches2string(valid_batches.next()))
```

```
['ons anarchists advocat', 'when military governme', 'lleria arches national', ' abbeys and monasterie', 'married urraca princes', 'hel and richard baer h', 'y and liturgical langu', 'ay opened for passenge', 'tion from the national', 'migration took place d', 'new york other well kn', 'he boeing seven six se', 'e listed with a gloss ', 'eber has probably been', 'o be made to recognize', 'yer who received the f', 'ore significant than i', 'a fierce critic of the', ' two six eight in sign', 'aristotle s uncaused c', 'ity can be lost as in ', ' and intracellular ice', 'tion of the size of th', 'dy to pass him a stick', 'f certain drugs confus', 'at it will take to com', 'e convince the priest ', 'ent told him to name i', 'ampaign and barred att', 'rver side standard for', 'ious texts such as eso', 'o capitalize on the gr', 'a duplicate of the ori', 'gh ann es d hiver one ', 'ine january eight marc', 'ross zero the lead cha', 'cal theories classical', 'ast instance the non g', ' dimensional analysis ', 'most holy mormons beli', 't s support or at leas', 'u is still disagreed u', 'e oscillating system e', 'o eight subtypes based', 'of italy languages the', 's the tower commission', 'klahoma press one nine', 'erprise linux suse lin', 'ws becomes the first d', 'et in a nazi concentra', 'the fabian society neh', 'etchy to relatively st', ' sharman networks shar', 'ised emperor hirohito ', 'ting in political init', 'd neo latin most of th', 'th risky riskerdoo ric', 'encyclopedic overview ', 'fense the air componen', 'duating from acnm accr', 'treet grid centerline ', 'ations more than any o', 'appeal of devotional b', 'si have made such devi']
['ate social relations b', 'ments failed to revive', 'al park photographic v', 'ies index sacred desti', 'ess of castile daughte', ' h provided a detailed', 'guage among jews manda', 'gers in december one n', 'al media and from pres', ' during the one nine e', 'known manufacturers of', 'seven a widebody jet w', 's covering some of the', 'en one of the most inf', 'ze single acts of meri', ' first card from the d', ' in jersey and guernse', 'he poverty and social ', 'gns of humanity vol th', ' cause so aquinas come', 'n denaturalization and', 'ce formation solution ', 'the input usually meas', 'ck to pull him out but', 'usion inability to ori', 'omplete an operation c', 't of the mistakes of a', ' it fort des moines th', 'ttempts by his opponen', 'ormats for mailboxes i', 'soteric christianity a', 'growing popularity of ', 'riginal document fax m', 'e nine eight zero one ', 'rch eight listing of a', 'haracter lieutenant sh', 'al mechanics and speci', ' gm comparison maize c', 's fundamental applicat', 'lieve the configuratio', 'ast not parliament s o', ' upon by historians an', ' example rlc circuit f', 'ed on the whole genome', 'he official language o', 'on at this point presi', 'ne three two one one t', 'inux enterprise server', ' daily college newspap', 'ration camp lewis has ', 'ehru wished the econom', 'stiff from flat to tig', 'arman s sydney based b', 'o to begin negotiation', 'itiatives the lesotho ', 'these authors wrote in', 'icky ricardo this clas', 'w of mathematics prese', 'ent of arm is represen', 'credited programs must', 'e external links bbc o', ' other state modern da', ' buddhism especially r', 'vices possible the sys']
[' ana']
['narc']
```

* 次に、LSTMの入力のサイズを修正します。
主に`vocabulary_size` → `embedding_size`。

```python
  # Parameters:
  # Input gate: input, previous output, and bias.
  ix = tf.Variable(tf.truncated_normal([embedding_size, num_nodes], -0.1, 0.1))
  im = tf.Variable(tf.truncated_normal([num_nodes, num_nodes], -0.1, 0.1))
  ib = tf.Variable(tf.zeros([1, num_nodes]))
  # Forget gate: input, previous output, and bias.
  fx = tf.Variable(tf.truncated_normal([embedding_size, num_nodes], -0.1, 0.1))
  fm = tf.Variable(tf.truncated_normal([num_nodes, num_nodes], -0.1, 0.1))
  fb = tf.Variable(tf.zeros([1, num_nodes]))
  # Memory cell: input, state and bias.                             
  cx = tf.Variable(tf.truncated_normal([embedding_size, num_nodes], -0.1, 0.1))
  cm = tf.Variable(tf.truncated_normal([num_nodes, num_nodes], -0.1, 0.1))
  cb = tf.Variable(tf.zeros([1, num_nodes]))
  # Output gate: input, previous output, and bias.
  ox = tf.Variable(tf.truncated_normal([embedding_size, num_nodes], -0.1, 0.1))
  om = tf.Variable(tf.truncated_normal([num_nodes, num_nodes], -0.1, 0.1))
  ob = tf.Variable(tf.zeros([1, num_nodes]))
  # Variables saving state across unrollings.
  saved_output = tf.Variable(tf.zeros([batch_size, num_nodes]), trainable=False)
  saved_state = tf.Variable(tf.zeros([batch_size, num_nodes]), trainable=False)
  
  # Concat to big variables
  ax = tf.concat(1, [ix, fx, cx, ox])
  am = tf.concat(1, [im, fm, cm, om])
  ab = tf.concat(1, [ib, fb, cb, ob])

  # Variables saving state across unrollings.
  saved_output = tf.Variable(tf.zeros([batch_size, num_nodes]), trainable=False)
  saved_state = tf.Variable(tf.zeros([batch_size, num_nodes]), trainable=False)
  # Classifier weights and biases.
  w = tf.Variable(tf.truncated_normal([num_nodes, vocabulary_size], -0.1, 0.1))
  b = tf.Variable(tf.zeros([vocabulary_size]))
```

LSTM Cellの定義の中に、cellを入れる前にone-hot encodingをword2vecに置き換えます。ここで、`tf.nn.embedding_lookup`を使います。embedding_lookupのパラメータはone-hot encodingではなく、辞書IDにしないといけないので、one-hot encodingから辞書IDに変換することが必要です。一番簡単なのは`argmax`をとるだけです。

```python
def lstm_cell(i, o, state):
    """Create a LSTM cell. See e.g.: http://arxiv.org/pdf/1402.1128v1.pdf
    Note that in this formulation, we omit the various connections between the
    previous state and the gates."""
    
    embed_i = tf.nn.embedding_lookup(final_embeddings,tf.argmax(i,1))
    big_matrix = tf.matmul(embed_i, ax) + tf.matmul(o, am) + ab
    split0, split1, split2, split3 = tf.split(1, 4, big_matrix)
    
    input_gate = tf.sigmoid(split0)
    forget_gate = tf.sigmoid(split1)
    update = split2
    
    state = forget_gate * state + input_gate * tf.tanh(update)
    output_gate = tf.sigmoid(split3)
    
    return output_gate * tf.tanh(state), state
```

残りは何も変えなくても動くはずです。

```
Average loss at step 7000: 3.161350 learning rate: 1.000000
Minibatch perplexity: 21.49
================================================================================
clound is the familics of ging in a net ilges has americals through oc guents and the ter valupers on bour that coance form is accounds the with at the acheck d
cns is arrarined matticuance in the beon seven outlash poldics of the weva as of hold on the round the larger ynosemon to the liferatian gormer neoin intrame to
vwjeop church jahn s ands and the tennen the cautch circet sho coman mulago instructed by igamains the volars about the lang one umpilition the them into iteria
graphy for refeared demonsources in westening history this fegurance conduction at cribeck in tex which is warf stractly the three brities cour general can secu
p typing to sell languagng but sarguor or could the meul the churcy pups the prividition lovely sounting varetics presidentifical traditur six the whili maked w
================================================================================
Validation set perplexity: 19.28
```

不思議な単語たくさん入っていますけど、そんなもんか。。。
デバッグのヒント：初期perplexity ≒ 729 (=dictionary_size)

### c- dropoutの導入

参考論文より、inputにdropoutかけて、previous_outputにかけないということです。
`embedding_lookup`の前か後かdropout入れるべきなのか迷いです。実質変わらない？
`lstm_cell`は以下のように修正：

```python
def lstm_cell(i, o, state, keep_prob):
    """Create a LSTM cell. See e.g.: http://arxiv.org/pdf/1402.1128v1.pdf
    Note that in this formulation, we omit the various connections between the
    previous state and the gates."""
    
    embed_i = tf.nn.embedding_lookup(final_embeddings,tf.argmax(i,1))
    drop_i = tf.nn.dropout(embed_i, keep_prob)

    big_matrix = tf.matmul(drop_i, ax) + tf.matmul(o, am) + ab
    split0, split1, split2, split3 = tf.split(1, 4, big_matrix)
    
    input_gate = tf.sigmoid(split0)
    forget_gate = tf.sigmoid(split1)
    update = split2
    
    state = forget_gate * state + input_gate * tf.tanh(update)
    output_gate = tf.sigmoid(split3)
    
    return output_gate * tf.tanh(state), state
```

残りは学習のとき、dropoutをかけますが、予測のときdropoutをかけないように修正すればよいです。

```python
  # Input data.
  train_data = list()
  for _ in range(num_unrollings + 1):
    train_data.append(
      tf.placeholder(tf.float32, shape=[batch_size, vocabulary_size]))
  train_inputs = train_data[:num_unrollings]
  train_labels = train_data[1:]  # labels are inputs shifted by one time step.

  # Unrolled LSTM loop.
  outputs = list()
  output = saved_output
  state = saved_state
  for i in train_inputs:
    output, state = lstm_cell(i, output, state, keep_prob=0.5)
    outputs.append(output)

  # State saving across unrollings.
  with tf.control_dependencies([saved_output.assign(output),
                                saved_state.assign(state)]):
    # Classifier.
    logits = tf.nn.xw_plus_b(tf.concat(0, outputs), w, b)
    loss = tf.reduce_mean(
      tf.nn.softmax_cross_entropy_with_logits(
        logits, tf.concat(0, train_labels)))

  # Optimizer.
  global_step = tf.Variable(0)
  learning_rate = tf.train.exponential_decay(
    10.0, global_step, 5000, 0.1, staircase=True)
  optimizer = tf.train.GradientDescentOptimizer(learning_rate)
  gradients, v = zip(*optimizer.compute_gradients(loss))
  gradients, _ = tf.clip_by_global_norm(gradients, 1.25)
  optimizer = optimizer.apply_gradients(
    zip(gradients, v), global_step=global_step)

  # Predictions.
  saved_output_prediction = tf.Variable(tf.zeros([batch_size, num_nodes]), trainable=False)
  saved_state_prediction = tf.Variable(tf.zeros([batch_size, num_nodes]), trainable=False)
  outputs_prediction = list()
  output_prediction = saved_output_prediction
  state_prediction = saved_state_prediction
  for i in train_inputs:
    output_prediction, state_prediction = lstm_cell(i, output_prediction, state_prediction, keep_prob=1.0)
    outputs_prediction.append(output_prediction)
  with tf.control_dependencies([saved_output_prediction.assign(output_prediction),
                                saved_state_prediction.assign(state_prediction)]):
    # Classifier.
    logits_prediction = tf.nn.xw_plus_b(tf.concat(0, outputs_prediction), w, b)
    train_prediction = tf.nn.softmax(logits_prediction)

  # Sampling and validation eval: batch 1, no unrolling.
  sample_input = tf.placeholder(tf.float32, shape=[1, vocabulary_size])
  saved_sample_output = tf.Variable(tf.zeros([1, num_nodes]))
  saved_sample_state = tf.Variable(tf.zeros([1, num_nodes]))
  reset_sample_state = tf.group(
    saved_sample_output.assign(tf.zeros([1, num_nodes])),
    saved_sample_state.assign(tf.zeros([1, num_nodes])))
  sample_output, sample_state = lstm_cell(
    sample_input, saved_sample_output, saved_sample_state, keep_prob=1.0)
  with tf.control_dependencies([saved_sample_output.assign(sample_output),
                                saved_sample_state.assign(sample_state)]):
    sample_prediction = tf.nn.softmax(tf.nn.xw_plus_b(sample_output, w, b))
```

結果：

```
Average loss at step 7000: 3.628081 learning rate: 1.000000
Minibatch perplexity: 29.62
================================================================================
age betwint expentun phare living mattur colon with rungcrish wecking peditated the alben b jentetion by empopeds willother det of juldotilol hissifization its 
 liberno wlast the mewes pasmed provosed to atpirsonn of a shown sest discition a trook to tho conflinations publican lecurs issight for bhe sriticially two vou
yr draketer of some fuers was ensially was swind the sime ating std to be an usepremas an archeres to chilnoot place form as invements of tour munlity famm righ
odhiactiond welley produaily and sevent barw will on to seff soonst of one nine two was shown the scuth colors materrated of clapudgidices is that designaly its
gwencect or the gords with a to the ustist consequal was sigust portiv and boy addost the interneds a clainst that mos wover i most quilians he seudmon since to
================================================================================
Validation set perplexity: 26.94
```

unigramよりperplexityが上がりますけど、どういうこと？
答：perplexityは次の単語の選択肢の数なので、bigramの場合はもし完全にランダムだったら次の単語の選択肢は27*27=729になります。unigramの場合はただ27ですね。
今回のモデルは729から26.94に減少できたので良いでしょう。

