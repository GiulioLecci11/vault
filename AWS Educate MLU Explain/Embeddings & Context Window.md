The word itself may have changed meaning, from referring to a specific mathematical concept to now having a pure ML meaning for an audience that might be mostly unaware of the mathematical predecessor.

Embeddings are not restricted to one modality of data. Any input can now be embedded.
Furthermore embedding not only happens in the first layers of the network, but can also happen in the penultimate layer.
Remember also that Embeddings don’t have to translate a high-dimensional vector into a low-dimensional vector, since it is a matter of REPRESENTATION and NOT OF DIMENSIONALITY, it happens that you go from a dimension of 7 to 4 or from 12 to 9 but it's not a significatively reduction.

Putting most of that together, embeddings may or may not be directly comparable with distance metrics, they may or may not compress dimensionality, they may come from any layer in a neural network or plausibly from other model types, they might refer to single inputs or to a combination of all features

So what is an embedding?
Google says that "An embedding is a relatively low-dimensional space into which you can translate high-dimensional vectors" but, as said before, it's not just a dimensionality reduction.
A more general definition states that "Embeddings are learned transformations to make data more useful"
it is fundamentally important that embeddings are learned by the model. The algorithm discovers useful relationships between data by learning how to define its embedding space.

In the end we can say that Embeddings usually refer to a space that retains much of the information from the source data, compressing information into fewer dimensions and transforming it to make it more useful for downstream layers or tasks. So there is a transformation but without much information loss.


CONTEXT WINDOW FOR LLMs

The context window of LLMs is the number of tokens the model can take as input when generating responses.
Larger context window sizes increase the ability to perform in-context learning in prompts. That is, you can provide more examples and/or larger examples as prompt inputs, enabling the LLM to give you a better answer. For example, a LLM could take an entire document as input, helping with comprehension of the full scope of an article.

Another example would be providing the LLM with context information that was not available at the time the LLM was trained, for example I can provide documents, when writing the prompt, that enlarge the knowledge base of the model (that is stopped up to the date when the training ended.

Of course, having an higher context window size is much expensive for the model (and those costs don't increase linearly but exponentially)
#AI 
[[Vector Databases]]