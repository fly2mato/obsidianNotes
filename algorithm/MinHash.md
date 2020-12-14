MinHash, 一种用于计算 Jaccard 距离的算法。


假设有user-items的对应关系，user: Array(items)
希望计算 user1 与 user2 的相似性。
$$
	Jaccard Similarity = \frac{ I1 \cap I2} { I1 \cup I2}
$$

现在假设 u1 与 u2 在所有的items中，都包含的元素共计x个，只有一个人包含的元素y个
那么
$$
	J = \frac {x} { x+y }
$$


现在对所有元素按照编号进行随机shuffle，然后取排在首位的 item_id, 记为H(User)
如果对两个用户得到的id相同，那么排在首位的元素必然在 x中，即
$$ P( H(U1) = H(U2) ) = \frac{x}{ x+y } $$

显然对items做N次随机shuffle-取min值的操作，就可以逼近这个概率值。



spark中：
1. 生成一组hash函数 $$ H(x,a,b)=((1+x)*a+b) \% 2038074743 $$，a/b为随机数
2. ```hashFuns.map { H => items.map(item => H(item)).min }``` ，生成hash编码vec
3. 按照hash编码的每一位，将user分组，最后保留配对的所有可能
4. user-user 的相似度仍然使用 items 来计算jaccard相似分

