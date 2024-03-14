

These trends are classic symptoms of an underlying multiplicative model. Assuming EOV would be naïve in this case. If we did, we would get the following EOV check after fitting a linear model.





### Regression

In this case, we are looking at a dataset to see how cars loose value as they age. The data was collected in 1991 so we can subtract the year it was manufactured from that to get the cars' age. 

Plotting this data shows that 
![[Multiplicative - EOV, No Log.png|400]]
Here we see data that both curves and fans 



```R
lm(log(y ~ x))
```

$$\log(y) =\beta_{0} + \beta_{1}x$$
$$y= e^{\beta_0 + \beta_{1}x}$$

Only the Betas ($\beta$) are multiplicative

$\varepsilon \overset{\text{iid}}{\sim} N(0, \sigma^2)$

