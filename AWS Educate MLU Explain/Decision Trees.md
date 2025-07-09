Decision Trees are widely used algorithms for <<supervised>> machine learning. They're popular for their ease of interpretation and large range of applications. They work for both regression and classification problems.

A Decision Tree consists of a series of sequential decisions, or decision nodes, on some data set's features. The resulting flow-like structure is navigated via conditional control statements based on the dataset features, or if-then rules (if feature1<tot else), which split each decision node into two or more subnodes. Leaf nodes, also known as terminal nodes, represent prediction outputs for the model.
To train a Decision Tree from data means to figure out the order in which the decisions should be assembled from the root to the leaves. New data may then be passed from the top down until reaching a leaf node, representing a prediction for that data point.

Note that you don't have to go in too deep: 
If we do, the resulting regions would start becoming increasingly complex, and our tree would become unreasonably deep. Such a Decision Tree would learn too much from the noise of the training examples and not enough generalizable rules. In this case, going too deep results in a tree that overfits our data, so we'll stop here.

So a decision tree from the top down, creates a series of sequential rules that split the data into well-separated regions for classification. But given the large number of potential options, how exactly does the algorithm determine where to partition the data? Before we learn how that works, we need to understand <<Entropy>>.

Entropy measures the amount of information of some variable or event. We'll make use of it to identify regions consisting of a large number of similar (pure) or dissimilar (impure) elements.
Entropy H= - summation (pi*log_base2(pi))
Note that
1)H=0 only if pi=0 for all i except 1 that is equal to 1, so when there is no uncertainty
2)H maximum when all pi are equal so max uncertainty

So the entropy can be used to quantify the impurity of a collection of labeled data points: the higher the entropy the higher the number of different belonging class points in the collection.
We'll use it in the algorithm to train Decision Trees by defining the Information Gain.

"""We then select the split that yields the largest reduction in entropy, or equivalently, the largest increase in information""".

The iterative algorithm that uses the entropy and information gain to understand how to make the decision of the decision tree is called ID3
D3 Algorithm Steps
Calculate the entropy associated to every feature of the data set.
Partition the data set into subsets using different features and cutoff values. For each, compute the information gain ΔIG as the difference in entropy before and after the split using the formula above. For the total entropy of all children nodes after the split, use the weighted average taking into account N child, i.e. how many of the N samples end up on each child branch.
Identify the partition that leads to the maximum information gain. Create a decision node on that feature and split value.
When no further splits can be done on a subset, create a leaf node and label it with the most common class of the data points within it if doing classification or with the average value if doing regression.
Recurse on all subsets. Recursion stops if after a split all elements in a child node are of the same type. Additional stopping conditions may be imposed, such as requiring a minimum number of samples per leaf to continue splitting, or finishing when the trained tree has reached a given maximum depth.

Decision trees definetely have a lot of values: easy to understand, easy to train, minimal data pre processing required. But they come with a great drawback: They can be extremely sensitive to small perturbations in the data: a minor change in the training examples can result in a drastic change in the structure of the Decision Tree. Furthermore if left unchecked in the train phase, they tend to overfit.
There are ways to prevent excessive growth of Decision Trees by pruning them, for instance constraining their maximum depth, limiting the number of leaves that can be created, or setting a minimum size for the amount of items in each leaf and not allowing leaves with too few items in them.
As for the issue of high variance? Well, unfortunately it's an intrinsic characteristic when training a single Decision Tree.
#AI 