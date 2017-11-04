+++
date = "2017-10-07T12:58:00"
draft = false
tags = ["data", "data analytics", "data prepocessing"]
title = "Data Preprocessing"
math = true
summary = """ """

[header]
image = ""
caption = ""

+++

{{% toc %}}

Some common ways to do data preprocessing.

### Standardization, or mean removal and variance scaling
---

*If your data contains many outliers, scaling using the mean and variance of the data is likely to not work very well.*

**Standardization** of datasets is **a common requirement** for many machine learning estimators; estimators might behave badly if the individual features is not in standard normally distributed data: i.e. Gaussian with **zero mean and unit variance**.

In practice we just remove the mean value of **each feature**, then scale it by dividing non-constant features by their **standard deviation**.

If a feature has a variance that is orders of magnitude larger than others, it might **dominate the objective function** and **make the estimator unable to learn** from other features correctly as expected.

```python
>>> from sklearn import preprocessing
>>> import numpy as np
>>> X_train = np.array([[ 1., -1.,  2.],
...                     [ 2.,  0.,  0.],
...                     [ 0.,  1., -1.]])
>>> X_scaled = preprocessing.scale(X_train)

>>> X_scaled                                          
array([[ 0.  ..., -1.22...,  1.33...],
       [ 1.22...,  0.  ..., -0.26...],
       [-1.22...,  1.22..., -1.06...]])

# The scaled data has zero mean and unit variance:

>>> X_scaled.mean(axis=0)
array([ 0.,  0.,  0.])

>>> X_scaled.std(axis=0)
array([ 1.,  1.,  1.])
```

We can use `StandardScaler` to do standardization on training set, then later apply it to the testing set.

```python
>>> scaler = preprocessing.StandardScaler().fit(X_train)
>>> scaler.transform(X_train)                           
array([[ 0.  ..., -1.22...,  1.33...],
       [ 1.22...,  0.  ..., -0.26...],
       [-1.22...,  1.22..., -1.06...]])

>>> X_test = [[-1., 1., 0.]]
>>> scaler.transform(X_test)                
array([[-2.44...,  1.22..., -0.26...]])
```

### Scaling features to a range
---

An alternative standardization is scaling features to lie between a given minimum and maximum value, often between 0 and 1.

```python
>>> X_train = np.array([[ 1., -1.,  2.],
...                     [ 2.,  0.,  0.],
...                     [ 0.,  1., -1.]])
...
>>> min_max_scaler = preprocessing.MinMaxScaler()
>>> X_train_minmax = min_max_scaler.fit_transform(X_train)
>>> X_train_minmax
array([[ 0.5       ,  0.        ,  1.        ],
       [ 1.        ,  0.5       ,  0.33333333],
       [ 0.        ,  1.        ,  0.        ]])
```

### Normalization
---

Normalization is to normalize samples individually to unit norm (**Each sample (i.e. each row of the data matrix) with at least one non zero component is rescaled independently of other samples so that its norm ($l_1$ or $l_2$) equals one.**). This process can be useful if you plan to use a quadratic form such as the dot-product or any other kernel to quantify the similarity of any pair of samples.

```python
>>> X = [[ 1., -1.,  2.],
...      [ 2.,  0.,  0.],
...      [ 0.,  1., -1.]]
>>> X_normalized = preprocessing.normalize(X, norm='l2')

>>> X_normalized                                      
array([[ 0.40..., -0.40...,  0.81...],
       [ 1.  ...,  0.  ...,  0.  ...],
       [ 0.  ...,  0.70..., -0.70...]])
```


### Binarization
---

Give a threshold, and get boolean values of each feature.

```python
>>> binarizer = preprocessing.Binarizer(threshold=1.1)
>>> binarizer.transform(X)
array([[ 0.,  0.,  1.],
       [ 1.,  0.,  0.],
       [ 0.,  0.,  0.]])
```


### Encoding categorical features to onehot
---

Infer categories from input automatically:

```python
>>> enc.fit([[0, 0, 3], [1, 1, 0], [0, 2, 1], [1, 0, 2]])  
OneHotEncoder(categorical_features='all', dtype=<... 'numpy.float64'>,
       handle_unknown='error', n_values='auto', sparse=True)
>>> enc.transform([[0, 1, 3]]).toarray()
array([[ 1.,  0.,  0.,  1.,  0.,  0.,  0.,  0.,  1.]])
```


If the training data has missing categorical features, one has to explicitly set `n_values`:

```python
>>> enc = preprocessing.OneHotEncoder(n_values=[2, 3, 4])
>>> # Note that there are missing categorical values for the 2nd and 3rd
>>> # features
>>> enc.fit([[1, 2, 3], [0, 2, 0]])  
OneHotEncoder(categorical_features='all', dtype=<... 'numpy.float64'>,
       handle_unknown='error', n_values=[2, 3, 4], sparse=True)
>>> enc.transform([[1, 0, 0]]).toarray()
array([[ 0.,  1.,  1.,  0.,  0.,  1.,  0.,  0.,  0.]])
```

### Label encoding
---

Encode labels with value between `0` and `n_classes-1`.

```python
>>> from sklearn import preprocessing
>>> le = preprocessing.LabelEncoder()
>>> le.fit([1, 2, 2, 6])
LabelEncoder()
>>> le.classes_
array([1, 2, 6])
>>> le.transform([1, 1, 2, 6]) 
array([0, 0, 1, 2]...)
# Transform labels back to original encoding.
>>> le.inverse_transform([0, 0, 1, 2])
array([1, 1, 2, 6])
```

It can also be used to transform non-numerical labels:

```python
>>> le = preprocessing.LabelEncoder()
>>> le.fit(["paris", "paris", "tokyo", "amsterdam"])
LabelEncoder()
>>> list(le.classes_)
['amsterdam', 'paris', 'tokyo']
>>> le.transform(["tokyo", "tokyo", "paris"]) 
array([2, 2, 1]...)
>>> list(le.inverse_transform([2, 2, 1]))
['tokyo', 'tokyo', 'paris']
```


### Reference
---

1. http://scikit-learn.org/stable/modules/preprocessing.html
2. http://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.LabelEncoder.html