---
title: "Calculate Pearson Correlation Confidence Interval in Python"
layout: post
date: 2018-03-31 15:01
headerImage: false
tag:
- python
- statistics
category: blog
hidden: false
author: zhiyzuo
description: using Python to calculate pearson correlation confidence interval
---

<div class="breaker"></div>
```python
import numpy as np
from scipy import stats
```
<div class="breaker"></div>

Recently, many studies have been arguing that we should report effect sizes along with confidence intervals, as opposed to simply reporting p values (e.g., see [this paper](https://www.nature.com/articles/nmeth.3288)).

In Python, however, there is no functions to directly obtain confidence intervals (CIs) of Pearson correlations. I therefore decided to do a quick ssearch and come up with a wrapper function to produce the correlation coefficients, p values, and CIs based on `scipy.stats` and `numpy`. 

There are many tutorials on the detailed steps and I mainly followed [this one](http://davidmlane.com/hyperstat/B8544.html).

### Detailed steps
Let's use a random dataset for an example.
```python
> x = np.random.randint(1, 10, 10)
> y = np.random.randint(1, 10, 10)
> x
array([6, 4, 3, 3, 2, 5, 8, 2, 6, 1])
> y
array([3, 3, 9, 4, 9, 4, 6, 9, 7, 9])
```

The first step involves transformation of the correlation coefficient into a [Fishers' Z-score](https://en.wikipedia.org/wiki/Fisher_transformation).
```python
> r, p = stats.pearsonr(x,y)
> r,p
(-0.5356559002279192, 0.11053303487716389)
> r_z = np.arctanh(r)
> r_z
-0.5980434968020534
```

The corresponding standard deviation is $se = \dfrac{1}{\sqrt{N-3}}$:
```python
> se = 1/np.sqrt(x.size-3)
> se
0.3779644730092272
```

CI under the transformation can be calculated as $r_z \pm z_{\alpha/2}\times se$, where $z_{\alpha/2}$ is can be calculated using `scipy.stats.norm.ppf` function:
```python
> alpha = 0.05
> z = stats.norm.ppf(1-alpha/2)
> lo_z, hi_z = r_z-z*se, r_z+z*se
> lo_z, hi_z
(-1.3388402513358, 0.14275325773169323)
```

Finally, we can reverse the transformation by `np.tanh`:
```
> lo, hi = np.tanh((lo_z, hi_z))
> lo, hi
array([-0.87139341,  0.1417914 ])
```

We can validate this in R:
```R
> x=c(6, 4, 3, 3, 2, 5, 8, 2, 6, 1)
> y=c(3, 3, 9, 4, 9, 4, 6, 9, 7, 9)
> cor.test(x,y)

	Pearson's product-moment correlation

data:  x and y
t = -1.7942, df = 8, p-value = 0.1105
alternative hypothesis: true correlation is not equal to 0
95 percent confidence interval:
 -0.8713934  0.1417914
sample estimates:
       cor
-0.5356559
```

### A wrapper function
I wrapped all these steps up within a single function on [Github gist](https://gist.github.com/zhiyzuo/d38159a7c48b575af3e3de7501462e04):

<script src="https://gist.github.com/zhiyzuo/d38159a7c48b575af3e3de7501462e04.js"></script>
