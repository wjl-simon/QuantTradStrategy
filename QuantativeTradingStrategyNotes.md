# 经典量化交易策略笔记
##### Author：SIMON  
##### Date：JULY 2021

***

## MIST：有用资源传送门
* Python量化交易笔记（偏实操，主要看策略的python实现逻辑）：<https://blog.csdn.net/m0_46603114/category_9820181.html>


***

# 0. FUNDATIONS

## 0.1 $\alpha$

任何一个投资策略的收益率都可以分为两部分：  
1. 与市场基准完全相关  
2. 与市场基准不相关。

Alpha的含义如下：

>与市场不相关的（非系统性风险）就是Alpha（与投资产品表现与投资者自主策略水平有关）, 主要反应**超额收益水平**，应当越大越好。

因此，当阿尔法为正时即为正常超越，阿尔法为负时即为反向超越。


## 0.2 $\beta$
而Beta则与Alpha相反，含义如下：

>Beta反映**系统性风险**，贝塔反映的是弹性、市场波动性，换句话说，就是资产的收益对市场波动的敏感度，应当越小越好。


而**Beta收益就是与市场完全相关的那部分收益**。相对来说Beta收益更容易获得，比如持有成本低廉的指数基金。所以我们可以看出，策略收益相对重要的组成部分就是Alpha收益。

在进行投资组合建立时，市场行情决定了对贝塔的选择。在上涨的行情中，量化投资人利用贝塔值比较高的基金可以得到较高市场上涨带来的盈利。在不算明朗的行情中，贝塔值较低的基金可以更好的进行防御。

## 0.3 $\alpha$ vs $\beta$
对于基金多头的交易者来说，不管市场行情如何，阿尔法都是越大越好，交易者都希望能够获得正的超额收益。但是当市场行情下跌时，即使是拥有高超基金选择能力的交易者都抵挡不住市场下跌带来的损失。因此对于希望获得绝对收益的投资者来说，单边持有基金很难避免系统性的风险,这时阿尔法对冲策略为投资人获取绝对收益提供了可能。

保留超额收益，冲掉所有系统性风险是阿尔法策略的基本思想。执行该策略的投资人只要选择较高阿尔法的基金就可以了，并且不用担心市场的行为，因为此时的系统风险已经完全实现对冲。

我们都知道对冲是具有两面性，**一方面如果未来一段时间行情上涨，投资人不会获得收益，另一方面，如果行情下跌，投资人则能够避免市场下跌的风险**。

贝塔和阿尔法值的稳定性绝对了阿尔法策略是否有效。只有当他们两个的值都相对稳定的时候，预期收益才会比较确定。
阿尔法策略一般被应用在市场效率相对较弱的市场上。因为从以往的经验来看。在强式有效的市场，一般不存在或者很难寻找稳定的alpha。

## 0.4 开仓、持仓、平仓
开仓、建仓：初次买入
持仓：持有
平仓：可理解为1、指“调整”仓位（买入、卖出至新设置的仓位）；2、单纯指卖出


## 0.5 时间序列分析
传送门（包含基础、初级、进阶、应用，只看基础篇了解基础概念）：<https://zhuanlan.zhihu.com/p/38322638>


## 0.6 k阶段差分
对于序列$x[t]$，其k阶差分为：
$$
\Delta^{k}x[t] = \Delta^{k-1}x[t + 1] - \Delta^{k-1}x[t]
$$

参考资料：<http://mathonline.wikidot.com/higher-order-differences#:~:text=Recall%20from%20The%20Difference%20Operator,)%20%2D%20f(x)%24.&text=Definition%3A%20If%20is%20a%20real,1%7D%20f(x)%24.>


## 0.7 回撤

指在某一特定的时期内，账户净值**由最高值（或极高值）一直向后推移，直到净值回落到最低值（或极低值），这期间净值减少的幅度。**在选定的时间段内，有时会有好几次净值回落的情形，这时选取其中一段最大的回落情形，作为**最大回撤(maximum drawdown)**。因而，最大回撤不一定是：最高点净值—最低点净值，它也许会出现在好几段回落中的某一段。这来源于数学中的极值概念，就是将曲线划定一定的区间范围，在该区间范围内，将每一段下降的曲线的两端值（极高值和极低值）相减，计算出多个差值，取其中最大的差值作为最大回撤。  

最大资金回撤率是投资分析中的一个重要风险指标，主要是用来衡量账户在最糟糕的情况下的风险承受能力，公式如下：

$$
最大回撤率 = （前最高值—期间最低值）/前最高值。
$$

```
假设你的账户开户本金为5,000美元，在投资或者交易的过程中，你的账户升至了10,000美元，之后又跌至了4,000美元，然后又增长到了12,000美元，接着又跌至了3,000美元，当前又涨至13,000美元。

在这种情形下，资金经历了两次回撤：第一次回撤是10,000回落至4,000美元，回撤幅度为6,000美元，回撤率为6000/10000=60%；第二次回撤是12,000回落至3,000美元，回撤幅度为9,000美元，回撤率为9000/12000=75%。

因而，该账户在开户起至今，最大回撤为75%。
```


## 0.8 夏普率
夏普比率（Sharpe Ratio）定义如下：
$$
\text{SharpeRatio} = \frac{E[r_p]-r_f}{\sigma_p}
$$
其中$E[r_p]$为投资组合的期望超额回报率，$r_f$为无风险回报率,$\sigma_p$投资组合回报率的标准差。  

夏普率的目的是计算投资组合每承受一单位总风险，会产生多少的超额报酬。比率依据资产配置线(Capital Allocation Line,CAL)的观念而来，是市场上最常见的衡量比率。当投资组合内的资产皆为风险性资产时，适用夏普比率。夏普指数代表投资人每多承担一分风险，可以拿到几分超额报酬；若大于1，代表基金报酬率高过波动风险；若为小于1，代表基金操作风险大过于报酬率。这样一来，每个投资组合都可以计算Sharpe Ratio,即投资回报与多冒风险的比例，这个比例越高，投资组合越佳。



## 0.9 K线
K线包括*实体*和*影线*。
### 实体
实体即收盘价与开盘价之差，会被涂色形成一个矩形。实体分为两种：

* **阳实体**：收盘价大于开盘价的称为阳实体（矩形上边为收盘价，下方为开盘价）；整个实体会被涂为色（可能为红可能为绿可能为白，取决于习惯），成为*阳线*；
* **阴实体**：收盘价小于开盘价的称为阳实体（矩形上边为开盘价，下方为收盘价）；整个阴实体也会被涂色，成为阴线

### 影线
实体的上下方还各连接有一条竖直垂线，可分为两种：
* **上影线**：从实体向上延伸的细线。在阳线中，其为当日最高价与收盘价（阳线矩形上边）之差；在阴线中，期为当日最高价与开盘价（阴线矩形上边）只差。
* **上影线**：从实体向下延伸的细线。在阳线中，其为当日开盘价（阳线矩形下边）与最低价之差；在阴线中，为当日收盘价（阴线矩形下边）与最低价之差。

由於K線型態有非常多種，這邊先介紹兩個最基本的，都是由3根連續K棒組成：
### Moring star 晨星
![moring star](https://i.imgur.com/24c53DA.png)

第1根是陰線，第2根可以是陽線或陰線，第3根是陽線。
第2根的開盤價與收盤價(也就是灰色方塊的部分)都要小於第1根的收盤價與第3根的開盤價。
第3根的方塊長度要大於第1根的綠色方塊長度的一半。
Morning star的意義，在第1根大黑K之後，接第2根漲跌幅度較小的K棒，代表跌幅收斂，緊接著第3根大紅K，表示趨勢由下跌反轉為上漲行情。

### Evening star 夜星
![evening star](https://i.imgur.com/iBEV4EO.png)

第1根是陽線，第2根可以是陽線或陰線，第3根是陰線。
第2根的開盤價與收盤價(也就是灰色方塊的部分)都要大於第1根的收盤價與第3根的開盤價。
第3根的方塊長度要大於第1根的方塊長度的一半。
Evening star的意義，在第1根大紅K之後，接第2根漲跌幅度較小的K棒，代表漲幅收斂，緊接著第3根大黑K，表示趨勢由上漲反轉為下跌行情。


## 0.10 EMA （指数移动平均值）
EMA（Exponential Moving Average）是指数移动平均值。也叫 EXPMA 指标，它也是一种趋向类指标，指数移动平均值是以指数式递减加权的移动平均。

对于序列${x_n}$，其前$n$项的周期$N$的指数移动平均$\text{EMA}_N$定义如下:

$$
\text{EMA}_N(x_n) = \frac{2}{N+1} \sum^{\infty}_{k=0} (\frac{N-1}{N+1})^{k}x_{n-k}
$$

EMA即指数平均数指标（ Exponential Moving Average， EXPMA或EMA），也是一种趋向类指标。其构造原理是：对收盘价进行加权算术平均，用于判断价格未来走势的变动趋势。与MACD指标、DMA指标相比，EMA指标由于其计算公式中着重考虑了当天价格（当期）行情的权重，决定了其作为一类趋势分析指标，在使用中克服了MACD指标对于价格走势的滞后性缺陷，同时，也在一定程度上消除了DMA指标在某些时候对于价格走势所产生的信号提前性，是一个非常有效的分析指标。

## 0.11 标的 & 标的物

### 标的 （subject）
标的，为了确定当事人追求的司法效果的基本内容而要求当事人自己必须予以明确确定的那一部分内容，如货物交付、劳务交付、工程项目交付等。简单说，**标的是指合同当事人之间存在的权利义务关系。**  

它是合同成立的必要条件，是一切合同的必备条款。标的的种类总体上包括财产和行为，其中财产又包括物和财产权利，具体表现为动产、不动产、债权、物权等；行为又包括作为、不作为等。

### 标的物 （subject matter）
标的物是指**当事人双方权利义务指向的对象**。标的物指买卖合同中所指的物体或商品。举例说明，在房屋租赁中，标的是房屋租赁关系，而标的物是所租赁的房屋。  

标的和标的物并不是永远共存的。一个合同必须有标的，而不一定有标的物。举例说明，在提供劳务的合同中，标的是当事人之间的劳务关系。而在劳务合同中，就没有标的物。

## 0.12 头寸，仓位（position）

作者：张晴
链接：https://www.zhihu.com/question/52506610/answer/257874531
来源：知乎
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。

Position  部位（持仓、头寸、仓位）Position is the amount of cash, securities, commodities and to wider extent all financial instruments held and/or owned by a physical person or legal entity.部位（持仓、头寸）是**个人或法人持有的和/或拥有的现金、证券、商品等金融工具的总金额。**


>个人观点
>
>部位（持仓、头寸）本质上是**某种形式的资金**：可以是货币形式，也可以是非货币形式；可以是现货（现金、证券、商品等）形式，也可以是非现货（与各种现货对应的期货、期权等）形式。

> **更详细解释**
>
> 链接：<https://www.investopedia.com/terms/p/position.asp>
> 
> A **position is established (be in  open position 建仓，开仓)** when a trader or investor executes a trade that does not offset an existing position，是一个动作。
>
> **Open positions** can be either long, short, or neutral in response to the direction of its price.
> 
> Positions can be **closed (be in close position，关仓、平仓)** for either a profit or loss **by taking the opposite position**; for instance, selling shares that were purchased to open a long position.
> 
> Positions may be closed voluntarily or involuntarily—as in the case of a *forced liquidation (强制清算)* or a bond that has reached maturity.



In financial trading, a position is a binding commitment to buy or sell a given amount of financial instruments, such as securities, currencies or commodities, for a given price.在金融交易中，部位（持仓、头寸）是以指定价格买入或卖出指定数量金融工具（如证券、货币或商品）的具有法律约束力的承诺。  

In derivatives trading or for financial instruments, the concept of a position is used extensively. 在金融衍生产品交易中，或对于金融工具，“部位（持仓、头寸）”概念被广泛用运用。  

There are two basic types of position: a long and a short. 做多部位（持仓、头寸）和做空部位（持仓、头寸）。  

Generally speaking, long positions stand to gain from a rise of the price of the instrument and short positions from a fall (but with options the situation is more complicated).**一般来说，做多部位（持仓、头寸）可以从金融工具的价格上涨中获利，而做空部位（持仓、头寸）可以从价格下跌中获利（期权的情况较为复杂）。**  

Options will be used in the following explanations. 下面的解释以期权为例。The same principle applies for futures and other securities. 同样的道理适用于期货和其它证券。For simplicity, only one contract is being traded in these examples.为了简单起见，在这些例子中只交易一个合约。

### 0.12.1 开仓的风险（exposure）
An open position represents market exposure for the investor.**The risk exists until the position closes （平仓后可完全消除开仓带来的exposure敞口风险）**. Open positions can be held from minutes to years depending on the style and objective of the investor or trader.

Of course, portfolios are composed of many open positions. The amount of risk entailed with an open position depends on the size of the position relative to the account size and the holding period. Generally speaking, **long holding periods are riskier because there is more exposure** to unexpected market events.

**The only way to eliminate exposure is to close out or hedge against the open positions（消除开仓带来的敞口风险要么平仓、要么对冲）**. Notably, closing a short position requires buying back the shares, while closing long positions entails selling the long position.

### 0.12.2 平仓 （close a position）
In order *to get out of an open position, it needs to be closed*. A long will sell to close; a short will buy to close. **Closing a position thus involves the opposite action** that opened the position in the first place.  

The *time period between the opening and closing of a position in a security indicates the **holding period (持仓时间)** for the security*. This holding period may vary widely, depending on the investor's preference and the type of security. For example, day traders generally close out trading positions on the same day that they were opened, while a long-term investor may close out a long position in a blue-chip stock many years after the position was first opened.

### 0.12.3 Spot vs. Futures Positions 现货仓位、期货仓位
A direct position in an asset that is designed to be *delivered immediately* is known as a **“spot" or cash position 现货仓位**. Spots can be delivered literally the next day, the next business day, or sometimes after two business days if the security in question calls for it. On the transaction date, **the price is set but it generally will not settle at a fixed price**, given market fluctuations.

Transactions that are *not spot* may be referred to as **“futures” or “forward positions,” （期货仓位）** and while the price is still set on the transaction date, the settlement date when the transaction is completed and the security delivered can occur in the future. These are indirect positions since they do not involve outright positions in the actual underlying.


## 0.13 Long Position 做多 做多持仓 做多头寸 多头
When a trader buys an option contract that he is not short, he is said to be opening a long position. 当交易者买入一个还未持有的期权合约时，就说他建立（开立、进入）一个做多部位（持仓、头寸）。When a trader sells an option contract that he is already long, he is said to be closing a long position.当交易者卖出一个已经持有的期权合约时，就说他平掉（关闭、退出）一个做多部位（持仓、头寸）。 

> 更好的描述
> 
>  <https://www.investopedia.com/terms/l/long.asp>
> 
> * A long — or a long position — refers to the **purchase of an asset with the expectation it will increase** in value — a *bullish attitude*.
> 
> * A long position in *options contracts (期权合同)* indicates the holder owns the underlying asset.
>
> * A long position is the opposite of a short position.
> 
> * In options, being long can refer either to *outright ownership of an asset (long a call option)* or *being the holder of an option on the asset (long a put option)*.
>
> * Being long on a stock or bond investment is **a measurement of time**.


### 0.13.1 期权市场中的Long Position
The term long position is often used In the context of **buying an options contract**. The trader can **hold** either a **long call（看涨期权、买方期权）** or a **long put option（看跌期权、卖方期权）**, depending on the outlook for the underlying asset of the option contract.  

For example, an investor who hopes to benefit from an upward price movement in an asset will **"go long" on a call option (做多看涨期权、多头看涨期权)**. The call gives the holder *the option to buy the underlying asset at a certain price （低于市场价买入资产的选择权）*.   

Conversely, an investor who expects an asset’s price to fall will be **long on a put option（做多看跌期权、多头看跌期权）** — and maintain *the right to sell the asset at a certain price （高于市场价卖出资产的选择权）*.

下面是一个**买入看涨期权 going long on a call option**的例子
> 
> For example, let's say Jim expects Microsoft Corporation (MSFT) to increase in price and purchases 100 shares of it for his portfolio. Jim is therefore said to "be long" 100 shares of MSFT.
>
> Now, let's consider a November 17 call option on Microsoft (MSFT) with a $75 strike price and $1.30 premium. If Jim is still bullish on the stock, he may decide to purchase or go long one MSFT call option—one option equates to 100 shares—instead of purchasing the shares outright as he did in the previous example.
>
>At expiry, if MSFT is trading above the strike price plus the premium paid ($75 + $1.30), Jim will exercise his right to buy on his long option to purchase 100 shares of MSFT at $75. The writer of the options contract—the short position—that Jim bought must sell him the 100 shares at the $75 price.

然后是**买入看跌期权going long on a put option**的例子
> Let's say another investor, Jane currently has a long position in MSFT for 100 shares in her portfolio but is now bearish on it. She takes a long position on one put option. The put option is trading for $2.15 and has a strike price of $75 set to expire November 17.
>
> At the time of expiry, if MSFT drops below the strike price minus the premium paid ($75 - $2.15), Jane will exercise the long put option to sell her 100 MSFT shares for the strike price of $75. In this case, the option writer must buy Jane's shares at the agreed-upon $75 price, even if the shares are trading at less on the open market.


### 0.13.2 期货市场中的long position
investors and businesses can also enter into a **long forward or futures contract 做多期货合同** to hedge against adverse price movements. A company can employ a long hedge to lock in a purchase price for a commodity that is needed in the future （通过做多期货以锁定所需的商品价格，对冲未来商品价格变贵所造成的损失）. **Futures differ from options in that the holder is obligated to buy or sell the underlying asset （期货合同到期后必须要执行）**. They do not get to choose but must complete these actions.

下面是关于做多期货的例子：

> Suppose a **jewelry manufacturer believes the price of gold is poised to turn upwards** in the short term. The firm can enter into a long futures contract with its gold supplier to purchase gold in three months from the supplier at $1,300. **In three months, whether the price is above or below $1,300, the business that has a long position on gold futures is obligated to purchase the gold from the supplier at the agreed contract price of $1,300.** The supplier, in turn, is obligated to deliver the physical commodity when the contract expires.


## 0.14 Short Position 做空 做空持仓 做空头寸 空头    
When a trader sells an option contract that he is not long, he is said to be opening a short position. 当交易者卖出一个还未持有的期权合约时，就说他建立（开立、进入）一个做空部位（持仓、头寸）。When a trader buys an option contract that he is already short, he is said to be closing a short position. 当交易者买入一个已经持有的期权合约时，就说他平掉（关闭、退出）一个做空部位（持仓、头寸）。

> *更好的阐述*
> * A short position refers to a trading technique in which **an investor sells a security with plans to buy it later.**
> * Shorting is a strategy used when an investor a*nticipates the price of a security will fall in the short term*.
>
> * In common practice, **short sellers borrow shares of stock** from an investment bank or other financial institution, **paying a fee to borrow the shares while the short position is in place**.
> 
> 也即：预示短期下降，先卖出X股，等到下跌后再以更便宜的价格买回X股，净赚差价


## 0.15 Net Position  净部位 净持仓 净头寸
Net position is the difference between total open long (receivable) and open short (payable) positions in a given asset (security, foreign exchange currency, commodity, etc.) held by an individual.净部位（持仓、头寸）是个人或法人持有的指定资产（证券、外汇货币、商品等）中的全部敞口做多与做空部位（持仓、头寸）之差。  

>个人观点
>
>部位（持仓、头寸、仓位）的三大属性一*是方向（position direction）*，是做多还是做空；二是*规模或大小（position size）*，头寸中的“寸”即是此意；三是建立时的*价格位置（price level）*，部位中的“位”（也即仓位）即是此意。翻译整理自 Position (finance)


## 0.15 股票
股票（Stock），是股份证书的简称，是股份公司为筹集资金而发行给股东作为持股凭证并借以取得股息和红利的一种有价证券。每股股票都代表股东对企业拥有一个基本单位的所有权。这种所有权是一种综合权利，如参加股东大会、投票表决、参与公司的重大决策、收取股息或分享红利等。

每个股东所拥有的公司所有权份额的大小，取决于其持有的股票数量占公司总股本的比重。股票是股份公司资本的构成部分，可以转让、买卖或作价抵押，是资本市场的主要长期信用工具，但不能要求公司返还其出资。股东与公司之间的关系不是债权债务关系。股东是公司的所有者，以其出资份额为限对公司负有限责任，承担风险，分享收益。

其特点有三：  
1. 股票是一种出资证明，当一个自然人或法人向股份有限公司参股投资时，便可获得股票作为出资的凭证；
2. 股票的持有者凭借股票来证明自己的股东身份，参加股份公司的股东大会，对股份公司的经营发表意见；
3. 股票持有者凭借股票参与股份发行企业的**利润分配，也就是通常所说的分红**，以此获得一定的经
济利益。


股票本身没有价值，但它可以当做商品出卖，并且有一定的价格。*股票价格又叫股票行市，它不等于股票票面的金额*。  

**股票的票面额代表投资入股的货币资本数额，它是固定不变的**；而股票价格则是变动的，它经常是大于或小于股票的票面金额。**股票的买卖实际上是买卖获得股息的权利，因此股票价格不是它所代表的实际资本价值的货币表现，而是一种资本化的收入。**股票价格一般是由股息和利息率两个因素决定的。例如，有一张票面额为100元的股票，每年能够取得10元股息，即 10%的股息，而当时的利息率只有5%，那么，这张股票的价格就是10元÷5%=200元。计算公式是：  

$$股票价格=股息/利息率$$

可见，股票价格与股息成正比例变化，而和利息率成反比例变化。如果某个股份公司的营业情况好，
股息增多或是预期的股息将要增加，这个股份公司的股票价格就会上涨；反之，则会下跌。


### 股票市场
股票市场是股票发行和流通的场所，也可以说是指对已发行的股票进行买卖和转让的场所。股票的交易都是通过股票市场来实现的。一般地，股票市场可以分为一、二级，一级市场也称之为股票发行市场，二级市场也称之为股票交易市场。  

**股票市场是上市公司筹集资金的主要途径之一**。随着商品经济的发展，公司的规模越来越大，需要大量的长期资本。而如果单靠公司自身的资本化积累，是很难满足生产发展的需求的，所以必须从外部筹集资金。公司筹集长期资本一般有三种方式：一是向银行借贷；二是发行公司债券；三是发行股票。前两种方式的利息较高，并且有时间限制，这不仅增加了公司的经营成本，而且使公司的资本难以稳定，因而有很大的局限性。而**·**。把这三种筹资方式综合比较起来，发行股票的方式无疑是最符合经济原则的，对公司来说是最有利的。所以发行股票来筹集资本就成为发展大企业经济的一种重要形式，而股票交易在整个证券交易中因此而占有相当重要的地位。


## 0.16 金融期货 （Financial futures） 
所谓金融期货，是指**以金融工具（即信用工具，如股票、债券等）作为标的物的期货合约。**金融期货交易是指交易者在特定的交易所通过公开竞价方式成交，承诺在未来特定日期或期间内，以事先约定的价格买入或卖出特定数量的某种金融商品的交易方式。金融期货交易具有期货交易的一般特征，但与商品期货相比，其合约标的物不是实物商品，而是金融商品，如外汇、债券、股票指数等。

金融期货基本上可分为三大类：外汇（汇率）期货、利率期货和股票指数期货。
1. 外汇期货是指协约双方同意在*未来某一时期，根据约定价格---汇率，买卖一定标准数量的某种外汇的**可转让**的标准化协议*。外汇期货包括以下币种：日元、英镑、德国马克、瑞士法郎、荷兰盾、法国法郎、加拿大元、美元等；
2. 利率期货是指协议双方同意在约定的将来某个日期按*约定条件买卖一定数量的某种长短期信用工具的可转让的标准化协议*。利率期货交易的对象有长期国库券、政府住宅抵押证券、中期国债、短期国债等；  
3. 股票指数期货是指协议双方同意在将来某一时期*按约定的价格买卖股票指数的**可转让**的标准化合
约*。最具代表性的股票指数有美国的道·琼斯股票指数和标准·普尔500种股票指数、英国的金融时报工业普通股票指效、香港的恒生指数、日本的日经指数等。


## 0.17 趋势线
是衡量价格波动方向的直线，由趋势线的方向可以明确地看出股价的趋势。  

在上升趋势中将两个低点连成一条直线得到**上升趋势线**；该线起到*支撑作用*，是支撑线的一种。  

在下降趋势中将两个高点连成一条直线得到**下降趋势线**，起到*压力作用*，是压力线的一种。


## 0.18 期权 （option）
期权是一种金融衍生品。性质如下：  

1. 期权是一种权利。期权合约至少涉及买家和出售人两方。**持有人享有权利但不承担相应的义务**。
2. 期权的标的物。**期权的标的物是指选择购买或出售的资产。它包括股票、政府债券、货币、股票指数、商品期货**等。期权是这些标的物“衍生”的，因此称衍生金融工具。值得注意的是，期权出售人不一定拥有标的资产。期权是可以“卖空”的。期权购买人也不一定真的想购买资产标的物。因此，期权到期时双方不一定进行标的物的实物交割，而只需按价差补足价款即可。
3. 到期日。双方约定的期权到期的那一天称为“到期日”，*如果该期权只能在到期日执行，则称为欧式期权；如果该期权可以在到期日及之前的任何时间执行，则称为美式期权*。
4. 期权的执行。依据期权合约购进或售出标的资产的行为称为“执行”。在期权合约中约定的、期权持有人据以购进或售出标的资产的固定价格，称为“执行价格”。


举例如下：
```
公司2011年1月份给了2000股期权，行权日期为2012年1月，行权价格为5000元。
什么意思呢？就是说，只要给了5000块钱，到了2012年1月分，我可以以50元/股的价格购买公司股票。  

如果到了那个时候，公司的股价是100元，那么我选择行使权力：50元/股的价格买入，那就等于我花了2000*50=100000美元的价格买到了市值 100*2000=200000美元的股票。  

但是，如果2012年1月的时候公司的股价为20元，那么，如果那是我再行使权力，那我就是傻子了。这个时候，我只能暂时放弃行权，行权的5000打水漂。
```
这里，卖期权方类似于保险公司，买期权方类似于投保人。  

通过上面的例子，可以得出以下结论：一是作为**期权的买方（无论是看涨期权还是看跌期权）只有权利而无义务。他的风险是有限的（亏损最大值为权利金），但在理论上获利是无限的**。二是作为期权的**卖方（无论是看涨期权还是看跌期权）只有义务而无权利，在理论上他的风险是无限的，但收益是有限的（收益最大值为权利金）**。三是期权的买方无需付出保证金，卖方则必须支付保证金以作为必须履行义务的财务担保。

## 0.19 CTA 商品交易顾问

CTA(Commodity Trading Advisor)即商品交易顾问，是由专业的管理人以追求绝对收益为目标，运用客户委托的资金投资于期货市场、期权市场，并收取相应投资顾问费用的一种基金形式。由于CTA主要交易对象是期货及期权合约，因此CTA基金现在通常也被称作管理期货(Managed Futures)基金。

## 0.20 Order Types
订单可分为三大类：市价单（Market order）、限价单（Limit order）、止损单（Stop order）

### 市价单
* A **market order** is an order to buy or sell a security immediately. This type of order guarantees that the order will be executed, but does not guarantee the execution price. A market order **generally will execute at or near the current bid (for a sell order) or ask (for a buy order) price**. 
 
However, it is important for investors to remember that **the last-traded price is not necessarily the price at which a market order will be executed**.  

### 限价单
* A **limit order** is an order to buy or sell a security at a specific price or better. **A buy limit order can only be executed at the limit price or lower, and a sell limit order can only be executed at the limit price or higher.** 
 
Example: An investor wants to purchase shares of ABC stock for no more than $10. The investor could submit a limit order for this amount and this order will only execute if the price of ABC stock is $10 or lower.  

### 止损单
* **Stop orders** come in a few different variations, but they are all effectively **conditional based on a price that is not yet available in the market when the order is originally placed**. Once the future price (known as the *stop price*) is available, a stop order will be triggered, but depending on its type, the broker will execute them differently.  

Stop orders includes *stop-market (or stop-loss) orders* and *stop-limit orders*. Stop-market orders can be further categorized as *buy stop orders* and *sell stop orders*.

Stop orders are used to **limit losses with a stop-loss** or **lock in profits using a bullish stop**.

  
  * A **normal stop order (a.k.a stop-market order or stop-loss order)** will turn into a traditional market order **once your stop price is met or exceeded**. A stop order can be set as an entry order as well. If you wanted to open a position once the price of a stock is rising, a stop market order could be set above the current market price, which **turns into a regular market order once your stop price has been met**
    * **Sell-Stop Orders**:  
        Sell-stop orders protect long positions by **triggering a market sell order if the price falls below a certain level**. The underlying assumption behind this strategy is that, if the price falls this far, it may continue to fall much further. The loss is capped by selling at this price.（可以防止做多后价格一直下降而大亏，及时止损）

        > For example, let's say a trader owns 1,000 shares of ABC stock. They purchased the stock at $30 per share (成本30每股), and it has risen to $45, on rumors of a potential buyout （收购）. The trader wants to lock in a gain of at least $10 per share, so they **place a sell-stop order** at $41. If the stock drops back below this price, then the order will become a market order and get filled at the current market price, which may be more (or more likely less) than the stop-loss price of $41 （sell-stop提前锁定41-30的利润，防止未来的下降导致利润减少）. 
        >
        > In this case, the trader might get $41 for 500 shares and $40.50 for the rest. But they will get to keep most of the gain.

    * **Buy-Stop Orders**  
    Buy-stop orders are conceptually the same as sell-stop orders. However, they are *used to protect short positions* (做空后如果急速反升那么就变成之前在低位卖出，很可能亏了). A **buy-stop order price will be above the current market price and will trigger if the price rises above that level**.



  
  * A **stop-limit order** consists of two prices: a *stop price* and a *limit price*. This order type **can be used to activate a limit order to buy or sell a security once a specific stop price has been met**. 

For example, imagine you purchase shares at $100 and expect the stock to rise. You could place a stop-limit order to **sell the shares** if your forecast was wrong. If you set the stop price at $90 (to be visible when price <= $90) and the limit price at $90.50 (to be filled in when price >= 90.5), *the order will be activated (become visible to the whole market) if the stock trades at $90 or worse*.   

However, a limit order will be filled only if the limit price you selected is available in the market. If the stock drops overnight to $89 per share, that is below your stop price so that the order will be activated, but it will not be filled immediately because there are no buyers at your limit price of $90.50 per share (no buyer asks for >= $90.5). The stop price and the limit price can be the same in this order scenario.

### 0.20.1 Stop-limit order风险
A stop-limit order has two primary risks: no fills or partial fills. It is possible for your stop price to be triggered and your limit price to remain unavailable. If you used a stop-limit order as a stop loss to exit a long position once the stock started to drop, it might not close your trade.

Even if the limit price is available after a stop price has been triggered, your entire order may not be executed if there wasn't enough liquidity at that price. For example, if you wanted to sell 500 shares at a limit price of $75, but only 300 were filled, then you may suffer further losses on the remaining 200 shares.

### 0.20.2 Stop-market order风险
A stop order avoids the risks of no fills or partial fills, but because it is a market order, you may have your order filled at a price much worse than what you were expecting. For example, imagine that you have set a stop order at $70 on a stock that you bought for $75 per share.

### 0.20.3 Limit order 与 Stop Order 区别
* **A limit order is visible to the market** and instructs your broker to fill your buy or sell order at a specific price or better.  
  
* A stop order **isn't visible to the market and will activate a market order** once a stop price has been met.  

* A stop order avoids the risks of no fills or partial fills, but **because it is a market order, you may have your order filled at a price much worse** than what you were expecting.

---

# 1. 双均线策略

Granville 八大法则其中有四条是用于判断买进时机，另外四条是用于判断卖出时机。买进和卖出法则一一对应，分布在高点的左右两侧（除买4和卖4以外）。法则内容如下所示：

* 买1：**均线整体上行，股价由下至上上穿均线**，此为*黄金交叉*，形成第一个买点。
* 买2：股价出现下跌迹象，**但尚未跌破均线**，此时均线变成支撑线，形成第二个买点。
* 买3：股价**仍处于均线上方，但呈现急剧下跌趋势。当跌破均线**时，出现第三个买点。
* 买4：（右侧）**股价和均线都处于下降通道，且股价处于均线下方**，严重远离均线，出现第四个买点。

* 卖1：**均线由上升状态变为缓慢下降的状态，股价也开始下降。当股价跌破均线**时，此为*死亡交叉*，形成第一个卖点。
* 卖2：**股价仍处于均线之下，但股价开始呈现上涨趋势**，当股价无限接近均线但尚未突破时，此时均线变成*阻力线*，形成第二个卖点。
* 卖3：股价终于突破均线，处于均线上方。但**持续时间不长，股价开始下跌，直至再一次跌破均线**，此为第三个卖点。
* 卖4：（左侧）股价和均线都在上涨，股价上涨的速度远快于均线上涨的速度。当**股价严重偏离均线**时，出现第四个卖点。

![双均线策略示意图](https://www.myquant.cn/uploads/202010/attach_16402fcc49ff5d9b.jpg)[^1]

[^1]:浅蓝色折线为短期均线；深蓝色平滑曲线为长期均线；橙色为“真实”股价曲线。



## 参考传送门
<https://www.myquant.cn/docs/python_strategyies/153>

***

# 2. Alpha对冲策略

## 2.1 策略原理相关背景：CAPM

CAPM即资本资产定价模型（Capital Asset Pricing Model）。CAPM模型于1964年被Willian Sharpe等人提出。Sharpe等人认为，假设市场是均衡的，资产的预期超额收益率就由市场收益超额收益（alpha）和风险暴露（beta）决定的：

$$
E[r_p] = r_f + \beta_p(E[r_m] - r_f) \tag{1}
$$

其中，$r_p$为自家产品组合回报率（超额收益率，即“超出由于市场这种系统性水平导致的收益”），$E[R_p]$是**预期超额收益率**（$r_p$的数学期望），$r_f$是无风险收益率，$\beta_p$为自己产品组合的beta（风险）值，$E[r_m]$为市场所有产品的平均回报率。  

根据CAPM模型可知，**投资组合的预期收益**由两部分组成，一部分为无风险收益率$r_f$，另一部分为风险收益率(beta项)。  

应当指出，上面的式子仅仅是阐述投资组合收益来源的理论分析，不是策略本身！  

下面是一个对CAPM的通俗解释：
>
>首先，为什么要加上一个无风险回报率？作为一个投资者来说，期望回报率一定要大于无风险回报率的，这个很容易理解。什么是无风险回报率？比如在中国来说，我们可以假定在银行存个5年定期是无风险的，当然，这个无风险回报率也是因人而异的，没有一个特定指数（比如你也可以选10年定期）。
>
>然后我们看 用市场所有股票的平均回报率减去无风险回报率，我们叫它市场期望回报率。你想一下为什么要有这个式子存在呢？因为作为一个投资者，我们一定会在投资之前考虑为什么放弃无风险投资而选择投资股票吧？再通俗一点，为什么我们不拿钱存定期，而把钱投进股市呢？是因为股市的回报高呀！那到底高多少呢？用市场平均回报率减一下定期利率啊。所以如果 为负值，那意味着什么？意味着把钱投入股市，还不如存定期。这个账很好算，所以一旦为负，后面的计算就没有意义了。
>
>好，那贝塔值的意义是什么呢？贝塔值是用来衡量个别投资工具与市场的波动情况。说人话呢就是，这只股票的波动性，用波动性看风险。如果贝塔值是1呢，就是说大盘涨多少，这只股票就涨多少，是完全一致的。如果贝塔值大于1呢，就是说这只股票的波动是大于市场平均值的。如果贝塔值小于1则意味着这只股票波动小于市场平均值。要知道，在金融学里如何衡量一只股票的风险就是利用波动性。波动性为什么被用来作为衡量风险的标志之一呢？假设啊，假设有一只股票，每天不是跌停，就是涨停，而大盘很正常。这样的股票波动性很大，风险是不是也很大？贝塔值呢，就是这样一个用来衡量某只特定股票风险的指数。
>
>作为一个投资者，首先，要保证我们投资的股票回报率一定一定是大于定期的。其次，股票市场整体也要比无风险利率高。最后怎样体现我选的这只股票的风险呢？用贝塔值。这样用贝塔值乘以市场的期望回报率，再加上无风险利率，就能得到我想要的这只股票的期望回报率啦
>
>如何通俗易懂地解释 CAPM 理论？ - Amelie的回答 - 知乎
https://www.zhihu.com/question/28282744/answer/56312706


## 2.2 基于CAPM引入alpha项
CAPM模型表示的是在均衡状态下市场的情况，但市场并不总是处于均衡状态，个股总会获得超出市场基准水平的收益，即在CAPM模型的右端总是存在一个alpha项。 

为了解决这个问题，1968年，美国经济学家迈克·詹森（Michael Jensen）提出了詹森指数来描述这个alpha，因此又称**alpha指数**。将(1)式中的$E[r_p]$改写为$r_p$，以及加入了alpha收益项$\alpha_p$后再整理即可获得alpha指数的定义：
$$
\alpha_p = r_p - r_f - \beta_p(E[r_m] - r_f) \tag{2}
$$

将上面的式子(2)的无风险收益$r_f$去掉，获得**绝对的（非预期）产品超额收益**：
$$
r_p = \alpha_p + \beta_p(E[r_m] - r_f) \tag{3}
$$
其中:
$$
\beta_p = \frac{\text{cov}(r_p,r_m)}{\sigma_p\sigma_m}
$$
由此可见，可将投资组合的收益拆分为alpha收益和beta收益。

## 2.3 ALPHA对冲策略
$\beta$是由市场决定的，属于系统性风险，与投资者管理能力无关，只与投资组合与市场的关系有关。当市场整体下跌时，$\beta$对应的收益也会随着下跌（假设$\beta$为正）。alpha收益与市场无关，是**投资者自身能力的体现**。投资者通过自身的经验进行选股择时，得到超过市场的收益。  

当投资市场处于熊市或者非稳健市场时（beta值很大也即系统性风险很大，因此beta收益期望$\beta_p(E[r_m] - r_f)$为很大的负值），投资者将希望规避掉市场风险（将beta负收益尽可能缩小）、以及提高alpha收益以满足所期望的回报率。  

因此，我们希望：
* 对冲$\beta$收益
* 做大$\alpha$收益

也即，alpha对冲策略将市场性风险对冲掉，只剩下alpha收益，因而**整体收益（几乎）完全取决于投资者自身的能力水平，与市场无关**。应当指出，alpha对冲策略是一种高风险（对冲掉市场影响后完全靠自家组合技术赚取超额回报）、高回报（跑赢市场）策略。


计算阿尔法需要用到CAPM模型，它是由William Sharpe在其著作《投资组合理论与资本市场》中提出，它指出了投资者在市场中交易面临系统性风险和非系统性风险。传统阿尔法策略是在基金经理建立了beta部位的头寸[^2] 后，通过衍生品对冲beta部位的风险，从而获得正的阿尔法收益。

阿尔法策略的思想是通过衍生品来对冲投资组合的系统风险beta，锁定超额收益alpha。因此首先需要
寻找**稳定的alpha，构建alpha组合，进而计算组合的beta，对冲风险**。Alpha策略成功的关键就是寻找到一个*超越基准（具有股指期货等做空工具的基准）的策略*。比如，可以构造指数增强组合+沪深300指数期货空头策略。这种策略隐含的投资逻辑是择时比较困难，不想承受市场风险。  

从国内外的经验来看，在强式有效的市场，一般不存在或者很难寻找稳定的alpha。因此，阿尔法策略
一般运用在市场效率相对较弱的市场上，如新兴股票市 场、创业板市场等。我国的股票市场正是一个新兴的市场，效率相对较低，从过往的经验以及研究来看，的确存在着超额收益alpha。而股指期货， 无疑给我国市场阿尔法策略的运用提供了良好的基础。  

Alpha 策略成败的两个关键要素是：
1. 现货组合的超额收益空间有多大;
2. 交易成本的高低。
3. 
两者相抵的结果，才是 Alpha 策略可获得的利润空间。在股市 Alpha 策略中，最考验策略制定者
水平的因素在于选股方法和能力。

[^2]: 浅头寸亦称“头衬”，意即款项。旧时中国商业、银钱业惯用语。银行、钱庄和企业为了保证收支平衡，每日要对可以收进或需要支出的款项进行预测估算，看能否平衡。这种预算活动称为“轧头寸”。如预计当日全部收入大于支出，即为“多头寸”，亦称“多单”；如需支付的款项大于收入款项，即为“缺头寸”，亦称“缺单”。头寸经轧算后有“多头寸”，可把收大于支的余额出借；如“缺寸头”，则应设法拆进或借入款项 (俗称“调头寸”)，以资轧平。

## 参考传送门
* <https://www.myquant.cn/docs/python_strategyies/101>
* <https://wiki.mbalib.com/wiki/%E9%98%BF%E5%B0%94%E6%B3%95%E7%AD%96%E7%95%A5>
* <https://blog.csdn.net/kwame211/article/details/85045198>


---

# 3. 网格交易法

## 3.1 内容
网格交易法是一种利用行情震荡进行获利的策略。在标的价格不断震荡的过程中，对标的价格绘制网格，在市场价格触碰到某个网格线时进行加减仓操作尽可能获利。

网格交易法属于左侧交易的一种。与右侧交易不同，网格交易法并非跟随行情，追涨杀跌，而是逆势而为，在**价格下跌**了一个梯度（decremeny by some amount）买入，**价格上涨**了一个梯度（increment by some amount）时卖出。如下两图所示：

### 震荡上涨
![震荡上涨时](https://www.myquant.cn/uploads/202010/attach_1642640f8e2ec613.png)  
假设格子之间的差为1元钱，每变化一个格子相应的买入或卖出1手，则通过网格交易当前账户的净收益为5元，持空仓3手，持仓均价为13元
#### 震荡下降
![震荡下降时](https://www.myquant.cn/uploads/202010/attach_164264143d608515.png)  
同理可知，净收益为10元，持5手多仓，平均成本为8元。可以看到，无论行情上涨还是下跌，已平仓的部分均为正收益，未平仓的部分需要等下一个信号出现再触发交易。  

即使网格交易能够获得较为稳定的收益，但也存在一定的风险。如果行情呈现大涨或大跌趋势，会导致不断开仓（买得太多），增加风险敞口。这也是为什么网格交易更适用震荡行情，不合适趋势性行情。

## 3.2 设计网格
投资者可以随意设置网格的宽度和数量。既可以设置为等宽度，也可以设置为不等宽度的。设置等宽度网格可能会导致买点卖点过早，收益率较低。设置不等宽度网格能够避免这个问题，但如果行情出现不利变动，可能会错失买卖机会。

网格交易主要包括以下几个核心要点：

**挑选的标的最好是价格变化较大，交易较为活跃**  
网格交易是基于行情震荡进行获利的策略，如果标的不活跃，价格波动不大，很难触发交易。

**选出网格的压力位和阻力位**  
确定适当的压力位和阻力位，使价格大部分时间能够在压力位和阻力位之间波动。如果压力位和阻力位设置范围过大，会导致难以触发交易；如果压力位和阻力位设置范围过小，则会频繁触发交易。

**设置网格的宽度和数量**  
设定多少个网格以及网格的宽度可根据投资者自身喜好自行确定。

## 3.3 算法描述


1. 确定价格中枢、压力位和阻力位
2. 确定网格的数量和间隔
3. 当价格触碰到网格线时，若高于买入价，则每上升一格卖出m手；若低于买入价，则每下跌一格买入m手。


## 参考传送门
<https://www.myquant.cn/docs/python_strategyies/104>

---

# 4. 指数增强策略

## 4.1 背景
在进行股票投资时，有一种分类方式是将投资分为*主动型投资*和*被动型投资*。被动型投资是指完全复制指数，跟随指数的投资方式。与被动型投资相反，主动型投资是根据投资者的知识结合经验进行主动选股，不是被动跟随指数。**主动型投资者期望获得超越市场的收益，被动型投资者满足于市场平均收益率水平**。

## 4.2 原理
指数增强是指在跟踪指数的基础上，采用一些判断基准，**将不看好的股票权重调低或平仓，将看好的股票加大仓位，以提高收益率的方法。**既然如此，我已经判断出来哪只是“好股票”，哪只是“一般”的股票，为什么不直接买入？而是要买入指数呢？  

指数增强不同于其他主动投资方式，除了注重获取超越市场的收益，还要兼顾降低组合风险，**注重收益的稳定性**。如果**判断失误，只买入选中股票而非指数会导致投资者承受巨大亏损** （类似于玩alpha对冲策略选择好alpha失败）。  

怎样选择股票呢？和alpha对冲策略类似，指数增强仅仅是一个思路，怎样选择“好股”还需投资者结合自身经验判断。本策略利用“动量”这一概念，**认为过去5天连续上涨的股票具备继续上涨的潜力，属于强势股；过去5天连续下跌的股票未来会继续下跌，属于弱势股**。

## 4.3 算法
```
第一步：选择跟踪指数，以权重大于0.35%的成分股为股票池。
第二步：根据个股价格动量来判断是否属于优质股，即连续上涨5天则为优势股；间隔连续下跌5天则为劣质股。
第三步：将优质股权重调高0.2，劣质股权重调低0.2。
第四步：按权重/总权重为比例，将各个股票的仓调节至该比例（结果是绩优股买入更多了绩差股的一部分   卖出）
```

## 参考传送门
<https://www.myquant.cn/docs/python_strategyies/104>


# 5. 跨品种套利（期货）
## 5.1 原理
当两个合约有很强的相关性时，可能存在相似的变动关系，两种*合约之间的价差*会维持在一定的水平上。当市场出现变化时，两种*合约之间的价差*会偏离均衡水平。此时，可以买入其中一份合约同时卖出其中一份合约，当价差恢复到正常水平时平仓，获取收益。


## 5.2 举例
以大商所玉米和淀粉为例，合约分别为DCE.c1801和DCE.cs1801。
![玉米与淀粉价位图](https://www.myquant.cn/uploads/202011/attach_16447dccc6e340a8.png)


如图所示，二者价差整体处于250-350之间。当价差偏离此区间时（例如红圈所示），价差达到接近500,此时我们可以卖出相对价格偏高cs1801（橙色线），买入相对低价的cs1801（蓝色线），以此套利（500-正常的价差）

## 5.3 应用条件
1. 套利的两种资产必须有一定的相关性。
2. 两种合约标的不同，到期时间相同。
3. 两种资产之间的价差呈现一定规律。

## 5.4 确定合约相关性
最常用的方法是利用EG两步法对两个序列做协整检验，**判断两个序列是否平稳**。只有**单整阶数相同，二者才有可能存在一定的关系。**


## 5.5 策略设计
传统利用价差进行跨品种套利的方法是计算出均值和方差，**设定开仓、平仓和止损阈值。**当新的价格达到阈值时，进行相应的开仓和平仓操作。

![跨品种套利阈值设计](https://www.myquant.cn/uploads/202011/attach_16447de3754242dd.png)


## 5.6 算法
```
第一步：选择相关性较高的两个合约，本例选择大商所的焦炭和焦煤。
第二步：以过去30个的1d频率bar的均值正负0.75个标准差作为开仓阈值，以正负2个标准差作为止损阈值。
第三步：最新价差上穿上界时做空价差，回归到均值附近平仓；下穿下界时做多价差，回归到均值附近平仓。设定止损点，触发止损点则全部平仓。
```

## 参考传送门
<https://www.myquant.cn/docs/python_strategyies/106>

---

# 6. 做市商策略

做市商（Market maker）制度是一种报价驱动制度。做市商根据自己的判断，**不断地报出买入报价和卖出报价，以自有资金与投资者进行交易。做市商获取的收益就是买入价和卖出价的价差。**

假设做市商以6344卖出一手合约，同时以6333买入一手合约。如果都成交，做市商可净获利11个点。但如果当时合约价格持续走高或走低，做市商没有对手方能够成交，这时就不得不提高自己的买价或降低自己的卖价进行交易，做市商就会亏损。因此，做市商并不是稳赚不赔的。

>做市商”的英文原文是 Market Maker，换句话理解：在没有市场的地方，做市商 Make（做，创造）了一个市场出来。
>
>这里所谓的“没有市场的地方”，并非指市场不存在，而是是指交易市场的活跃度不够。市场本身是为了解决供需双方进行买卖交换的需求的，不过要想达成交易，必须要买方和卖方在同一时刻、对同一价格都有交易意愿。这件事说起来简单，不过不活跃的市场想要在同一时刻汇集起合适的买家和卖家并非易事。
举一个简答的例子。如果说市场供需双方众多，买家 A 如果想买一个桌子，那么他随时去找到一众木匠中找到报价合适的木匠 B， 谈一个合理的价格就可以成交了，这时也不需要什么做市商。但如果交易者稀少，只有一个买家和一个木匠，那么当买家 A 一大早跑到市场上想买个东西，待了半个小时依然发现市场空无一人，买不到货。木匠 B 下午到了市场，也发现自己的桌子无人问津，只得兴尽而归。这就导致了他们虽然都有买卖的需求，但是无法成交的状况。
>
>更严重的是，如果买家 A 每天只有早上有空能到市场， 木匠 B 每天也只能下午到市场，那他们之间就永远无法实现交易。于是，机智的做市商 C 出现了。既然他知道 A 想买 B 想卖，又苦于碰不到面，那他就可以下午的时候先把木匠 B 的桌子买走，第二天早上再在市场上等着 A 出现，转手把桌子卖给 B。不过代价嘛... C 的进价很可能是 100，卖价是 110。这中间的 10 块钱的差价就是 C 的利润。
>
>现代意义上说的做市商，一般就是特指金融市场里面专门从事这类“二道贩子”业务的金融机构。无论你何时想买或者何时想买或者何时想卖，总有一个 C 这种的“倒爷”等着你，给你提供报价，你也随时可以找他成交。他们存在的意义，就是为了解决市场流动性不足带来的交易时间难以匹配的问题。我国现在典型的存在官方指定做市商的市场就是 50 ETF 期权市场。

## 参考链接
<https://zhuanlan.zhihu.com/p/364969897>
<https://www.myquant.cn/docs/python_strategyies/109>

---

# 7. 事件驱动策略

本策略不是一个具体的策略，是一类策略的总称。事件驱动策略是在**提前挖掘和深入分析可能造成股价异常波动的事件基础上，通过充分把握交易时机获取超额投资回报的交易策略**。

可以用于事件驱动策略的事件有很多，如CEO或CFO的变更、派息、拆股、回购、定增、指数成分股调整、大股东增持等等，此类事件的特点是具有较为明确的时间和内容，能够对部分投资者的行为产生一定的影响，从而决定股价短期波动的因素。

例如：高管增持事件，我们可以采用的一种策略是：在上市公司公布高管增持公告后，立刻买入并持有一个月，回测该策略是否存在超额收益。

我们也可以对高管增持事件进行一定的优化，如可以增持公告前跌幅最大的一组股票，公告后30日的累计超额收益约为5%，公告后60日的累计超额收益接近10%，其超额收益要比简单的高管增持事件明显。 

对于公募基金业绩披露事件，如果某只股票被多数公募基金购买，则说明多数公募基金看好该只股票。根据业绩披露事件，可以构建高共识大小盘组合，即流通市值排在前50%的重仓股池子中，选择持有主动基金数目最多的前50只标的作为高共识大盘组合。而流通市值排在后50%的重仓股池子中，选择持有主动基金数目最多的前50 只标的构建高共识小盘组合。


此外，我们也可以**基于分析师评级作为事件驱动策略**，如选取中证800股票池中，持续6个月有分析师发布含有目标价报告的股票。其思路是分析师持续发布含有目标价报告，说明该个股长期被分析师看好，基本面较优良。同时该策略对分析师报告的数量没有特别的约束，兼顾了大小市值的个股，对市场大小盘风格切换有较强的适应性。从策略回测收益来看，从2012 年至2018年，该策略年化收益超过20%，相对中证800 指数年化超额收益为10.45%。从换手率指标来看，该策略的年化换手率在4.5%上下波动，说明该组合的成分股平均持股时间较长，是一个精选个股、中长期持有的投资策略。

非常多的事件为传统因子分析提供了低相关度投资组合的机会，但是研究成本过高（特殊数据库、相关研究框架）阻碍了此前此类事件的研究。我们按照这种事件驱动思路实现了基于网络爬虫的券商评级事件驱动策略。通过爬虫技术，跟踪机构研报对个股的评级变化，当个股评级上调为“强烈推荐”时作为买入信号，等权持有股票一个月。该策略的回测效果如下：

该策略还可以进一步优化，如对分析师评级进一步分析，筛选出发布研究报告后个股表现最好和最差的分析师，给表现好的分析师给予更多的配置权重，给表现差的分析师给予更少的配置权重甚至零权重，从而获取更好的策略收益。

---

# 8. 宏观因素策略