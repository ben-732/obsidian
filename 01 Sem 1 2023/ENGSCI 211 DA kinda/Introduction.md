## Statistical Inference
Refers to a situation where we have some unknown population parameter, normally the mean, and we take a sample of that population in order to estimate that unknown parameter

-   Average height of all students
-   Tensile strength of beams made of a new metal alloy

Easy Marks - **Centre**, **Spread**, **Skew**
### Standard Deviation $S$
Calculated from the variance, standard deviation is a measure of spread of the data. The variability ($Var(X)$) measures the average squared distance of each observation form the mean. We square each difference to ensure that it is positive. 
$$\large{Var(X) = \dfrac{(\sum^n_{i=1}(X_i - \bar{X})^2)}{n}}
$$$$\LARGE{s = var(s)}$$
``` R
sd(fabrication.df$gadgetTime)
## [1] 3.041918
```

### Standard Error
Every time we take a sample from a population, we will get a different mean. In order to quantify the sample to sample variability, we uses the standard error. It is an estimate of the variability between the means of different samples. it gives an indication of how far away our observed sample mean is likely to be from the true population mean. It is uncommon for a sample mean to be more than two standard errors away from the true mean. 
$$se(\hat{x}) = \frac{S}{\sqrt{n}}$$

```R
sd(fabrication.df$gadgetTime)/sqrt(nrow(fabrication.df))
## [1] 0.6083837
```

### Confidence Intervals
An x% confidence intervals: Under repeated sampling, x% of such intervals will contain the true population mean.

If we have a 5% confidence interval and take 100 individual samples from the same population we will expect that 95 of the 100 intervals will contain the true mean. This means there's a 95% chance of capturing the true population mean in a study.

Confidence Interval = $\bar{X} \pm t\times se(\bar{X})$

$\varepsilon\overset{\text{iid}}{\sim} N(0, \sigma^2)$


```R
t.test(fabrication.df$gadgetTime)$conf.int

## [1] 9.036358 11.547642
## attr(,"conf.level")
## [1] 0.95```

```


95% is a convention in statistics