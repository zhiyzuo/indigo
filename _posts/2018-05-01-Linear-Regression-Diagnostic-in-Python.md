---
title: "Linear Regression Diagnostic in Python with StatsModels"
layout: post
date: 2018-05-02 00:01
headerImage: false
tag:
- python
- statistics
- visualization
category: blog
hidden: false
author: zhiyzuo
description: using Python to conduct linear regression diagnostic with statsmodels
---

<div class="breaker"></div>

```python
import numpy as np
import statsmodels
import seaborn as sns
from matplotlib import pyplot as plt
%matplotlib inline
```

<div class="breaker"></div>

While linear regression is a pretty simple task, there are several assumptions for the model that we may want to validate. I follow the regression diagnostic [here](http://people.duke.edu/~rnau/testing.htm), trying to justify four principal assumptions, namely ___LINE___ _in Python_:
- Lineearity
- Independence (This is probably more serious for time series. I'll pass it for now)
- Normality
- Equal variance (or homoscedasticity)

I learnt this abbreviation of linear regression assumptions when I was taking a course on correlation and regression taught by Walter Vispoel at UIowa. Really helped me to remember these four little things!

In fact, `statsmodels` itself contains useful modules for [regression diagnostics](http://www.statsmodels.org/dev/examples/notebooks/generated/regression_diagnostics.html). In addition to those, I want to go with somewhat manual yet very simple ways for more flexible visualizations.

---

### Linear regression

Let's go with the depression data. More toy datasets can be found [here](http://vincentarelbundock.github.io/Rdatasets/datasets.html). For simplicity, I randomly picked 3 columns.


```python
> df = statsmodels.datasets.get_rdataset("Ginzberg", "car").data
> df = df[['adjdep', 'adjfatal', 'adjsimp']]
> df.head(2)
```



<div>
<table class="table">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>adjdep</th>
      <th>adjfatal</th>
      <th>adjsimp</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>0.41865</td>
      <td>0.10673</td>
      <td>0.75934</td>
    </tr>
    <tr>
      <th>1</th>
      <td>0.51688</td>
      <td>0.99915</td>
      <td>0.72717</td>
    </tr>
  </tbody>
</table>
</div>



Linear regression is simple, with `statsmodels`. We are able to use R style regression formula.


```python
> import statsmodels.formula.api as smf
> reg = smf.ols('adjdep ~ adjfatal + adjsimp', data=df).fit()
> reg.summary()
```

<table class="table">
<caption>OLS Regression Results</caption>
<tr>
  <th>Dep. Variable:</th>         <td>adjdep</td>      <th>  R-squared:         </th> <td>   0.433</td>
</tr>
<tr>
  <th>Model:</th>                   <td>OLS</td>       <th>  Adj. R-squared:    </th> <td>   0.419</td>
</tr>
<tr>
  <th>Method:</th>             <td>Least Squares</td>  <th>  F-statistic:       </th> <td>   30.19</td>
</tr>
<tr>
  <th>Date:</th>             <td>Tue, 01 May 2018</td> <th>  Prob (F-statistic):</th> <td>1.82e-10</td>
</tr>
<tr>
  <th>Time:</th>                 <td>21:56:07</td>     <th>  Log-Likelihood:    </th> <td> -35.735</td>
</tr>
<tr>
  <th>No. Observations:</th>      <td>    82</td>      <th>  AIC:               </th> <td>   77.47</td>
</tr>
<tr>
  <th>Df Residuals:</th>          <td>    79</td>      <th>  BIC:               </th> <td>   84.69</td>
</tr>
<tr>
  <th>Df Model:</th>              <td>     2</td>      <th>                     </th>     <td> </td>   
</tr>
<tr>
  <th>Covariance Type:</th>      <td>nonrobust</td>    <th>                     </th>     <td> </td>   
</tr>
</table>
<table class="table">
<tr>
      <td></td>         <th>coef</th>     <th>std err</th>      <th>t</th>      <th>P>|t|</th>  <th>[0.025</th>    <th>0.975]</th>  
</tr>
<tr>
  <th>Intercept</th> <td>    0.2492</td> <td>    0.105</td> <td>    2.365</td> <td> 0.021</td> <td>    0.039</td> <td>    0.459</td>
</tr>
<tr>
  <th>adjfatal</th>  <td>    0.3845</td> <td>    0.100</td> <td>    3.829</td> <td> 0.000</td> <td>    0.185</td> <td>    0.584</td>
</tr>
<tr>
  <th>adjsimp</th>   <td>    0.3663</td> <td>    0.100</td> <td>    3.649</td> <td> 0.000</td> <td>    0.166</td> <td>    0.566</td>
</tr>
</table>
<table class="table">
<tr>
  <th>Omnibus:</th>       <td>10.510</td> <th>  Durbin-Watson:     </th> <td>   1.178</td>
</tr>
<tr>
  <th>Prob(Omnibus):</th> <td> 0.005</td> <th>  Jarque-Bera (JB):  </th> <td>  10.561</td>
</tr>
<tr>
  <th>Skew:</th>          <td> 0.836</td> <th>  Prob(JB):          </th> <td> 0.00509</td>
</tr>
<tr>
  <th>Kurtosis:</th>      <td> 3.542</td> <th>  Cond. No.          </th> <td>    5.34</td>
</tr>
</table>



---

### Regression assumptions

Now let's try to validate the four assumptions one by one

#### Linearity & Equal variance 

Both can be tested by plotting residuals vs. predictions, where residuals are prediction errors.


```python
> pred_val = reg.fittedvalues.copy()
> true_val = df['adjdep'].values.copy()
> residual = true_val - pred_val
```

```python
> fig, ax = plt.subplots(figsize=(6,2.5))
> _ = ax.scatter(residual, pred_val)
```

![png](/assets/images/Linear-Regression-Diagnostic-in-Python/output_17_0.png)


It seems like the corresponding residual plot is reasonably random. To confirm that, let's go with a hypothesis test, ___Harvey-Collier multiplier test___, for _linearity_


```python
> import statsmodels.stats.api as sms
> sms.linear_harvey_collier(reg)
Ttest_1sampResult(statistic=4.990214882983107, pvalue=3.5816973971922974e-06)
```


Several tests exist for equal variance, with different alternative hypotheses. Let's go with Breusch-Pagan test as an example. More can be found [here](http://www.statsmodels.org/stable/diagnostic.html). Small p-value (`pval` below) shows that ___there is violation of homoscedasticity___.


```python
> _, pval, __, f_pval = statsmodels.stats.diagnostic.het_breuschpagan(residual, df[['adjfatal', 'adjsimp']])
> pval, f_pval
(6.448482473013975e-08, 2.21307383960346e-08)
```

Usually assumption violations are not independent of each other. Having one violations may lead to another. In this case, we see that both linearity and homoscedasticity are not met. Possible data transformation such as log, [Box-Cox power transformation](https://docs.scipy.org/doc/scipy/reference/generated/scipy.stats.boxcox.html), and other fixes may be needed to get a better regression outcome.


#### Normality

We can apply [___normal probability plot___](https://docs.scipy.org/doc/scipy/reference/generated/scipy.stats.probplot.html) to assess how the data (error) depart from normality visually:


```python
> import scipy as sp
> fig, ax = plt.subplots(figsize=(6,2.5))
> _, (__, ___, r) = sp.stats.probplot(residual, plot=ax, fit=True)
> r**2
0.9523990893322951
```

![png](/assets/images/Linear-Regression-Diagnostic-in-Python/output_26_0.png)

The good fit indicates that normality is a reasonable approximation.
