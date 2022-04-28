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

We want to find the relationship between our factors, and some future return rather than some past/present return.