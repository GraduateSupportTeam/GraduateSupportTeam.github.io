---
title: contrastive_learning
date: 2022-10-29 00:18:23
mathjax: true
---
# 对抗损失

## 自监督对抗损失

### 网络

{% asset_img network.jpg 举例网络 %}


### 损失函数

{% asset_img loss.png 损失函数 %}


$\cdot$ 代表点积

$i$是一个样本，$j(i)$是与$i$来自同一分布的样本，$A(i)$是所有样本

🥥 比如：

$i$和$j(i)$来自同一张图片的增强图片 i.e. 同一分布

[Softmax](https://www.notion.so/Softmax-6cfb69b063334d2aa55cb8bfd9639d28)

形象解释：在特征空间，相同label的特征被拉近，不同的特征被拉开

### 特点

自监督：不需要label

预训练：可以提升性能

因为没有label，所以（边牧实际上是负样本）经过训练会被推到远离当前空间的位置，为后面的训练增加难度

**优点**：生成unlabeled samples is cheap

**缺点**：如果同一label有不同的样本（来自同一样本

{% asset_img paint.png 图解说明 %}
## 改进👇

### 1. **有监督的**

{% asset_img mechanism.png 有监督和无监督的区别 %}

- 增强不再是必要的形式

### 2. **Loss的两种变形**

***变形1***

{% asset_img 2.png 变形损失函数1 %}

$P(i) = {p \in A(i):\mathcal{y_p}={y_i}}$, 与$i$不同的positive样本的indices，$|P(i)|$是势（总个数）

里面的$\sum$计算了当前样本$i$与其所有positive的对抗损失

外侧的$\sum$计算了所有样本$i\in I$

***变形2***


{% asset_img 3.png 变形损失函数2 %}

里侧的$\sum$提到了log里

**均具备的优点**

（1）这种形式促使同一label的样本在特征表达上更加相似

- 补充公示推导
    
    对于变形2
    
    对每种label的损失
    
    
    {% asset_img 9.png 变形1的梯度值推导 %}
    
    
    {% asset_img 1010.png 变形1的梯度值推导 %}
    
    $P_{ip}$ 是关于所有正负样本的可能值（在所有样本里）,$X^{in}_{ip}$仅关于正样本（在正样本中的区分）
    
    变形1与变形2的推导结果是一样的，但是
    
    
    {% asset_img 15.png 变形2的推导 %}
    变形1的$z_p$是同一label的所有正样本表达的均值，所以更鲁邦（？）
    

（2）有利于hard positive/negative的信息挖掘

难分正负样本的贡献的梯度值更大，且随着负样本数量的增加，训练效果越好（作者的推测）

变形1，2作为自监督的泛化版本，利用了这种优势

- loss关于上层的梯度
    
    
    {% asset_img 16.png  %}
    
    $z_i$是特征，$w_i$是归一化前的结果，$z_i = w_i/||w_i||$
    
    
    {% asset_img derivation.png %}
    
    对于易分的样本，对总体梯度的贡献很小
    
    
    {% asset_img weight0.png %}
    
    对于不易分的样本
    
    {% asset_img weight1.png %}
    

**2，3的优劣：**

根据杰森不等式

{% asset_img less.png %}

变形2的loss 小于等于 变形1的loss

所以变形1的loss是变形二的上界，效果也更好

{% asset_img result.png %}

在图像分类上的结果也证明了变形1优于变形2
