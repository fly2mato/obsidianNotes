[Graph-based Anomaly Detection and Description: A Survey](https://arxiv.org/abs/1404.4679)

> **Abstract**
> As a key contribution, we give a general framework for the algorithms categorized under various settings: unsupervised vs. (semi-)supervised approaches, for static vs. dynamic graphs, for attributed vs. plain graphs. 
> We highlight the effectiveness, scalability, generality, and robustness aspects of the methods. What is more, we stress the importance of anomaly attribution and highlight the major techniques that facilitate digging out the root cause, or the ‘why’, of the detected anomalies for further analysis and sense-making. 

##### 1.1 Outliers vs. Graph Anomalies
异常值和图异常有什么区别？
- 将数据视为多维空间上的点，那么异常检测的结果找到的就是异常值/异常点
- 将数据按照关联关系建图，检测的结果就是图异常
- 数据的建模形式。

同一份数据的某个离散特征，是不是既可以建模成特征-值，也可以按照共现性生成边？

建模成图结构的必要性：
- 数据本身就是具有关联关系的，比如 关系网络、社交网络、email-phone 网络等
- 数据的表现方式更强
- 问题本身就具有关系特性
- 机制更鲁棒：虽然一些特征信息会被篡改，但是作弊者无法获知全局信息，所以即使伪造了一些特征，也能很难让自己完美融入图结构中。

##### 1.2 Challenges
> It stems from the fact that no unique deﬁnition for the problem of anomaly detection exists.
> 异常检测问题本身并没有统一的定义


![[concept/outlier#Hawkins’ Deﬁnition of Outlier 1980]]

基于图的异常检测问题
> Given a (plain/attributed, static/dynamic) graph database
> Find the graph objects (nodes/edges/substructures) that are rare and that differ signiﬁcantly from the majority of the reference objects in the graph.


###### 数据特有的挑战
1. 数据量级庞大，且动态更新
2. 数据形式复杂

###### 异常检测问题特有的挑战
1. 缺乏有效标签，没有充足的人力评估。即使人力资源无穷，人给出的标签也充满了噪声。对相同的问题人工会给出相反的结论。
> ？后半句不太充分，如果人力充足，可以通过多人重复评估的方式提升准确度？
2. 样本不平衡
3. 强对抗
4. 异常归因很困难

###### 图结构特有的挑战
1. 对象相互关联。需要谨慎的处理异常的扩散。？
2. 图上异常的定义多样化。不同类型的欺诈会对应不同形式的异常表现。
3. 搜索空间的尺寸



### Anomaly detection in static graphs
> Deﬁnition 3 (Static-Graph Anomaly Detection Problem)
> Given the snapshot of a (plain or attributed) graph database
> Find the nodes and/or edges and/or substructures that are “few and different” or deviate signiﬁcantly from the patterns observed in the graph.

2.1 static plain graphs
2.1.1 只包含图的结构信息
- 基于图特征，[[ODDBALL]]
- 基于接近度，[[PageRank]], [[PPR]], [[SimRank]]

2.1.2 基于社区的方法



### Anomaly detection in dynamic graphs



### Graph-based anomaly description



### Graph-based anomaly detection in real-world applications













