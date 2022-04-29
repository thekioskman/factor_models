# Factor Models
## What are Factors

Factors is a very loosely defined word, and it is one of the big ambiguities that can make factor models hard to understand at first. We will break down factors as follows, in a broad sense we can break down factors into:

1. Stock Specific factors
2. Non-Stock Specific factors

We would say EBITA Margin, Net Assets, Total Income are stock specific factors, where as interest rates, currency exchange rates and Index ETF returns are non stock specific factors. When constructing a factor model we would choose to either look at stock spefic factors or, non stock specific factors. 

I would not advise trying to combine them however.

### Variable Data and Constant Data

Data found on income statement as balance sheets should be considered constant data. They usually only come out once a quarter. But data such as interest rates, or S&P returns are continuous streams of data, we can get 1 year returns all the way down to 1 day returns.

## Purpose of Factor Models

Factors models are a pretty flexible tool as they can be applied for a variety of reasons. We can imagine using them for analysis or for prediction. For example, if we wanted to figure out which factors are positively correlated/ negatively correlated with a companies returns a factor model could help us out.

$$
Return = \beta_1 *Fac_1 + \beta_2 *Fac_2 + \beta_3*Fac_3
$$

The signs of the $\beta$ values would be indicative if the returns are positively or negatively correlated with a given factor.

Another use of factor models is predicting return, if we build upon the previous example, stocks with high values for factors that are positively correlated with return will likely have a higher return than those with lower values (high negatively correlated value). 

We can think of this as a more quantitative approach to fundamental factors. Such as that fact that high debt is bad, or low income margins are bad.

## Its just Linear Regression

Factors models by design assume a linear relationship between all the factors and the return on the stock. This is because linear models tend to be more robust - they give a general outlook on the stock which tends to be better than a super detailed prediction.

With this in mind, we can simply run a multiple regression to get the $\beta$ values in our factor model. Of course we would need training data, each data point would just be an $n$ dimension tensor containing the return and $n-1$ factors we are considering.

A factor model is a super rough estimate on how different things affect the return of a stock, just like how a line of best fit sees to capture the general direction of points rather than be vary close to their true value.

## Correlation and Causation

One important thing to keep in mind is the distinction between correlation and causation. This was a source of confusion for me when I was first learning about factor models. The idea is that in the stock market, correlation ~ causation. 

Let me elaborate. We usually view our investments in the stock market as a risk vs reward trade off. For example is you invest in the S&P you are subject to market risk, if the market does poorly then your portfolio does poorly.  

In this example is it easy to see that the decline of your portfolio was caused by the decline of the market. Naturally, it would also be the case that your portfolio and the market would have strong correlation.  Now just imagine a case where this is just a strong correlation and no evident causation. When it rains, your portfolio tanks. Knowing whether rain is the cause does not matter... your portfolio is still tanking! 

To investors who want to diversify their portfolio, correlation is the only thing that matters. If all your stocks tank at the same time (regardless of what caused it) you do not have good diversity. I will define this more formally in the following section.

## Factor Modeling is Portfolio Diversity

Factors models have analytical use of measuring risk exposure.  The whole idea here is just to use factor modeling as a regression to find correlation between your portfolio an other *time series*. Most of the time, these other time series are what we are going to call Risk Factors. Usually things such as large market indices, currency rates, interest rates, ect. 

The amount of exposure would of course be measured with the $\beta$  values and understanding these exposures would allows us to hedge risks. Naturally, we want all the $\beta$ values to be close to zero, meaning we are not affected by any outside factors, or in other words, that our strategy returns a profit regardless of the market.

Recall the factor model equation as a multiple linear regression where, this means that there is indeed a constant term attached to it. 

 

$$
Return = \alpha + \beta_1 *Fac_1 + \beta_2 *Fac_2 + \beta_3*Fac_3 ... +\beta_n * Fac_n
$$

We want:

$$
Return = \alpha
$$

In other words we want to maximize this constant “risk free” profit rate. Also known as Alpha.

## What is Alpha

When you here people talk about alpha, or searching for alpha this is what they are referring to. Strategies that have no inherit risk exposure, meaning there returns are uncorrelated with any existing markets. People make a big deal about alphas, but it is worth noting that they are hard to find and diminish quickly when found/popularized.

## Analysis or Prediction

So far, we have only discussed these factor models in the context of analysis, but of course, being a linear regression model, they also have the power to predict.  We just have to change on simple input in the training processes to get:

$$
Rerturn_{t+1} = \alpha + \beta_1 *Fac_1 + \beta_2 *Fac_2 + \beta_3*Fac_3 ... +\beta_n * Fac_n
$$

We want to find the relationship between our factors, and some future return rather than some past/present return. We can easily make this change in the code, buy changing how we calculate the returns. Currently, we are looking at returns 3 months before the quarterly reports come out.

```
times = balance_sheet.columns.values[1::]
times = list(map(datetime.fromisoformat, times))
stock_price_history = pd.read_csv(path + "/stock_prices.csv")
stock_price_history["Date"] = pd.to_datetime(stock_price_history["Date"])
quarterly_returns = []


for i in range(len(times)):
    quarterly_data = pd.DataFrame()
    quarterly_data = stock_price_history[(stock_price_history["Date"] < times[i] ) & (stock_price_history["Date"] > times[i] - dateutil.relativedelta.relativedelta(months=3))].reset_index(drop=True)
    #calculate the net return in each quater
    if not(quarterly_data.empty) :
        total_ret = (quarterly_data.loc[len(quarterly_data)-1]["Close"] - quarterly_data.loc[0]["Open"])/quarterly_data.loc[0]["Open"]
        quarterly_returns.append(total_ret)
    else:
        quarterly_returns.append(None)
```

But we can adjust the holding period to be, for example, 2 weeks after the quarterly reports come out. This would then enable is to predict positive or negative shifts if the price over the next two weeks after quarterly reports based on the fundemental factors we are considering.

## Analysis of Outcome

Our model was set up as a regression between the 3-month return leading up to the quarterly balance sheet, cash flow statement, and earnings reports. It was an analytical experiment to check the relationship of certain fundamental factors to the returns of a stock during the same period. 

The first experiment I did was without normalizing the input data from the top 700 TECH companies ranked by market cap. This means that the regression was working with large numbers (about 10^11). So naturally, the coefficients of our regression were super small numbers, to make it more readable, I divided all the values by the absolute value of Change To Liabilities. (That is why the constant is a huge number)

```
Output:
const                                        9.140611e+11
Change To Liabilities                       -1.000000e+00
Total Cash From Operating Activities         1.351298e+00
Net Borrowings                              -1.991888e+00
Total Cashflows From Investing Activities   -4.411001e+00
Investments                                  2.564988e+00
Net Income                                  -3.986257e+00
Total Liab                                   3.498029e-01
Total Assets                                -2.967563e-01
Retained Earnings                            2.671895e-01
Cash                                         3.151412e+00
Net Receivables                              1.945539e+00
Long Term Debt                              -1.170602e+00
Short Long Term Debt                         2.874320e-01
Total Current Assets                        -1.049530e+00
```

We can see that most of our results are fairly intuitive, things such as cash, retained earnings and net receivables were positively correlated with the returns as you would expect. While things like Net Borrowing, Long Term Debt, and Total Liabilities were negatively correlated. 

Some results surpised me though. The first is Total Assets having a negative correlation. The only rationalization I can have for this is that because the companies have such large market cap, changes in total assets are fairly irrelevant, of course, we can also see the argument that having too much of your money in assets is not a good thing, specifically for big software companies. 

The next interesting result is that total liability is positively correlated with returns. For the explanation, I look to "Short Term Debt" and "Cashflows From Investing Activities" which are also both positively correlated. We can ration by short term debt would be a good thing, as it is usually indicative of a company needing some extra funding for a big project. We can see that the debt and subsequent liability could have been used to positively invest in the future of the company. Large-cap companies are usually more secure in taking out loans, so seeing increased liability could be a good sign that they found an investment opportunity, though the same cannot be said for smaller companies (which could default).  

## Review of Outcomes and Conclusion
After a further review of the results and the data, I discovered the reason why some parts of the results were not adding up. I had forgotten that the list of the top 700 tech companies contained many international corporations with stock prices quoted in their native currency. Although this does not change the aspect of return calculation, as we just took the stock price on the day after the reports as the final amount and the stock price 3 months ago as the principle.

<img src="https://render.githubusercontent.com/render/math?math=Return = \frac{Price_i}{Price_f}">

But, having international companies involved does add a component of currency exchange rate risk that we have not accounted for in our model. Due to the small sample size, I believe that these accounted for some odd results. After removing the international companies, I obtained the following revised results.
```
const                                        5.825244e+10
Change To Liabilities                        1.000000e+00
Total Cash From Operating Activities         3.473057e-01
Net Borrowings                              -1.577814e-02
Total Cashflows From Investing Activities   -1.270778e+00
Investments                                  1.479920e-02
Net Income                                   8.131597e-01
Total Liab                                  -7.365760e-02
Total Assets                                 4.976205e-02
Retained Earnings                           -2.056288e-01
Cash                                        -7.511013e-02
Net Receivables                              7.291306e-02
Long Term Debt                              -3.087043e-01
Short Long Term Debt                         1.137247e+00
Total Current Assets                         6.000365e-02
```

The results may not entirely be what we expected, but again it is important to note that I used large-cap stocks in my example, and there is research to support that factor models may not work very well on large-cap stocks. http://bearcave.com/finance/thesis_project/factor_analysis.pdf 


## Building a Predictive model
The second experiment I ran, was to see if the factor model had any predictive power. Instead of regression against the returns from 3 months prior, I performed a regression on the returns on a holding period of 2 weeks after the reports came out. The goal was to see which factors contributed most greatly to post-earnings drift.

```
times = balance_sheet.columns.values[1::]
times = list(map(datetime.fromisoformat, times))
stock_price_history = pd.read_csv(path + "/stock_prices.csv")
stock_price_history["Date"] = pd.to_datetime(stock_price_history["Date"])
quarterly_returns = []


for i in range(len(times)):
    quarterly_data = pd.DataFrame()
    #quarterly_data = stock_price_history[(stock_price_history["Date"] < times[i] ) & (stock_price_history["Date"] > times[i] - dateutil.relativedelta.relativedelta(months=3))].reset_index(drop=True)
    
    quarterly_data = stock_price_history[(stock_price_history["Date"] > times[i] ) & (stock_price_history["Date"] < times[i] + dateutil.relativedelta.relativedelta(weeks=2))].reset_index(drop=True)

    #calculate the net return in each quater
    if not(quarterly_data.empty) :
        total_ret = (quarterly_data.loc[len(quarterly_data)-1]["Close"] - quarterly_data.loc[0]["Open"])/quarterly_data.loc[0]["Open"]
        quarterly_returns.append(total_ret)
    else:
        quarterly_returns.append(None)
```



```
The results are as follows:
const                        8.163774e+09
Change To Liabilities       -1.000000e+00
Net Borrowings              -2.681154e-01
Investments                 -1.197345e-01
Net Income                   1.812647e-01
Total Liab                  -3.747576e-02
Total Assets                 6.354751e-03
Retained Earnings           -1.192692e-02
Cash                         2.896582e-02
Net Receivables              1.567684e-02
Total Current Liabilities    1.059928e-01
Total Current Assets        -4.476471e-02
```

Changing the time window of the holding period provides us with results that are much more expected than what we had previously. This makes sense as the market usually only reacts to a company's performance after the reports come out. The price of the company most likely will not reflect its financials (especially for large CAP companies) before those financials are released, even if the company is doing well under the hood.


## Non-Traditional Factors
Another important use of factor models is to take non-traditional factors into account. In fact, this is the direction where most quantitative hedge funds are trending... obscure data sets.

### Twitter Sentiment


### Momentum 


### Indicators?