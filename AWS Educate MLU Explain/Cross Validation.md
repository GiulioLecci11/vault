We'd like to find a way to assess the generalization capabilities of a model without having to wait for new data.
Until now the validation set approach has been used, it consists of having 3 sets, namely:
1)The Training Set is used to learn the model parameters.
2)The Validation Set is used to select which model or set of hyperparameters you'd like to use.
3)The Test Set is used to evaluate how your model will perform on unseen data.

The Validation Set Approach is still widely used, especially when resource constraints prohibit alternatives that require <resampling> (like cross validation). Furthermore it come with a big problem: the obvious issue is that our estimate of the test error can be highly variable depending on which particular observations are included in the training set and which are included in the validation set. Another issue is that this approach tends to overestimate the test error for models fit on our entire dataset. This is because more training data usually means better accuracy, but the validation set approach reserves a decent-sized chunk of data for validation and testing (and not training).

K-Fold cross-validation
Rather than worrying about which split of data to use for training versus validation, we'll use them all in turn. Our strategy will be to iteratively use different portions of our data to train and validate our model.We'll randomly split our dataset into k sets, or folds, of equal size. One fold will be reserved as the validation set (or "hold-out set") and the remaining k−1 folds will be used as the training set.
This process will be repeated on our data k times, using a different fold for the validation set at each iteration. At the end of the procedure, we'll take the average of the validation sets' scores and use it as our model's estimated performance.
Note that <<the test data always remains untouched>> (after all, it's the final hold-out set), but the distribution of training and validation sets differs at every fold.

This approach lowers the overall variance on the performance evaluation since multiple distributions of training (and validation) set are evaluated. So it's also correct to say that K-Fold Cross-Validation approach looks at more data in the training phase, so every data point will get included in the training of a model, and the evaluation of that model will then be factored into the final evaluation estimate.

The main cost of this approach is the need to train and evaluate the model multiple times (k times to be precise).


Leave One Out Cross Validation:
A special case of K-Fold Cross-Validation, Leave-One-Out Cross-Validation (LOOCV), occurs when we set k equal to n, the number of observations in our dataset. In Leave-One-Out Cross-Validation, our data is split into a training set containing all but one observations, and a validation set containing the remaining left-out observation.
While the large value of k in LOOCV should minimize the variance in our estimate, it comes with a cost: the need to train a model n times!
Usually in the wild, k is set to 5 or 10.

NOTA CHE K RIGUARDA IL NUMERO DI FOLD, NON DI ELEMENTI PER TRAIN/VALIDATION SET. IL NUMERO DI FOLD RAPPRESENTA IN QUANTE PARTI UGUALI VAI A DIVIDERE IL DATASET (SENZA TEST SET, CHE RESTA INVARIATO). 
POI ALLENI UN MODELLO PER OGNI FOLD, QUINDI + FOLD = + MODELLI DA ALLENARE.

Ritornaci dopo aver visto bias e variance

#AI 
[[Cognitive Computing Summary]]