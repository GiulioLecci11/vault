Logistic regression is a supervised learning algorithm that can be used to classify data into categories, or classes, by predicting the probability that an observation falls into a particular class based on its features.
Though it can be extended to more than two categories, logistic regression is often used for binary classification.

Prob y belongs to x -> P(y=1|X)=sigmoid(z)=1/(1+e^-z)
where z=beta0+b1x1+b2x2....

This is called a linear predictor, and it is transformed by the sigmoid function so that the values fall between 0 and 1, and can therefore be interpreted as probabilities.

Although it resembles a linear regression, there a big difference since the outcomes of a linear regression model can take any numerical value, but these data can only take on outcomes of 0 or 1, so the predictions of a linear model may not be meaningful. Instead, we can fit a logistic function to the data. The values of this function can be interpreted as probabilities, as the values range between 0 and 1.

Evaluating the model:
When fitting our model, the goal is to find the parameters that optimize a function that defines how well the model is performing. Put simply, the goal is to make predictions as close to 1 when the outcome is 1 and as close to 0 when the outcome is 0. In machine learning, the function to be optimized is called the loss function or <<cost function>>. We use the loss function to determine how well our model fits the data.
A suitable loss function in logistic regression is called the Log-Loss, or binary cross-entropy. 
"Minimizing the Log-Loss is equivalent to maximizing the Log-Likelihood", since the Log-Loss is the negative of the Log-Likelihood.
Using this, as the probability gets closer to the true value (p tends to y) the Log-Loss decreases to 0.

Estimating coefficients:
To estimate the correct coefficients b_i we can use 2 different methods: gradient descent and maximum likelihood estimation.

Gradient descent: A common way to estimate coefficients is to use gradient descent. In gradient descent, the goal is to minimize the Log-Loss cost function over all samples. This method involves selecting initial parameter values, and then updating them incrementally by moving them in the direction that decreases the loss. At each iteration, the parameter value is updated by the gradient, scaled by the step size (otherwise known as the learning rate). The gradient is the vector encompassing the direction and rate of the fastest increase of a function, which can be calculated using partial derivatives. The parameters are updated in the opposite direction of the gradient by the step size in an attempt to find the parameter values that minimize the Log-Loss.
Because the gradient calculates where the function is increasing, going in the opposite direction leads us to the minimum of our function.


Maximum likelihood estimation:Another approach is finding the model that maximizes the likelihood of observing the data by using Maximum Likelihood Estimation (MLE). It turns out, minimizing the Log-Loss is equivalent to maximizing the Log-Likelihood. We can do so by differentiating the Log-Likelihood with respect to the parameters (if the derivative is easy to calculate), setting the derivatives equal to 0, and solving the equation to find the estimates of the parameters.

Interpreting the parameters' meaning: Interpreting the coefficients of a logistic regression model can be tricky because the coefficients in a logistic regression are on the log-odds scale. This means the interpretations are different than in linear regression.
Example: Say that the probability of a sunny day is 0.75. This implies that the probability of a rainy day is 0.25. The odds would then be 0.75/0.25=3, which means that the odds of a sunny day are 3 to 1. If the probability of rain is 0.5, then the odds would be 0.5/0.5=1, meaning that the odds of a sunny day are 1 to 1, so sun and rain are equally likely.
Since the coefficients are on the log-odds scale, we can transform them to the odds scale by exponentiating so that they are easier to interpret.

#AI 