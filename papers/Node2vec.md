node2vec

对图上的顶点做embedding，做特征学习

目标函数：
![](figures/node2vec_0.png)

假设每个邻居结点的观测是相对独立的，那么

$$
Pr(N_S(u)|f(u)) = \prod_{n_i \in N_S(u)} Pr(n_i|f(u))
$$

而对每个邻居与当前顶点的条件概率为：
$$
Pr(n_i|f(u)) = \frac{\exp(f(n_i)\cdot f(u))}{\sum_{u\in V} \exp(f(v)\cdot f(u))}
$$
相当于一个softmax

下一步就是找出”邻居“ $N_S(u)$

## node2vec walk
![](figures/node2vec_1.png)

在网络的同质性（homophily）和结构性（structural equivalence）中进行权衡权衡。

- 网络的“同质性”指的是距离相近节点的embedding应该尽量近似

- “结构性”指的是结构上相似的节点的embedding应该尽量接近

<mark> DFS擅长学习网络的同质性，BFS擅长学习网络的结构性。</mark>

```scala
def setupEdgeAlias(p: Double = 1.0, q: Double = 1.0)(srcId: Long, srcNeighbors: Array[(Long, Double)], dstNeighbors: Array[(Long, Double)]): (Array[Int], Array[Double]) = {
    val neighbors_ = dstNeighbors.map { case (dstNeighborId, weight) =>
      var unnormProb = weight / q
      if (srcId == dstNeighborId) unnormProb = weight / p
      else if (srcNeighbors.exists(_._1 == dstNeighborId)) unnormProb = weight

      (dstNeighborId, unnormProb)
    }

    setupAlias(neighbors_)
  }
```




采样采用别名采样法 [[Alias Sample Method 别名采样]]

## node2vec algorithm
![](figures/node2vec_2.png)
1. $r$轮游走，每次走$l$步。代码中是从每个顶点同时开始游走
2. 每条游走的路径是为一个句子，使用word2vec进行embedding


## case study
论文中用 *悲惨世界* 中角色之间的关系图谱，来说明node2vec的游走通过设定p/q参数能够兼具BFS和DFS两种策略。

embedding之后使用kmeans对顶点进行聚类


## 多分类问题
embedding的向量送入 one-vs-rest L2逻辑回归。


## Link prediction