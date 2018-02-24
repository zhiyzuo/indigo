---
title: "Visualizing Dot-Whisker Regression Coefficients in Python"
layout: post
date: 2018-02-22 21:15
headerImage: false
tag:
- python
- statistics
- visualization
category: blog
hidden: false
author: zhiyzuo
description: using Python to viualize dot-whisker regression coefficients
---


Today I spent some time to work out better visualizations for a manuscript in Python using Matplotlib. I figured I should write it down because there are really very few resource on this!

<div class="breaker"></div>

```python
import pandas as pd
import statsmodels.formula.api as smf
```


```python
from matplotlib import pyplot as plt
from matplotlib.lines import Line2D
%matplotlib inline
```

<div class="breaker"></div>

In the past year, I've been using R for regression analysis. This is mainly because there are great packages for visualizing regression coefficients:

- [dotwhisker](https://cran.r-project.org/web/packages/dotwhisker/vignettes/dotwhisker-vignette.html)
- [coefplot](https://cran.r-project.org/web/packages/coefplot/index.html)

However, I hardly found any useful counterparts in Python. The closest I got from Google is from [statsmodels](http://www.statsmodels.org/dev/examples/notebooks/generated/regression_plots.html), but it is not very good. The other one I found is related to this [StackOverflow question](https://stackoverflow.com/questions/28642360/coefficient-plot-in-python), which used [seaborn](https://seaborn.pydata.org/)'s `coefplot`, which has already been deprecated and not usable.

Therefore, I decided to try using [matplotlib](https://matplotlib.org/api/_as_gen/matplotlib.pyplot.bar.html) to build my own ___dot-and-whisker plots___ that can be used for journal publications.

---

### Data preparation

Let's play with the [swiss dataset](https://raw.githubusercontent.com/vincentarelbundock/Rdatasets/master/csv/datasets/swiss.csv).


```python
url = "https://raw.githubusercontent.com/vincentarelbundock/Rdatasets/master/csv/datasets/swiss.csv"
df = pd.read_csv(url, index_col=0)
df.head()
```

<div>
<table class="table">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Fertility</th>
      <th>Agriculture</th>
      <th>Examination</th>
      <th>Education</th>
      <th>Catholic</th>
      <th>Infant.Mortality</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Courtelary</th>
      <td>80.2</td>
      <td>17.0</td>
      <td>15</td>
      <td>12</td>
      <td>9.96</td>
      <td>22.2</td>
    </tr>
    <tr>
      <th>Delemont</th>
      <td>83.1</td>
      <td>45.1</td>
      <td>6</td>
      <td>9</td>
      <td>84.84</td>
      <td>22.2</td>
    </tr>
    <tr>
      <th>Franches-Mnt</th>
      <td>92.5</td>
      <td>39.7</td>
      <td>5</td>
      <td>5</td>
      <td>93.40</td>
      <td>20.2</td>
    </tr>
    <tr>
      <th>Moutier</th>
      <td>85.8</td>
      <td>36.5</td>
      <td>12</td>
      <td>7</td>
      <td>33.77</td>
      <td>20.3</td>
    </tr>
    <tr>
      <th>Neuveville</th>
      <td>76.9</td>
      <td>43.5</td>
      <td>17</td>
      <td>15</td>
      <td>5.16</td>
      <td>20.6</td>
    </tr>
  </tbody>
</table>
</div>



Change column names for convenience.


```python
df.columns = ['fertility', 'agri', 'exam', 'edu', 'catholic', 'infant_mort']
```

Now, let's build a simple regression model.


```python
formula = 'fertility ~ %s'%(" + ".join(df.columns.values[1:]))
formula
```

    'fertility ~ agri + exam + edu + catholic + infant_mort'


```python
lin_reg = smf.ols(formula, data=df).fit()
lin_reg.summary()
```




<div >
<table class="table">
<caption>OLS Regression Results</caption>
<tr>
  <th>Dep. Variable:</th>        <td>fertility</td>    <th>  R-squared:         </th> <td>   0.707</td>
</tr>
<tr>
  <th>Model:</th>                   <td>OLS</td>       <th>  Adj. R-squared:    </th> <td>   0.671</td>
</tr>
<tr>
  <th>Method:</th>             <td>Least Squares</td>  <th>  F-statistic:       </th> <td>   19.76</td>
</tr>
<tr>
  <th>Date:</th>             <td>Thu, 22 Feb 2018</td> <th>  Prob (F-statistic):</th> <td>5.59e-10</td>
</tr>
<tr>
  <th>Time:</th>                 <td>20:56:58</td>     <th>  Log-Likelihood:    </th> <td> -156.04</td>
</tr>
<tr>
  <th>No. Observations:</th>      <td>    47</td>      <th>  AIC:               </th> <td>   324.1</td>
</tr>
<tr>
  <th>Df Residuals:</th>          <td>    41</td>      <th>  BIC:               </th> <td>   335.2</td>
</tr>
<tr>
  <th>Df Model:</th>              <td>     5</td>      <th>                     </th>     <td> </td>   
</tr>
<tr>
  <th>Covariance Type:</th>      <td>nonrobust</td>    <th>                     </th>     <td> </td>   
</tr>
</table>


<table class="table">
<tr>
       <td></td>          <th>coef</th>     <th>std err</th>      <th>t</th>      <th>P>|t|</th>  <th>[0.025</th>    <th>0.975]</th>  
</tr>
<tr>
  <th>Intercept</th>   <td>   66.9152</td> <td>   10.706</td> <td>    6.250</td> <td> 0.000</td> <td>   45.294</td> <td>   88.536</td>
</tr>
<tr>
  <th>agri</th>        <td>   -0.1721</td> <td>    0.070</td> <td>   -2.448</td> <td> 0.019</td> <td>   -0.314</td> <td>   -0.030</td>
</tr>
<tr>
  <th>exam</th>        <td>   -0.2580</td> <td>    0.254</td> <td>   -1.016</td> <td> 0.315</td> <td>   -0.771</td> <td>    0.255</td>
</tr>
<tr>
  <th>edu</th>         <td>   -0.8709</td> <td>    0.183</td> <td>   -4.758</td> <td> 0.000</td> <td>   -1.241</td> <td>   -0.501</td>
</tr>
<tr>
  <th>catholic</th>    <td>    0.1041</td> <td>    0.035</td> <td>    2.953</td> <td> 0.005</td> <td>    0.033</td> <td>    0.175</td>
</tr>
<tr>
  <th>infant_mort</th> <td>    1.0770</td> <td>    0.382</td> <td>    2.822</td> <td> 0.007</td> <td>    0.306</td> <td>    1.848</td>
</tr>
</table>

<table class="table">
<tr>
  <th>Omnibus:</th>       <td> 0.058</td> <th>  Durbin-Watson:     </th> <td>   1.454</td>
</tr>
<tr>
  <th>Prob(Omnibus):</th> <td> 0.971</td> <th>  Jarque-Bera (JB):  </th> <td>   0.155</td>
</tr>
<tr>
  <th>Skew:</th>          <td>-0.077</td> <th>  Prob(JB):          </th> <td>   0.925</td>
</tr>
<tr>
  <th>Kurtosis:</th>      <td> 2.764</td> <th>  Cond. No.          </th> <td>    807.</td>
</tr>
</table>
</div>



Now that we obtained the result, we need three things for building the coefficient plot:

- Point estimates (`coef`)
- Confidence intervals (`[0.025, 0.975]`)
- Covariate names (of course :sweat_smile:)


```python
lin_reg.params
```

    Intercept      66.915182
    agri           -0.172114
    exam           -0.258008
    edu            -0.870940
    catholic        0.104115
    infant_mort     1.077048
    dtype: float64

```python
lin_reg.conf_int()
```




<div>
<table class="table table-sm">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>0</th>
      <th>1</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Intercept</th>
      <td>45.293900</td>
      <td>88.536463</td>
    </tr>
    <tr>
      <th>agri</th>
      <td>-0.314096</td>
      <td>-0.030132</td>
    </tr>
    <tr>
      <th>exam</th>
      <td>-0.770726</td>
      <td>0.254709</td>
    </tr>
    <tr>
      <th>edu</th>
      <td>-1.240574</td>
      <td>-0.501306</td>
    </tr>
    <tr>
      <th>catholic</th>
      <td>0.032911</td>
      <td>0.175320</td>
    </tr>
    <tr>
      <th>infant_mort</th>
      <td>0.306150</td>
      <td>1.847947</td>
    </tr>
  </tbody>
</table>
</div>



Calcualte the error bar lengths for confidence intervals.


```python
err_series = lin_reg.params - lin_reg.conf_int()[0]
err_series
```


    Intercept      21.621281
    agri            0.141982
    exam            0.512717
    edu             0.369634
    catholic        0.071205
    infant_mort     0.770898
    dtype: float64



Typically, we do not care about intercepts.


```python
coef_df = pd.DataFrame({'coef': lin_reg.params.values[1:],
                        'err': err_series.values[1:],
                        'varname': err_series.index.values[1:]
                       })
coef_df
```

<div>
<table class="table table-sm">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>coef</th>
      <th>err</th>
      <th>varname</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>-0.172114</td>
      <td>0.141982</td>
      <td>agri</td>
    </tr>
    <tr>
      <th>1</th>
      <td>-0.258008</td>
      <td>0.512717</td>
      <td>exam</td>
    </tr>
    <tr>
      <th>2</th>
      <td>-0.870940</td>
      <td>0.369634</td>
      <td>edu</td>
    </tr>
    <tr>
      <th>3</th>
      <td>0.104115</td>
      <td>0.071205</td>
      <td>catholic</td>
    </tr>
    <tr>
      <th>4</th>
      <td>1.077048</td>
      <td>0.770898</td>
      <td>infant_mort</td>
    </tr>
  </tbody>
</table>
</div>



---

### Let's plot!

#### Basic `coefplot`

The basic idea is that we plot a bar chart ___without facecolors___, and then we can add scatter plots to annotate the coefficients values by markers.


```python
fig, ax = plt.subplots(figsize=(8, 5))
coef_df.plot(x='varname', y='coef', kind='bar', 
             ax=ax, color='none', 
             yerr='err', legend=False)
ax.set_ylabel('')
ax.set_xlabel('')
ax.scatter(x=pd.np.arange(coef_df.shape[0]), 
           marker='s', s=120, 
           y=coef_df['coef'], color='black')
ax.axhline(y=0, linestyle='--', color='black', linewidth=4)
ax.xaxis.set_ticks_position('none')
_ = ax.set_xticklabels(['Agriculture', 'Exam', 'Edu.', 'Catholic', 'Infant Mort.'], 
                       rotation=0, fontsize=16)
```


![png](/assets/images/Plot-Regression-Coefficient/output_27_0.png)


#### Control/study variables?

Sometimes we may want to annotate which are control variables and which are study variables. We use use [Axes.annotate](https://matplotlib.org/api/_as_gen/matplotlib.axes.Axes.annotate.html) to highlight them! Let's say we want to highlight the last two as study variables.

_The answer to [this StackOverflow question](https://stackoverflow.com/questions/35320437/drawing-brackets-over-plot) helps me a lot. Credit to the author!_


```python
fig, ax = plt.subplots(figsize=(8, 5))
coef_df.plot(x='varname', y='coef', kind='bar', 
             ax=ax, color='none', 
             yerr='err', legend=False)
ax.set_ylabel('')
ax.set_xlabel('')
ax.scatter(x=pd.np.arange(coef_df.shape[0]), 
           marker='s', s=120, 
           y=coef_df['coef'], color='black')
ax.axhline(y=0, linestyle='--', color='black', linewidth=4)
ax.xaxis.set_ticks_position('none')
_ = ax.set_xticklabels(['Agriculture', 'Exam', 'Edu.', 'Catholic', 'Infant Mort.'], 
                       rotation=0, fontsize=16)

fs = 16
ax.annotate('Control', xy=(0.3, -0.2), xytext=(0.3, -0.3), 
            xycoords='axes fraction', 
            textcoords='axes fraction', 
            fontsize=fs, ha='center', va='bottom',
            bbox=dict(boxstyle='square', fc='white', ec='black'),
            arrowprops=dict(arrowstyle='-[, widthB=6.5, lengthB=1.2', lw=2.0, color='black'))

_ = ax.annotate('Study', xy=(0.8, -0.2), xytext=(0.8, -0.3), 
                 xycoords='axes fraction', 
                 textcoords='axes fraction', 
                 fontsize=fs, ha='center', va='bottom',
                 bbox=dict(boxstyle='square', fc='white', ec='black'),
                 arrowprops=dict(arrowstyle='-[, widthB=3.5, lengthB=1.2', lw=2.0, color='black'))
```


![png](/assets/images/Plot-Regression-Coefficient/output_31_0.png)


---

### Multiple models

It's also very easy to generalize this barchart to incorporate multiple models' results by shifting barchart's X axis positions.

let's say we have two models:

- Three controls plus ___Catholic___
- Three controls plus ___Infant Mort.___

#### Collect coefficients


```python
formula_1 = 'fertility ~ %s'%(" + ".join(df.columns.values[1:-1]))
print(formula_1)
mod_1 = smf.ols(formula_1, data=df).fit()
mod_1.params
```

    fertility ~ agri + exam + edu + catholic

    Intercept    91.055424
    agri         -0.220646
    exam         -0.260582
    edu          -0.961612
    catholic      0.124418
    dtype: float64


```python
formula_2 = 'fertility ~ %s'%(" + ".join(df.columns.values[1:-2].tolist() + ['infant_mort']))
print(formula_2)
mod_2 = smf.ols(formula_2, data=df).fit()
mod_2.params
```

    fertility ~ agri + exam + edu + infant_mort

    Intercept      68.773136
    agri           -0.129292
    exam           -0.687994
    edu            -0.619649
    infant_mort     1.307097
    dtype: float64

```python
coef_df = pd.DataFrame()
for i, mod in enumerate([mod_1, mod_2]):
    err_series = mod.params - mod.conf_int()[0]
    coef_df = coef_df.append(pd.DataFrame({'coef': mod.params.values[1:],
                                           'err': err_series.values[1:],
                                           'varname': err_series.index.values[1:],
                                           'model': 'model %d'%(i+1)
                                          })
                            )
coef_df
```




<div>
<table class="table">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>coef</th>
      <th>err</th>
      <th>model</th>
      <th>varname</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>-0.220646</td>
      <td>0.148531</td>
      <td>model 1</td>
      <td>agri</td>
    </tr>
    <tr>
      <th>1</th>
      <td>-0.260582</td>
      <td>0.553176</td>
      <td>model 1</td>
      <td>exam</td>
    </tr>
    <tr>
      <th>2</th>
      <td>-0.961612</td>
      <td>0.392609</td>
      <td>model 1</td>
      <td>edu</td>
    </tr>
    <tr>
      <th>3</th>
      <td>0.124418</td>
      <td>0.075207</td>
      <td>model 1</td>
      <td>catholic</td>
    </tr>
    <tr>
      <th>0</th>
      <td>-0.129292</td>
      <td>0.151049</td>
      <td>model 2</td>
      <td>agri</td>
    </tr>
    <tr>
      <th>1</th>
      <td>-0.687994</td>
      <td>0.456646</td>
      <td>model 2</td>
      <td>exam</td>
    </tr>
    <tr>
      <th>2</th>
      <td>-0.619649</td>
      <td>0.355803</td>
      <td>model 2</td>
      <td>edu</td>
    </tr>
    <tr>
      <th>3</th>
      <td>1.307097</td>
      <td>0.820514</td>
      <td>model 2</td>
      <td>infant_mort</td>
    </tr>
  </tbody>
</table>
</div>



#### Plot!


```python
## marker to use
marker_list = 'so'
width=0.25
## 5 covariates in total
base_x = pd.np.arange(5) - 0.2
base_x
```




    array([-0.2,  0.8,  1.8,  2.8,  3.8])




```python
fig, ax = plt.subplots(figsize=(8, 5))
for i, mod in enumerate(coef_df.model.unique()):
    mod_df = coef_df[coef_df.model == mod]
    mod_df = mod_df.set_index('varname').reindex(coef_df['varname'].unique())
    ## offset x posistions
    X = base_x + width*i
    ax.bar(X, mod_df['coef'],  
           color='none',yerr=mod_df['err'])
    ## remove axis labels
    ax.set_ylabel('')
    ax.set_xlabel('')
    ax.scatter(x=X, 
               marker=marker_list[i], s=120, 
               y=mod_df['coef'], color='black')
    ax.axhline(y=0, linestyle='--', color='black', linewidth=4)
    ax.xaxis.set_ticks_position('none')
    _ = ax.set_xticklabels(['', 'Agriculture', 'Exam', 'Edu.', 'Catholic', 'Infant Mort.'], 
                           rotation=0, fontsize=16)

    fs = 16
    ax.annotate('Control', xy=(0.3, -0.2), xytext=(0.3, -0.35), 
                xycoords='axes fraction', 
                textcoords='axes fraction', 
                fontsize=fs, ha='center', va='bottom',
                bbox=dict(boxstyle='square', fc='white', ec='black'),
                arrowprops=dict(arrowstyle='-[, widthB=6.5, lengthB=1.2', lw=2.0, color='black'))

    ax.annotate('Study', xy=(0.8, -0.2), xytext=(0.8, -0.35), 
                xycoords='axes fraction', 
                textcoords='axes fraction', 
                fontsize=fs, ha='center', va='bottom',
                bbox=dict(boxstyle='square', fc='white', ec='black'),
                arrowprops=dict(arrowstyle='-[, widthB=3.5, lengthB=1.2', lw=2.0, color='black'))
    
## finally, build customized legend
legend_elements = [Line2D([0], [0], marker=m,
                          label='Model %d'%i,
                          color = 'k',
                          markersize=10)
                   for i, m in enumerate(marker_list)
                  ]
_ = ax.legend(handles=legend_elements, loc=2, 
              prop={'size': 15}, labelspacing=1.2)
```


![png](/assets/images/Plot-Regression-Coefficient/output_42_0.png)


Finally, we got something that's very similar to those produced by R packages! Further, we can easily extend this to the situation where we need subplots to include more models. I was thinking of getting a wrapper around this in case I need this in the future. However, the adjustments of `annotate`'s coordinates and `arrowprops` properties seem to be not that trivial to me...
