# 学习资料

[各社区](https://support.worldquantbrain.com/hc/en-us/community/topics)
[中文论坛](https://support.worldquantbrain.com/hc/en-us/community/topics/12913416465431-%E4%B8%AD%E6%96%87%E8%AE%BA%E5%9D%9B)
[【学习资料】一文读懂BRAIN的量化学习体系：从入门到高阶](https://support.worldquantbrain.com/hc/en-us/community/posts/24472160509719--%E5%AD%A6%E4%B9%A0%E8%B5%84%E6%96%99-%E4%B8%80%E6%96%87%E8%AF%BB%E6%87%82BRAIN%E7%9A%84%E9%87%8F%E5%8C%96%E5%AD%A6%E4%B9%A0%E4%BD%93%E7%B3%BB-%E4%BB%8E%E5%85%A5%E9%97%A8%E5%88%B0%E9%AB%98%E9%98%B6)
[【学习资料】BRAIN平台自学路径图推荐](https://support.worldquantbrain.com/hc/en-us/community/posts/22863075241623--%E5%AD%A6%E4%B9%A0%E8%B5%84%E6%96%99-BRAIN%E5%B9%B3%E5%8F%B0%E8%87%AA%E5%AD%A6%E8%B7%AF%E5%BE%84%E5%9B%BE%E6%8E%A8%E8%8D%90)

优先通过自学路径图掌握基本能力，通过 1w 分门槛吧

# TOSub
![[Pasted image 20241006180525.png]]
ts_rank(operating_income / close, 252)+rank(open/close)-ts_delta(returns, 10)

# ack

纪念一下我的首通表达式：
```
rank(open/close)+(sqrt(ts_rank(operating_income,300)))
```

记录一下群友的好想法明天用（手动狗头）：
```
ts_rank(operating_income / close, 252)
```


# How to develop quant ideas

1、collect data and info
2、come up with an idea or hypothesis
3、translate idea into mathemetical model
4、backtest the idea on historical data
5、check for robustness

平台会提供 1、4、5 （数据、历史数据的一键测试、各种指标测试）

# What is an alpha

Aim: how to distribute capital among a group of financial instruments.
如何在一组金融工具中分配资金（同时确保下行保护和良好收益）

Develop equity long-short market neutral alphas
为了下行保护，开发“股票多空市场中性alpha”

> "Long Positions" 做多股票：买入该股票，股票升值就赚钱；
> "Short Positions" 做空股票：（从 broker 借入你并不拥有的股票，卖出，并期望它贬值。此时你可以用一个低的价格买回该股票并返还借来的股票）

> Equity LONG-SHORT market neutral strategy, used commonly by hedge funds  
> 股票多空市场中性投资法是对冲基金主要采用的一种投资策略
> Equal dallor amount of long positions and short positions .
> 同时持有预计升值和预计贬值的股票，如果两者份额相同，则对冲基金收益
> Can work even if long positions declines in value, because profits from shorting can be higher than losses from long positions.
> 即便要做多的股票贬值，但是做空的收益会高于损失，所以对冲基金仍然收益

> GOAL：minimize exposure to the market in general, and profit from a change in the spread between two stocks.
> 策略的目标是在将市场风险降至最低，并从两只股票的价差变化中获得最大的模拟利润。

Alpha 是从股票池中预测股票价值的一个向量，每个值每天都可以更改，并拥有两个属性：
> Sign：positive 买入，negative 卖出
> Magnitude：买卖的量

如何创造 alpha：
1、产生 idea
2、实现 idea
	实现方式有两种：
		1、通过一个简单的表达式
		2、通过 Python
	表达式和 Python code 会在历史数据运行并输出 alpha
3、对生成的 alpha 进行操作，使其变为多空中性，并发挥预测能力
4、得到统计数据（产生的利润或损失）
	

## Making alpha：simulation settings

- Region 地区
- Universe 股票池
	- 选择流动性最强的多少支股票进行预测，对于前 500 或 前200 这样的高流动性股票池，构建 alpha 相对困难。因为这些股票的可交易量更高、信息被吸收的更快。但这样的 alpha 也更有价值，因为其附带的交易成本更低
-  Delay 延迟
	- 用于 alpha 数据的时间安排，Delay-1 使用昨天的价格，Delay-0 使用当天的价格
- Decay 衰减（未理解）
	- 提供过去指定天数的 alpha 值的加权总和 
- Neutralization 中性化
- Max stock weight 最大股票权重
	- 避免将大量资金放到少数几只股票上（通常建议值为 0.05~0.1）
- Simulation Duration 模拟期限
	- 建议至少五年
- Lookback days 回溯天数
	- alpha 通常需要过去的信息来生成当天的仓位，所以该值不能小于你“回溯”的时间。


