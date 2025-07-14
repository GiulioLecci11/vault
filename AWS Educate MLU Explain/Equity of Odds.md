Unwanted bias in machine learning can inadvertently harm, and negatively stereotype against underrepresented or (historically and otherwise) disfavored groups. Therefore, it is crucial to evaluate and control data and model predictions not only for general machine learning performance but also for bias.

Equality of Odds (EO) aims to equalize the error a model makes when predicting categorical outcomes for different groups. For example, if we consider a hiring scenario, the errors EO compares are 'wrong rejection' and 'wrong acceptance'. We could simply count the number of wrong rejections and acceptances but as groups generally differ in size, we should use error rates instead as those are scale invariant. Useful error rates to consider are the False Negative Rate (FNR) and False Positive Rate (FPR) of a classifier, or the combination of both those error rates,

Using EO to measure fairness:
According to EO, a model is fair if the predictions it makes have the same TPR[ℹ] and FPR across all groups in the dataset. Note that there can be some so-called <<lazy solutions>> where everyone gets rejected or accepted. These are solutions where the FPR or FNR difference is indeed 0. However, while these solutions technically meet the relaxed version of EO, they make little sense from a general ML performance perspective (check out the accuracy values of the model).

Using EO to achieve fairness: 
Using EO, we can also influence the predictions a model makes to achieve a more fair outcome. We are going to look at two different ways of performing this: by constraining the model during training and by introducing group-wise probability thresholds for a trained model. To implement EO during model training, we can constrain the possible set of parameters, and make the problem an "optimization problem".

To visualize the search for the probability thresholds that meets EO, we can look at the so-called "ROC curves".

While machine learning algorithms have the potential to revolutionize decision-making, we have to ensure that a fairness criteria is used for measuring any potential bias in addition to general Machine Learning metrics. Depending on the outcome of the bias evaluation we should include bias mitigation. Equality of Odds (EO) offers a promising approach to mitigate bias and is a method that can be used in different ways (and even during post-processing with access only to the predictions).
#miscellaneous 