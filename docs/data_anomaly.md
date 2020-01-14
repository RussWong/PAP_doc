# 异常检测

检测数据集中的异常数据并剔除。

主要使用孤立森林的方法:

孤立森林法（Isolation Forest）是一种被广泛采用的集成异常检测算法。孤立森林由一系列孤立
树组成，每一颗孤立树是一个二叉树结构。具体实现步骤如下：

> 	-  从数据集中随机选择一定数量样本作为子空间放入孤立树根节点
> 	-  随机选择一个维度，并随机产生切割点将数据在此维度下切分为两个子集。
> 	-  递归上一步骤直到子节点只有一个样本点或者孤立树达到指定高度
> 	-  循环上述步骤直到生成了指定个数的孤立树
> 	-  对于每个数据点，计算其在每棵树的高度并取平均值，得到异常分数
> 	-  设定阈值判断数据异常。

## 项目实例

在项目实例中，调用sklearn中的集成模型IsolationForest完成异常检测功能，并使用matplotlib进行画图。

由于一次检测具有偶然性，因此进行多次检测迭代。当检测结果稳定时，终止检测，并剔除异常数据。

核心代码如下：


```python
from sklearn.ensemble import IsolationForest
clf = IsolationForest(behaviour='new', contamination='auto')
pred = clf.fit_predict(data)
data['Outlier'] = pred
tmp = list((set(res) & set(list(data[data['Outlier']==-1].index))))
if len(tmp) == len(res):
    plt_anomaly(data,ylabel,path)
    data.drop(res, inplace=True)
    data.reset_index(drop=True, inplace=True)
    break
else：
	res = tmp
    data.drop('Outlier', axis=1, inplace=True)
```

画图示例如下：



![WechatIMG12](/Users/huangzhiwei/Desktop/mkdoc_demo/docs/WechatIMG12.png)

