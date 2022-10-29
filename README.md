# auto-trading
Auto-Trading Robot With RL And GA

## 1. Data Process (Dr Zhang)
### 1.1 Data Structure
  我们用的是纽交所的股票高频数据，每有一次订单的提交都会产生一行数据，每一行数据都有现在的最佳bid和最佳ask，这两个价格作为成交价。在任何一个成交价之前的历史数据是可得的。我们以1/5分钟的数据作为最小的window。

###1.2 Data Clean
  1. 我们的trading robot只能从历史价格变动信息中去学习如何做交易，所以没有办法获得盘外信息，为此我们把隔夜信息去掉，所以每天开盘后的15分钟和收盘前的15分钟不进行交易，也不获取信息。
  2. 我们参考【Terrence Hendershott, & Pamela C. Moulton (2011).】的方法进行数据清洗：
    (i) Eliminate records beyond the exchange opening time from 9:30 am to 4 pm; 
    (ii) Eliminate quotes with negative price or size or with bid price greater than ask price; 
    (iii) Eliminate trades with zero quantities; 
    (iv) Eliminate trades with prices more than (less than) 150% (50%) of the previous trade price; exclude quotes when the quoted spread exceeds 25% of the quote midpoint or when the ask price exceeds 150% of the bid price.
   3. 计算了indicator后，仍然要对indicator进行winsorization 和 normalization。

## 2. Indicator Selection
  我们一共有三种类型的指标：窗口内量价指标；窗口内微观结构指标；长期指标（指上一个交易日）；
  
### 2. 窗口内量价指标
