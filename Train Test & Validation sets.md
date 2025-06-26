The first step in our classification task is to randomly split our dataset into three independent sets:

Training Set: The dataset that we feed our model to learn potential underlying patterns and relationships.

Validation Set: The dataset that we use to understand our model's performance across different model types and hyperparameter choices.

Test Set: The dataset that we use to approximate our model's unbiased accuracy in the wild.

The training set should be as representative as possible of the population that we are trying to model. Additionally, we need to be careful and ensure that it is as unbiased as possible, as any bias at this stage may be propagated downstream during inference.

Note that almost everytime we have to work with a logistic regression (classification) model, we can choose among different models (each look at a different feature). How do we decide which one to select?
We could compare the accuracy of each model on the training set, but if we use the same exact dataset for both training and tuning, the model will <overfit> and won't generalize well. Here we can use the validation set as t acts as an independent, unbiased dataset for comparing the performance of different algorithms trained on our training set.

Once we have used the validation set to determine the algorithm and parameter choices that we would like to use in production, the test set is used to approximate the models's true performance in the wild. It is the final step in evaluating our model's performance on unseen data.
ATTENTION: We should never, under any circumstance, look at the test set's performance before selecting a model.
#AI 
[[Cognitive Computing Summary]]
[[Neural Networks]]
