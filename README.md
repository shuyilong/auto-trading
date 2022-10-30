# AUTO-TRADING PROJECT
Auto-Trading Robot With RL And GA

## 1. Data Process (Dr Zhang)
### 1.1 Data Structure
  我们用的是纽交所的股票高频数据，每有一次订单的提交都会产生一行数据，每一行数据都有现在的最佳bid和最佳ask，这两个价格作为成交价。在任何一个成交价之前的历史数据是可得的。我们以1/5分钟的数据作为最小的window。

### 1.2 Data Clean
  1. 我们的trading robot只能从历史价格变动信息中去学习如何做交易，所以没有办法获得盘外信息，为此我们把隔夜信息去掉，所以每天开盘后的15分钟和收盘前的15分钟不进行交易，也不获取信息。
  2. 我们参考【Terrence Hendershott, & Pamela C. Moulton (2011).】的方法进行数据清洗：
    (i) Eliminate records beyond the exchange opening time from 9:30 am to 4 pm;   
    (ii) Eliminate quotes with negative price or size or with bid price greater than ask price;   
    (iii) Eliminate trades with zero quantities;   
    (iv) Eliminate trades with prices more than (less than) 150% (50%) of the previous trade price; exclude quotes when the quoted spread exceeds 25% of the quote midpoint or when the ask price exceeds 150% of the bid price.  
   3. 计算了indicator后，仍然要对indicator进行winsorization 和 normalization。

## 2. Indicator Selection
  我们一共有三种类型的指标：窗口内技术指标；窗口内市场微观结构指标；长期指标（指上一个交易日）；这部分我们需要参考很多文献，并且我们要做特征选择（否则太多高度自相关的indicator没有意义）。
  
### 1. 窗口内技术指标
  1. Intra day technique indicators:  
    (1) 参考本文appendix：Hryshko*, A., & Downs, T. (2004) ， 这里有14个技术分析指标，可以做15/30/60分钟的指标。  
    (2) （待补充...）  

### 2. 窗口内市场微观结构指标
  这部分是比较难的，需要阅读很多文献来获取每个文献所设计的指标，而且需要一定的《微观市场结构》知识，所以我们看后期有没有时间吧，没有时间就暂时跳过；
  
### 3. 长期指标
  长期指标只是把1和2中的指标计算前一个交易日的数据即可；为了避免刚开始模型过于复杂，我们对“长期”只研究过去一个交易日的；

## 3. Model Generation

### 1. GA Part:To Get Strategies Perform Good In In-sample Data
  首先我们需要定义什么叫做策略：第2节我们计算的是指标，而一个策略是由多个指标信号和逻辑连接符所构成的，而信号是指指标所给出的买入或者卖出的建议。我们用一个例子来说明：  
  假如我们有2个indicator，记为A和B。这2个指标可以单独给出“买”或者“卖”的建议，所以每个指标都有一对结果，比如A_Buy和A_Sell，于是上述两个指标给出了4个信号，所以我们的策略就是就是由这些信号和逻辑符（AND OR）所构成，比如A_Buy and (B_Sell or A_Sell) then sell就代表一个策略（进入策略或者退出策略），表示如果指标A给出了买入指令，并且要么指标B给了卖出指令要么指标A给了卖出指令，的情况下就卖出的策略。注意，策略必须是由一个进入策略和一个退出策略组成的。  
  然后我们利用GA算法，在样本内数据对不同的策略（即信号和逻辑符的组合）进行计算，找出前5好的交易策略（一个进入-退出pair）。（这里有一个需要补充，信号的顺序是有影响的，但是如果所有顺序都去尝试，计算量太大，需要一个方法去解决这个问题。）  该算法的fitness可以依据股票的回报率或者风险报酬比率来计算。
  最后我们预期的结果是GA算法给我们profitable的strategies，所以在利用这些策略里面所出现的indicators进入下一步，RL自动交易。
 
### 2. RL Part:To Get Strategies Perform Good In In-sample Data
  1. 算法：具体算法可以尝试不同类型，暂时待定；  
  2. 环境变量：利用GA所得出的indicators构建0-1环境变量，比如只有两个indicators，A和B，A的buy和cell状态就是0和1，B同理。那么这个环境就有4种环境状态；
  3. 动作：就是仓位大小，可能的取值为【100，67，33，0，-33，-67，-100】；
  4. 奖励：依据股票的回报率或者风险报酬比率来计算；
  

