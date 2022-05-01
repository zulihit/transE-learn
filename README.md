# transE

本仓库为个人学习所用，大量借鉴了
https://github.com/Anery/transE
该代码库的作者在
https://github.com/Anery/transE
基础上进一步提高了训练的速度
建议了解transe的基本思想和代码以后，不必深究本代码每一处的实现细节，后续以基于pytorch的更系统的kge代码进行学习，例如
https://github.com/DeepGraphLearning/KnowledgeGraphEmbedding
https://github.com/TimDettmers/ConvE

## 感谢原代码作者的贡献，本代码整体易于理解，适合初学者学习，我在上述代码中加入了一些根据自己理解的注释，基本没有修改原作者的代码，后续会将transe的相关学习记录保存下来
	
### 文件结构说明
1. 训练和测试的代码放在src文件夹下
2. 训练和测试的结果放在res文件夹下，经过1001个epoch的训练，损失约为14000（其实300个epoch的时候就基本固定了）。

#### 关于transE：
论文原文：[Translating embeddings for modeling multi-relational data](http://papers.nips.cc/paper/5071-translating-embeddings-for-modeling-multi-rela)

#### 1 训练数据

FB15k.

#### 2. 训练transE

- Tbatch更新：在update_embeddings函数中有一个deepcopy操作，目的就是为了批量更新。这是ML中mini-batch SGD的一个通用的训练知识，在实际编码时很容易忽略。
- 两次更新：update_embeddings函数中，要对correct triplet和corrupted triplet都进行更新。虽然写作$(h,l,t)$和$(h',l,t')$，但两个三元组只有一个entity不同（前面说了，不同时替换头尾实体），所以在每步更新时重叠的实体要更新两次（和更新relation一样），否则就会导致后一次更新覆盖前一次。
- 关于L1范数的求导方法：参考了[刘知远组实现](https://github.com/thunlp/KB2E)中的实现，是先对L2范数求导，逐元素判断正负，为正赋值为1，负则为-1。
- 超参选择：对FB15k数据集，epoch选了1000（其实不需要这么大，后面就没什么提高了），nbatches选了400（训练最快），embedding_dim=50, learning_rate=0.01, margin=1。

 #### 3. 测试
- isFit参数：区分raw和filter。filter会非常慢。
