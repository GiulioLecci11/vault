Double Descent is a modern phenomena that appears to counter our notions on bias and variance. Namely, while we expect the best model performance to be obtained via some balance between bias (underfitting) and variance (overfitting), we instead observe strong test performance from very overfit, complex models.

When starting the train phase, initially, train and test (validation) error decrease together. Following the bias-variance tradeoff, as we continue with the training we overfit and the test error starts to grow as the train error continue to decrease.
But the double descent phenomena verifies when, (while continuing the train, so complicating the model) after a peak, the test error goes down again to a new minimun.
In other words, even though our model is extremely overfitted, it has achieved its best performance, and it has done so during this second descent (hence the name, double descent)!

We call the under-parameterized region to the left of the second descent the "classical regime" (the bias-variance principle works as expected), and the point of peak error the <<interpolation threshold>> (we're overfitting here)
To the right of the interpolation threshold, the behavior changes. We call this over-parameterized region the interpolation regime. In this regime, the model perfectly memorizes, or interpolates, the training data.
We can say that past the traditional U-shaped region is the interpolation regime.

Once we're at the interpolation threshold, every model from that complexity level onwards passes through each training data point. The only thing that changes is how the model connects the in-between points. As the models become more and more complex, these connections can become smoother, and the resulting prediction may fit your test data better. This is why models in the interpolation region can perform so well.


PT2: (more mathematical)
https://mlu-explain.github.io/double-descent2/
#AI 