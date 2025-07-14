When evaluating a classifier, accuracy isn't always a great measure of classification performance, there are other metrics that can be taken into account. You can use a <<confusion matrix>> to visualize the performance of a classification model, i.e. a matrix like this
	 Predicted:1 	Predicted:0
Actual:1    TP		   FN
Actual:0    FP		   TN

Instead of looking at the model's raw accuracy (the number of correctly assigned categories divided by te total number of predictions) , the confusion matrix decomposes predictions into several categories of interest, making explicit how one class may be confused for another:

True Positives (TP): The number of positive instances correctly classified as positive. E.g., predicting an email as spam when it actually is spam.
False Positives (FP): The number of negative instances incorrectly classified as positive. E.g., predicting an email is spam when it actually is not spam.
True Negatives (TN): The number of negative instances correctly classified as negative. E.g., predicting an email is not spam when it actually is not spam.
False Negatives (FN): The number of positive instances incorrectly classified as negative. E.g., predicting an email is not spam when it actually is spam.

A model's accuracy may be obtained as (TP+TN)/(TP+FP+TN+FN) (cioè numero di classificazioni corrette/numero totale di classificazioni).
A problem when we use this kind of metrics occurs in the case that our dataset is not balanced (for example if you have many more cases belonging to a category with respect to the other, the model can obtain a good accuracy even predicting everyone as belonging to the more popular category). So we need more metrics.

Precision: it's the ratio of correctly predicted positive classes to all items PREDICTED to be positive (falsi positivi compresi) TP/TP+FP.
[Questo ci dice quanto sono corrette (precise) le predizioni positive del modello]
This is important when false positive predictions are more important than false negatives (vogliamo pochi falsi positivi, quindi non vogliamo che una mail che non è spam venga classificata come spam).

Recall: it's the ratio of correctly predicted positive classes to all items that ARE ACTUALLY positive (falsi negativi, che in realtà sono quindi positivi) compresi. [Ci dice quanto è probabile che le istanze positive siano predette come positive].
This is important when false negatives are more important than false positives (vogliamo pochi falsi negativi, vogliamo che tutti i positivi siano classificati come tali, come le diagnosi di cancro).

Obtaining both perfect precision and recall is almost impossibile, so you have to understand what your problems.


F1-Score: 
The F1-score (also sometimes called the F-Measure) is a single performance metric that takes both precision and recall into account. It's calculated by taking the harmonic mean of the two metrics -> 	2*Precision*Recall / (Precision + Recall).
It varies in a range between 0 (worst) and 1 (best). Note that only when both precision and recall have good performance will the F1-score be high.
The F1-score is a great way to compare the performance of multiple classifiers. When choosing between multiple models, all with varying values of precision and/or recall, it may be used to determine which one produces the 'best' results for the problem at hand.

But it also has drawbacks, first of all, JUST LIKE PRECISION AND RECALL, it totally ignores true negatives. Furthermore it gives equal value to precision and recall but this may depend on the problem you are working with
#AI 
[[Vector Databases]]
[[Cognitive Computing Summary]]
[[RAG Concepts]]