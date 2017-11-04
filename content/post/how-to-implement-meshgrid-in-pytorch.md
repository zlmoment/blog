+++
date = "2017-10-10T05:07:00"
draft = false
tags = ["pytorch", "meshgrid"]
title = "How to Implement Meshgrid in PyTorch"
math = false
summary = """ """

[header]
image = ""
caption = ""

+++

In this post, I'll show how to implement meshgrid in PyTorch. 

The following graph shows what a meshgrid would be in numpy:

![](https://www.python-course.eu/images/creating_a_meshgrid.png)
*Image credit: https://www.python-course.eu/matplotlib_contour_plot.php*

If we have two tensors `x` and `y`:

```python
>>> x = torch.from_numpy(np.array([1, 2, 3, 4]))
>>> y = torch.from_numpy(np.array([11, 22, 33, 44]))
```

We'd like a tensor `z` with shape `[4, 4, 2]` in which `z[i, j]` is the concatenation of `x[i]` and `y[j]`.

```python
>>> xx = x.view(-1, 1).repeat(1, 4)
>>> xx

 1  1  1  1
 2  2  2  2
 3  3  3  3
 4  4  4  4
[torch.LongTensor of size 4x4]

>>> yy = y.repeat(4,1)
>>> yy

 11  22  33  44
 11  22  33  44
 11  22  33  44
 11  22  33  44
[torch.LongTensor of size 4x4]
```

Now we concatenate `xx` and `yy` by axis 2 after expanding a new dimension for `xx` and `yy`.

```python
>>> meshed = torch.cat([xx.unsqueeze_(2),yy.unsqueeze_(2)], 2)
>>> meshed.size()
torch.Size([4, 4, 2, 1])
```

Let's verify the results:

```python
>>> meshed[1,2]

  2
 33
[torch.LongTensor of size 2x1]

>>> x[1]
2
>>> y[2]
33
```

