Receiver Operating Characteristic Curve (the ROC Curve) and the Area Under the ROC Curve (AUC, or AUROC):
The operator's ability to identify as many true positives as possible while minimizing false positives is named the Receiver Operating Characteristic, and the curve analyzing their predictive abilities is called the ROC Curve.
In machine learning, we use ROC Curves to analyze the predictive power of a classifier: they provide a visual way to observe how changes in our model’s classification thresholds affect our model’s performance, in particular the curves allow us to select for classification thresholds that allow our model to identify as many true positives as possible while minimizing false positives.

In general,the ROC curve is composed by plotting a model's True-Positive Rate (TPR) versus its False-Positive Rate (FPR) across all possible classification thresholds, where:
True Positive Rate (TPR): The probability that a positive sample is correctly predicted in the positive class. E.g., the percentage of radar signals predicted to be airplanes that actually are airplanes.
False Positive Rate (FPR): The probability that a negative sample is incorrectly predicted in the positive class. E.g., the percentage of radar signals predicted to be airplanes that actually are not airplanes.

Recall that our goal is to find the classification threshold that best maximizes true positives while minimizing false positives.
To find that threshold, we'll have to try all possible values for our threshold!
After plotting each classification threshold's corresponding TPR and FPR, we obtain our ROC curve!
(Essa è quindi l'andamento nel piano FPR,TPR al variare della threshold su ogni valore possibile).

The ROC Curve is valuable not only because it gives us an overview of our model's performance, but because it also gives us an easy visual to compare the performance of different classifiers.
A perfect classifier is one that hugs along the outer-left and top of the chart (alto a sx -> True positive rate 1, False positive rate 0). This is expected, as ‘perfect’ here implies the classifier will always have a TPR=1, regardless of the FPR. On the other hand, a diagonal line implies that TPR=FPR for every classification threshold - in other words, the classifier is just making random guesses, curves under the diagonal line are even worse than random guessing.

AUC (sometimes written AUROC) is just the area underneath the entire ROC curve, it ranges in value from 0 to 1, with higher numbers indicating better performance. A perfect classifier will have an AUC of 1, while a perfectly random classifier an AUC of 0.5.
In other words (presi 2 sample, uno negativo e uno positivo) a model that always predicts that a negative sample is more likely to have a positive label than a positive sample will have AUC of 0. If the predicted probabilities are random, it will be 0.5. Finally if the model always predicts a positive sample is more likely to have a positive label than a negative sample, then it will have an AUC of 1.

Those parameter however have some drawbacks, for example the AUC metric treats all classification errors equally, ignoring the potential consequences of ignoring one type over another. For example, in cancer detection, we’ll probably want to minimize false negatives (even at the risk of increasing false positives)
#miscellaneous 