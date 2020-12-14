## skip-gram

给定一个句子，包含单词 {w1, w2, ..., wT}， 则Skip-gram模型的目标函数是去最大化如下的平均对数条件概率：

$$
\frac 1 T \sum_{t=1}^T \sum_{-c \le j \le c, j \ne 0} \log p(w_{t+j}|w_t)
$$

![](figures/skip_gram.png)

也就是平均计算每个词对(traning samples) 的条件概率。

论文中定义

$$
p(w_O|w_I) = \frac{\exp(v_{w_O}' ^ T v_{w_I})}{\sum_{w=1}^W \exp( v_w' ^ T v_{w_I})}
$$

这个等式的右端怎么理解？右端的结果就是对向量做一次softmax。只不过对于两种向量$v$和$v'$是什么需要注意下：

<mark>参考这篇论文</mark>[word2vec Parameter Learning Explained](chrome-extension://ikhdkkncnoglghljlkmcimlnlhkeamad/pdf-viewer/web/viewer.html?file=https%3A%2F%2Farxiv.org%2Fpdf%2F1411.2738.pdf)

![](figures/word2vec_vec2.png)

$v_i$ 表示的是单词 $w_i$ embedding之后的编码，其实就是矩阵$W$中${w_i}$对应的第$i$列。

$v_i'$表示的是在矩阵$W'$中对应的第$i$列，也就是输出层生成单词$w_i$对应值时使用的那一列。

![](figures/word2vec_vec.png)


## CBOW
和skip-gram基本类似，只是在训练集上，一个句子按照窗口切分的时候，窗口中的词会同时作为输入，也就是一次取$W$矩阵的多列，然后做平均，再进行后续计算。



## Hierarchical softmax 
1. 将词表按照词频构造出一颗 [[Huffman tree]]，每个词与叶子结点一一对应。
2. 输出矩阵$W'$的列向量与Tree的非叶子结点一一对应。
3. 定义$p(w_O|w_I)$是从跟结点遍历到$w_O$的路径上，每个非叶子结点的概率相乘
4. 假定每个非叶子结点上计算的逻辑回归概率是向左子树移动的概率，则有：

$$
p(w|w_I) = \prod_{j=1}^{L(w)-1} \sigma([\![ n(w,j+1) = ch(n(w,j))]\!] \cdot {v'_{n(w,j)}}^{\mathrm{T}} v_{w_I})
$$


## Negative Sampling, 负采样
- 相对于分层softmax，也可以使用负采样的方式提升训练效率。
- 根据词表中每个词的词频，从中采样出k个负样本用于更新矩阵向量值。
- 目标函数就定义为这k+1个的逻辑回归概率的计算结果：

<mark>这个目标函数是怎么来的？</mark>

$$
\log P(w_O|w_I) = \log \sigma({v'_{w_O}}^T v_{w_I}) + \sum_{w_j \in W_{neg}} \log \sigma(-{v'_{w_j}}^T v_{w_I}) 
$$

优化目标：增加正样本(window中出现的词)的概率，减小window外其他词出现的概率




## Subsampling，下采样
- 目的是减少高频词的训练样本


通过滑动窗口生成训练样本的时候，对文本中的每一个词，按照如下的公式计算出一个被过滤掉的概率。

$$
P(w_i) = \sqrt{1+\frac{f(w_i)}{t}} * \frac{t}{f(w_i)}
$$

```c
// The subsampling randomly discards frequent words while keeping the ranking same
if (sample > 0) {
  real ran = (sqrt(vocab[word].cn / (sample * train_words)) + 1) * (sample * train_words) / vocab[word].cn;
  next_random = next_random * (unsigned long long)25214903917 + 11;
  if (ran < (next_random & 0xFFFF) / (real)65536) continue;
}
```


#### discussion
![[discusssion#word2vec]]
