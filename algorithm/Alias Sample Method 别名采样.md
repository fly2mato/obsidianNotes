### 原理

https://blog.csdn.net/haolexiao/article/details/65157026
https://www.keithschwarz.com/darts-dice-coins/

- 使用O(1)的时间复杂度，从均匀分布生成 一组给定的概率分布
- 给定的概率分布 p(xi) = [1/2, 1/3, 1/12, 1/12]
	![](figures/alias_sample_fig_1.png)
	
- 预处理成n列，每列最多两个取值。
	![](figures/alias_sample_fig_2.png)
	
- 从均匀分布产生两个随机数a in [0, 1, 2, ..., n-1], b in [0, 1). a用来判断在哪一列, 再根据b判断取这一列上的具体值。
- 预处理的方法：对给定的概率分布排序后，用超过均分值的概率补充小于均分值的概率。


### code
```python
import numpy        as np
import numpy.random as npr
 
def alias_setup(probs):
    '''
    probs： 某个概率分布
    返回: Alias数组与Prob数组
    '''
    K       = len(probs)
    q       = np.zeros(K) # 对应Prob数组
    J       = np.zeros(K, dtype=np.int) # 对应Alias数组
    # Sort the data into the outcomes with probabilities
    # that are larger and smaller than 1/K.
    smaller = [] # 存储比1小的列
    larger  = [] # 存储比1大的列
    for kk, prob in enumerate(probs):
        q[kk] = K*prob # 概率
        if q[kk] < 1.0:
            smaller.append(kk)
        else:
            larger.append(kk)
 
    # Loop though and create little binary mixtures that
    # appropriately allocate the larger outcomes over the
    # overall uniform mixture.
    
    # 通过拼凑，将各个类别都凑为1
    while len(smaller) > 0 and len(larger) > 0:
        small = smaller.pop()
        large = larger.pop()
 
        J[small] = large # 填充Alias数组
        q[large] = q[large] - (1.0 - q[small]) # 将大的分到小的上
 
        if q[large] < 1.0:
            smaller.append(large)
        else:
            larger.append(large)
 
    return J, q
 
def alias_draw(J, q):
    '''
    输入: Prob数组和Alias数组
    输出: 一次采样结果
    '''
    K  = len(J)
    # Draw from the overall uniform mixture.
    kk = int(np.floor(npr.rand()*K)) # 随机取一列
 
    # Draw from the binary mixture, either keeping the
    # small one, or choosing the associated larger one.
    if npr.rand() < q[kk]: # 比较
        return kk
    else:
        return J[kk]
 
K = 5
N = 100
 
# Get a random probability vector.
probs = npr.dirichlet(np.ones(K), 1).ravel()
 
# Construct the table.
J, q = alias_setup(probs)
 
# Generate variates.
X = np.zeros(N)
for nn in xrange(N):
    X[nn] = alias_draw(J, q)

```
