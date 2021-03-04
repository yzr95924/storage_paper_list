---
typora-copy-images-to: ../paper_figure
---
Capacity Forecasting in a Backup Storage Environment
------------------------------------------
|           Venue            |       Category       |
| :------------------------: | :------------------: |
| LISA'11 | Workload Analysis |
[TOC]

## 1. Summary
### Motivation of this paper
Many system administrators already have historical data for their systems and thus can predict full capacity events in advance.

It needs a proactive tool 
> 1. predicts the date of full capacity and provides advance notification.
> 2. there seems to be little previous work discussing applications of predictive modeling to data storage environments.

This paper presents the predictive model employed internally at EMC to forecast system capacity.
> generate alert notification months before systems reach full capacity.


### Data Domain 
- Data collection
1. employ inline deduplication technology on disk.
> Customers can configure their Data Domain systems to send an email everyday with detailed **diagnostic information**.

2. Most customers choose to send autosupports to EMC 
> the historical data enables more effective customer support.

Two variables of capacity forecasting:
> 1. Total physical capacity of the system (changes over time)
> 2. Total physical space used by the system

- Normal predictive model
1. the most common methods employed in predictive modeling is **linear regression**
> 1. This is challenging because behavior changes
> 2. blind application of regression to the entire data set often leads to poor predictions.
> ![1562591190121](../paper_figure/1562591190121.png)

2. select a subset of recent data
choose a subset of recent data such as the prior 30 days
> 1. eliminates the influence of the older data and improves the accuracy of the model's predictions.


- Piecewise linear regression
**Main idea**: analyze the quality of many linear regressions and then selects the one having the best fit.

1. How to reduce the error rate of the original linear regression model
> 1. applying the regression to a data subset that best represents the most recent behavior.

2. How to find the best subset of data?
> 1. the boundary must be determined where the recent behavior begins to deviate.
> 2. "goodness-of-fit" of a linear regression: $R^2 = 1$ indicates perfectly linear data
$$
R^2 = \frac{SSM}{SST} = \frac{\sum_i [f(x_i) - \bar{y}]}{\sum_i [y_i - \bar{y}]}
$$
> 3. select the subset with maximum $R^2$, from $\{(x_{-n}, y_{-n}),......,(x_0, y_0)\}$.
> 4. the calculated boundary occurs near the discontinuity of the truc function.

![1562596227030](../paper_figure/1562596227030.png)


- Model validation
Validation rules are applied to the results of the linear model to determine if capacity forecasts should be pulished.
> 1. Goodness-of-fit
> 2. positive slope

### Implementation and Evaluation
- Evaluation
1. Analysis of the quality of forecasts
> false positive: hardware changes, software changes
> from a statistical perspective, it is unknown whether the recent data points are signal or noise.

2. Capacity forecasting example
In Data Domain storage systems

## 2. Strength (Contributions of the paper)
1. This paper shows that there is a trade-off in predicative model 
eliminating reasonable models vs. generating false positives
> By requiring more data for models, it can gain higher confidence in their predictions, but reduce the advanced notification for true positives.

## 3. Weakness (Limitations of the paper)
1. If historical data does not demonstrate linear growth, then obviously linear regression would be a poor.


## 4. Future Works
1. In this paper, it mentions there exists many other models which can be applied to **time series data**.
> 1. weighted linear regression
> 2. logarithmic regression
> 3. auto-regressive (AR) model

There is an open question whether the remaining systems can be modeled by other methods.

2. How to improve this model to be compatible with some other systems? or find other applications of this predicative model?
predicate bandwidth throughput, load-balancing or I/O capacity.

3. The paper shows that the majority systems exhibit very linear behavior since the linear model had a very good fit the datasets.


