Contrasting Learning for Story Point Estimation

场景：
现有一批文本数据，只有少部分有标记。使用对比学习的深度聚类进行训练。对比学习使用基于同义词替换的数据增强方式完成样本构造。
在一个mini-batch中，对于样本x1来说正样本为其增强后的样本x1_,负样本为该mini-batch中其他样本x2及其增强后的样本x2_。
损失函数包括对比损失（InfoNCE），聚类损失（KL散度）和纯净度损失（即聚类后属于同一个类别的簇包含不同真实标签数据的多少。
若该簇没有真实标签的数据或有真实标签的数据但都属于同一类别，则该损失为0，否则有越多不同类别的真实标签数据损失越大）。

方法思路
1. 划分数据：
从有标签数据按各类别的20%比例划分出验证集V1,剩余的标记数据未L和未标记数据N。

2. 初始化阶段：
使用标记数据L训练微调一个bert语言模型。

3. 伪标签生成：
用微调后的语言模型提取所有数据(L+N)的词嵌入特征，然后用k-means进行聚类，给所有数据打上伪标签。

4. 打真实标签：
遍历聚类结果，计算每一个类别的置信度（该类中具有某类真实标签数据的数量比上该类中具有真实标签数据的数量中的最大值），给置信度大于50%且数量大于3的类别打上该类的真实标签，此时L扩充为L=L+m，N缩减为N=N-m。

5.循环执行：
重复步骤2-4，直到所有N都被打上真实标签或达到最大的迭代次数。

6. 最终评估：
在独立的验证集V1上评估模型的最终性能，确保模型的有效性和泛化能力。
