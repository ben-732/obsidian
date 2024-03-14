Basic steps of machine learning:
1. Select the "Right" ML model to fit the data
2. Get data, preferably lots of high quality data. 
3. Clean data, carry out *Feature Engineering* and *Target Engineering* so it has the variables we are interested in
4. Fit model to data, to minimise prediction error - Do not under or over fit
	1. You need to tune hyper parameters to help us do this, 
5. Determine best model for prediction
6. Put model into production, have it predict values on new data. 

#### Why we can't use `predict()`
We build regression models using `lm()` on just ONE dataset, so the results are optimised for that particular dataset. 
> We fit the model using data, and then measure the quality of fit on the same data

If we get a different dataset, perhaps our model should not look exactly the same. We want a model that we can generalise to new values. 

In ML we want to determine what properties the model should have if it sees new data
- how complicated the model should be
- What variables the model should contain

### How to build a ML model. 
#### Training/Test sets
We want to measure how well a model predicts, but we usually only have one dataset. So we simulate

We split our data into two sets, a **Training** and a **Testing** set. We manipulate our model as required and fit our model on the training set. Then we see how good the fitted model is at predicting data from the test set. 

Generally we split the data so that 60-80% is in the training set and 20-40% is in the test set. 

### Model Evaluation
We need to be able to evaluate how well a model preforms. We can do this by evaluating the mean square (prediction) error
$$
\large\text{MSE} = \frac{1}{n}\sum^n_{i=1}(\underbrace{ y_{i} }_{ \text{Actual} }- \underbrace{ \hat{y}_{i} }_{ \text{Predicted} })^{2}
$$

This is like the variance, it gives a measure of spread. We square so that negative values work

This however, isn't much use to us in terms of squared units. So we square root to get the root mean square error, which is back on the original scale.

$$
\text{RMSE} = \sqrt{ \frac{1}{n}\sum^n_{i=1}(y_{i} - \hat{y}_{i})^{2} }
$$
There is such thing as the mean absolute error but this isn't very popular in practice. 

This tells us how much our model is inaccurate by on average
	"Our model on average, mis-predicts by 12.2 points marks on the exam"

This is only on one test run so it doesn't tell us how the model generalises. 

We are more interested in how this model preforms in general. That is, how well it might preform if faced with slightly different input data for the training test. 

using only one set limits the model as the set may be bad in some way...

To ensure that the each data-point gets a chance to be tested, we can try dividing the dataset into $k$ groups (folds). We then fit the model on $k-1$ folds and test it on the remaining fold. We repeat this $k$ times so that each fold gets tested. 

We then average the test mean scores across all the folds so we can get an approximation of the error we might see on if the model is trained and tested on unseen data. 
$k = 5$ or $k = 10$ are common choices but sometimes we see $k = n$ which is where we have as many folds as datapoints. 

![[Pasted image 20230511145729.png | center | 600]]

### Model Usage
Once we determine the model that we are happy with by looking at the properties of the model, then we can use that model. In `regression` it is easiest to re-fit the model using `lm()` and go from there 

### Problems in ML
#### Feature and Target engineering



#### Bias-Variance Trade-off
Machine learning models can be very complex - and fitting complex models often means we can model the training set very well, but the model is useless for predicting on new data. In the regression context, we can think about fitting curves instead of lines. 
![[Bias-Variance Trade-off graph.png| center | 500]]

Here there are two situations to account for:
**Overfit**: When the model fits the data too well, so the model does not generalise to unseen data. 
- This model gives high variance when we predict for new data
- if we repeatedly sample and refit the mode, we would get get wildly different predictions.

**Under fit**: When the model is too simple and does not fit the data well enough. so the predictions are biased. 
- fitting the null model to the previous  graph would be an extreme case of this.

To overcome this, we would want to try tweaking the input parameters to the function we are fitting to find the combination that gives the smallest root mean square error on the test set.

![[variance and bias.png | center | 300]]

#### Missing Data
Real data often has missing values. Sometimes they are omitted randomly due to recording or human error - which is not usually a big deal. 

More often, missing data tells us something interesting
- Website users blocking all cookies might mean you have a lot less data on vairables of interest; but also tell us something about their technical prowess
- Data on people might have variables such as income missing. Some may genuinely not have an income, but other reasons might tell things like tax fraud, illegal activity, etc. 

We can try to impute missing values, but that is a book-length discussion. We would be making very strong assumptions on how the data behaves no matter what we do. 


#### Class imbalance
Sometimes the dataset we build our model with is not actually helpful for answering the question of interest. 
- If we were trying to predict whether a person will default on a loan, five bank records, the data migth show that 95% of people do not default on a loan and only 5% do. 
- However if we were to build a machine learning model, a fairly good model might be able to simply predict that a customer will NOT default regardless of what the data actually says. This model will be 95% accurate. 
- To avoid this we would have to create a dataset with an equal amount of defaulters and non-defaulters.

#### Data Bias in general
When ML models are put into production, we can predict on data points that are totally different to what the model was trained with.

For example:
- Use the `lm(Exam ~ Test)` model to predict exam marks for Arts or Law courses where law still uses quotas to determine grades
- Build a model to predict earthquake damage from data in Europe and directly apply it to NZ

This is less of a big deal in ‘classic’ model fitting because we are often just interested in the trends in the data, and how the model was fitted is often described somewhere.

In a black-box predictive model, this can be a bigger problem – especially if you use o-the-shelf models for things like image recognition

#### Historical Bias
A common application of machine learning is to build models that replicate human decision making. like training models on past human decisions. 

However humans are not perfect, decisions are often made with subconscious biasses. 
Examples of this are
- Bank Loans
- Health Insurance
- Facial Recognition

We need to evaluate the models to ensure such biases are not perpetuated further. 

### Visualisations...
ML is vey cool - if you have the chance to work with advanced ML models, it is very interesting and powerful. However, really cool models (Boosted trees, neural nets) are very hard to example. 

For most applications, a picture might tell ur client / manager all the information they need. 
![[ML Graphs.png| center  | 600]]