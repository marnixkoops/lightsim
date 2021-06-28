# ⚡ hyperscale
**Fast recommendations and vector similarity search**

## 👋 Hello

`hyperscale` solves the main bottleneck in recommender systems; querying items fast at scale.

When the number of items is large, scoring and ranking all combinations is computationally expensive. `hyperscale` implements fast Approximate Nearest Neighbors (ANN) algorithms in high-dimensional space for vector similarity search and maximum inner-product search (recommendation). This method is computationally efficient and able to produce microsecond response-times across millions of items.

Vector search can be effectively applied to increase customer experience through personalization of content, product upsell, cross-sell and other use-cases.
Moreover, vector models are also widely used in NLP, computer vision and other fields. `hyperscale` can be used in combination with any (embedding) vector-based model. For example, to quickly find the most similar embeddings learned by a Neural Network like `word2vec` or to generate recommendations for users based on any type of Collaborative Filtering model like Matrix Factorization.


## ✨ Install

```console
$ pip3 install hyperscale
```

## 🚀 Quickstart

`hyperscale` offers a simple Python API. While leveraging C++ under the hood, it avoids engineering complexity to get up and running.

```python
from hyperscale import hyperscale
import numpy as np

item_vectors = np.random.rand(int(1e6), 32)
user_vectors = np.random.rand(int(1e4), 32)

recommendations = hyperscale.recommend(user_vectors, item_vectors)

vector_index = hyperscale.build_vector_index(item_vectors)
most_similar = hyperscale.find_most_similar(vector_index)
```

## 🪄 Examples

It is easy to scale up personalized recommendations or vector similarity search after training a model with any popular library. Simply extract the embedding vectors and feed them to `hyperscale`. Some examples are shown below.

###### [`sklearn`](https://github.com/scikit-learn/scikit-learn)
<details><summary><b>show code</b></summary>

```python
from hyperscale import hyperscale
from sklearn.decomposition import NMF

matrix = np.random.rand(1000, 1000)

model = NMF(n_components=16)
model.fit(matrix)

user_vectors = model.transform(matrix)
item_vectors = model.components_.T

recommendations = hyperscale.recommend(user_vectors, item_vectors)
vector_index = hyperscale.build_vector_index(item_vectors)
most_similar = hyperscale.find_most_similar(vector_index)
```

</details>

###### [`surprise`](https://github.com/NicolasHug/Surprise)
<details><summary><b>show code</b></summary>

```python
from hyperscale import hyperscale
from surprise import SVD, Dataset

data = Dataset.load_builtin("ml-100k")
data = data.build_full_trainset()

model = SVD(n_factors=16)
model.fit(data)

user_vectors = model.pu
item_vectors = model.qi

recommendations = hyperscale.recommend(user_vectors, item_vectors)
vector_index = hyperscale.build_vector_index(item_vectors)
most_similar = hyperscale.find_most_similar(vector_index)
```

</details>

###### [`lightfm`](https://github.com/lyst/lightfm)
<details><summary><b>show code</b></summary>

```python
from hyperscale import hyperscale
from lightfm import LightFM
from lightfm.datasets import fetch_movielens

data = fetch_movielens(min_rating=5.0)

model = LightFM(loss="warp")
model.fit(data["train"])

_, user_vectors = model.get_user_representations(features=None)
_, item_vectors = model.get_item_representations(features=None)

recommendations = hyperscale.recommend(user_vectors, item_vectors)
vector_index = hyperscale.build_vector_index(item_vectors)
most_similar = hyperscale.find_most_similar(vector_index)
```

</details>

###### [`implicit`](https://github.com/benfred/implicit)
<details><summary><b>show code</b></summary>

```python
from hyperscale import hyperscale
from implicit.als import AlternatingLeastSquares
from scipy import sparse

matrix = np.random.rand(1000, 1000)
sparse_matrix = sparse.csr_matrix(matrix)

model = AlternatingLeastSquares(factors=16)
model.fit(sparse_matrix)

user_vectors = model.user_factors
item_vectors = model.item_factors

recommendations = hyperscale.recommend(user_vectors, item_vectors)
vector_index = hyperscale.build_vector_index(item_vectors)
most_similar = hyperscale.find_most_similar(vector_index)
```

</details>

## 🪄 Algorithm

Using random projections and by building up a tree. At every intermediate node in the tree, a random hyperplane is chosen, which divides the space into two subspaces.

We do this k times so that we get a forest of trees. k has to be tuned to your need, by looking at what tradeoff you have between precision and performance. In practice k should probably be on the order of dimensionality.

## 🖇️ References

* Bachrach, Yoram, et al. ["Speeding up the Xbox Recommender System using a Euclidean transformation for inner-product spaces."](https://www.microsoft.com/en-us/research/wp-content/uploads/2016/02/XboxInnerProduct.pdf) *Proceedings of the 8th ACM Conference on Recommender systems. 2014.*

* Bern, Erik. [Annoy (Approximate Nearest Neighbors Oh Yeah)](https://github.com/spotify/annoy) *Spotify. 2015.*
