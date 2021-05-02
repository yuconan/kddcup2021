![炼丹笔记](https://pic1.zhimg.com/v2-071e132c7353c2be34deb925be1bf617_1440w.jpg "DOTA")
# KDD CUP 2021 Multi-dataset Time Series Anomaly Detection
### 作者： DOTA  
### 详解： [KDD Cup 2021：时间序列异常检测问题开源Baseline](https://mp.weixin.qq.com/s/BVVYuQuvVgNZ9VDe7bLBAw)  
### 知乎： [KDD Cup 2021：时间序列异常检测问题开源方案](https://zhuanlan.zhihu.com/p/88257675)  
### 数据： [KDD Cup 2021 Time Series Anomaly Detection](https://compete.hexagon-ml.com/practice/competition/39/)
### 微信公众号： 炼丹笔记 


#### KDD Cup 2021：时间序列异常检测  
![](https://pic2.zhimg.com/v2-631e7607f9cc0177494894e9ad42f221_r.jpg "DOTA")  
![](https://pic3.zhimg.com/v2-202ee9d92c3bb1be0f93b244fce969b6_r.jpg "DOTA")  
![](https://pic4.zhimg.com/v2-99c736b8099b9093816f1f30a3bb884f_r.jpg "DOTA")  
本次赛题的数据为时序数据，针对每条时序记录，需要选手完成具体的异常点定位。  
![](https://pic2.zhimg.com/v2-a698bb2987e9a41ad4c7a171b8a752c1_r.jpg "DOTA")  
文件的命名即分割了训练集和测试集，如下所示  

> <id>_<name>_<split-number>.txt  
e.g. 004_UCR_Anomaly_2500.txt.  

例子中，训练集和测试集的分割点为2500。  
![](https://pic2.zhimg.com/v2-c5330bcd604c3761b0b67e358a856bb5_r.jpg "DOTA")  
评测：如上图异常区域为2759至2820，最后答案定位的位置，在前后100的区间内都算正确。比如这里答案只要在2659 和 2920 区间内都算正确。  

![](https://pic3.zhimg.com/v2-626329fbdb631b170351e457ce428f3a_r.jpg "DOTA")  
时序异常检测算法非常多，这里介绍一些，有兴趣的同学可以尝试一波。  

- ARIMA  
- 基于聚类检测  
- 基于序列建模型的RNN、LSTM  
- Local Outlier Factor  
- One-Class SVM  
- Deep-SVDD  
- First Hour Average  
- Stddev from Moving Average  
- Mean Subtraction Cumulation  

![](https://pic1.zhimg.com/v2-fcb9c30a9e12c19c10d3f709cd887398_r.jpg "DOTA")  
本文开源主要使用的是基于AutoEncoder模型的时间序列异常检测算法。  

原始的时间序列往往面临维度高，存在噪声等情况，通过特征提取，对原始数据进行清洗和丰富等，会比直接将原始数据扔给神经网络要好。  

AutoEncoder是一种典型的无监督方法，可以将其扩展为Variational AutoEncoder，或者引入情景信息，从而扩展为Conditional Variational AutoEncoder。  

![](https://pic2.zhimg.com/v2-a2a48543ec079d3e873df28ebcdc8925_r.jpg "DOTA")  

训练集中的数据（无异常点的数据）：  
![](https://pic1.zhimg.com/v2-f3e8c34b02b7b856bed7542daab897b4_r.jpg "DOTA")  

测试集中的数据（有异常点的数据）：  
![](https://pic3.zhimg.com/v2-fcc4222604374cbc755a6ca55564f9d6_r.jpg "DOTA")  

自编码模型一般的神经网络，其内部结构呈现一定对称性。  

激活函数，一般会优先选择Relu，当Relu无法满足需求的时候，再去考虑其他激活函数，比如Relu的一些衍生版本Elu，它是Relu的其中一个优化版本，可以避免出现神经元无法更新的情况。  

训练模型很简单，只需要调用model.fit函数。虽然简单但是需要注意的是，对于AutoEncoder来说，输入和输出都是X_train特征， 除此之外在建模时划分出20%的数据集作为验证集，来验证模型的泛化性。  

查看训练的Loss历史曲线，发现训练集和验证集都很好的进行拟合，而且训练集并没有出现“反弹”，也就是没有过拟合的现象。  
![](https://pic3.zhimg.com/80/v2-6571ce82a98b8711e83cafaced1cdcee_1440w.jpg "DOTA")  

  
当自编码器训练好后，它应该能够学习到原始训练数据集的内在编码，然后根据学习到的编码，在一定程度内还原原始训练数据集中的数据。  
![](https://pic1.zhimg.com/v2-aacdd77ad4f7b759aafc753cb6adade0_r.jpg "DOTA")  

因为AutoEncoder学习到了“正常数据周期模式”的编码格式，所以当一个数据集提供给该自编码器时，它会按照训练集中的“正常数据周期模式”的编码格式去编码和解码。如果解码后的数据集和输入数据集的误差在一定范围内，则表明输入的数据集是“正常  的”，否则是“异常的"。  

第一个Sequence的拟合情况如下：  
![](https://pic2.zhimg.com/v2-e65aed84947a753bb343629ba71293f1_r.jpg "DOTA")  

  
异常节点为[1683，1684,，1685]，因为point分割的原因，最后提交的结果，在此基础上+point之后即可。  

总结  
![](https://pic2.zhimg.com/v2-fd237fe15641bd780e088ae0c8bd4b31_r.jpg "DOTA")  
本赛题的方法很多，此处开源的版本为基于卷积的AutoEncoder版本，同时基于统计的异常相关方法，线上提交也能取的不错的成绩。  

  
## 更多干货，尽在**炼丹笔记**   
![炼丹笔记](https://pic4.zhimg.com/50/v2-b04ed979e550209722eb85bfc4db79e7_r.jpg "DOTA")  
