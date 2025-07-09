Linear Regression is a simple and powerful model for predicting a numeric response from a set of one or more independent variables.
Linear regression is a supervised algorithm that learns to model a dependent variable, y, as a function of some independent variables (aka "features"), xi, by finding a line (or surface) that best "fits" the data. In general, we assume y to be some number and each xi can be basically anything. For example: predicting the price of a house using the number of rooms in that house (y: price, x1: number of rooms) or predicting weight from height and age (y: weight,x1: height, x2: age).

In general y=b+b1x1+b2x2+..+eps.
With y the dependent variable that we're trying to predict, xi the indipendent variables (features), bi the coefficients (weights), eps the irriducible error in the model.
Fitting a linear regression model is all about finding the set of cofficients that best model y as a function of our features.
Once we have estimated them we can proceed with predicting y, i.e.
y_predicted=b+b1x2+b2x2+..

Evaluate model's performance:
To evaluate our model's performance quantitatively, we plot the error of each observation directly. These errors, or <<residuals>>, measure the distance between each observation and the predicted value for that observation.

Several popular loss functions exist for regression problems

Mean Squared Error(MSE): MSE quantifies how close a predicted value is to the true value, so we'll use it to quantify how close a regression line is to a set of points. MSE works by squaring the distance between each data point and the regression line, summing the squared values, and then dividing by the number of data points.

R-Squared: Regression models may also be evaluated with the so-called goodness of fit measures, which summarize how well a model fits a set of data. The most popular goodness of fit measure for linear regression is r-squared, a metric that represents the percentage of the variance in y explained by our features x.
The highest possible value for r-squared is 1, representing a model that captures 100% of the variance. A negative r-squared means that our model is doing worse (capturing less variance) than a flat line through mean of our data would.

The evaluation metric should reflect whatever it is you actually care about when making predictions. For example, when we use MSE, we are implicitly saying that we think the cost of our prediction error should reflect the quadratic (squared) distance between what we predicted and what is correct. This may work well if we want to punish outliers or if our data is minimized by the mean, but this comes at the cost of interpretability: we output our error in squared units.

Linear regression is all about finding a line (or surface) that fits our data well. And as we just saw, this involves selecting the coefficients for our model that minimize our evaluation metric. But how can we best estimate these coefficients? Let's talk about 2 methods

Iterative Solution (Grad Desc): Gradient descent is an iterative optimization algorithm that estimates some set of coefficients to yield the minimum of a convex function. Put simply: it will find suitable coefficients for our regression model that minimize prediction error (remember, lower MSE equals better model). Although gradient descent is the most popular optimization algorithm in machine learning, it's not perfect! It doesn't work for every loss function, and it may not always find the most optimal set of coefficients for your model.

Closed Form Solution: the Normal Equation is a closed-form solution that allows us to estimate our coefficients directly by minimizing the residual sum of squares (RSS) of our data.
RSS=SUM(yi-yi_pred)^2 and then b=(X'*X)^-1*X'*Y (pseudoinverse of X)
However, the Normal Equation estimates are often not used in practice, because of the computational complexity required to invert a matrix with too many features

#miscellaneous 
