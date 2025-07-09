Prediction errors can be decomposed into two main subcomponents of interest: error from bias, and error from variance.

When the test error is even higher than the train error, we say that our model is underfitting the data: our model is so simple that it fails to adequately capture the relationships in the data. The high test error is a direct result of the lack of complexity of our model.
Instead when our model is too complicated, we say that it overfits the data. Instead of learning the true trends underlying our dataset, it memorized noise and, as a result, the model is not generalizable to datasets beyond its training data.

Error composition:
Our test error can come as a result of both under- and over- fitting our data, but how do the two relate to each other?

In the general case, mean-squared error can be decomposed into three components: error due to bias, error to to variance, and error due to noise.
Error=Bias^2+Variance+Noise.
We can’t do much about the irreducible Noise term, but we can make use of the relationship between both bias and variance to obtain better predictions.

Bias: Bias represents the difference between the average prediction and the true value, we can think of the bias as measuring a systematic error in prediction.
For underfit (low-complexity) models, the majority of our error comes from bias.

Variance:  Variance measures how much, on average, predictions vary for a given data point.
Predictions from overfit (high-complexity) models show a lot more error from variance than from bias

By trading some bias for variance (i.e. increasing the complexity of our model), and without going overboard, we can find a balanced model for our dataset, a model that doesn't underfit neither overfit.

Graficando il test error in funzione della complessità del modello si vede una forma ad U. Quindi il test error è minore quando ci troviamo nel mezzo, con un modello non troppo complesso nè troppo poco. Questo perché mentre il noise resta più o meno costante al variare della model complexity, Bias inizia alto e va ad abbassarsi e al contempo Variance inizia bassa e va ad innalzarsi.
The ideal model aims to minimize both bias and variance, achieving such a balance will yield the minimum error.

Many models have parameters that change the final learned models, called <<hyperparameters>> Let's look at how these hyperparameters may be used to control the the bias-variance tradeoff with two examples: LOESS Regression and K-Nearest Neighbors.

LOESS Regression:
LOESS (LOcally Estimated Scatterplot Smoothing) regression is a nonparametric technique for fitting a smooth surface between an outcome and some predictor variables. The curve fitting at a given point is weighted by nearby data. This weighting is governed by a smoothing hyperparameter, which represents the proportion of neighboring data used to calculate each local fit. Thus, the bias variance tradeoff for LOESS may be controlled via the smoothness parameter. When the smoothness is small, the amount of data we consider is insufficient for an accurate fit, resulting in large variance. However, if we make the smoothness too high (i.e. over-smoothed), we trade local information for global, resulting in large bias.
Visually, the higher the smoother the less complex is the model

K-Nearest Neighbors:
K-Nearest Neighbors (KNN) classification is a simple technique for assigning class membership to a data point with some majority vote of its K-nearest neighbors. For example, when K = 1, the data point is simply assigned to the class of that single nearest neighbor. If K = 69, the data point is assigned to the majority class of its 69-nearest neighbors.
We can observe the bias variance tradeoff in KNN directly by playing with the hyperparameter K.
Small K= low bias & high variance.
Big K= high bias & low variance.
#miscellaneous 
