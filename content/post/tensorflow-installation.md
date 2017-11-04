+++
date = "2016-10-08T12:00:00"
draft = false
tags = ["tensorflow", "deep learning"]
title = "TensorFlow Installation"
math = false
summary = """ """

[header]
image = ""
caption = ""

+++

{{% toc %}}

![](/img/blog/tf.jpg)

We are going to install TensorFlow on Ubuntu 14.04 x64.


### Install CUDA Toolkit 7.5
---

Download CUDA Toolkit 7.5 from the following address: https://developer.nvidia.com/cuda-75-downloads-archive. If your Ubuntu is 64 bit, choose target installer for Linux Ubuntu 14.04 x86_64, which is named `cuda-repo-ubuntu1404-7-5-local_7.5-18_amd64.deb`.

### Installation Instructions:
---

After you downloading the installation file, do the following command:

```bash
sudo dpkg -i cuda-repo-ubuntu1404-7-5-local_7.5-18_amd64.deb
sudo apt-get update
sudo apt-get install cuda
```
Use `source .bashrc` to make the changes effective.

### Install cuDNN
---

Go to this page https://developer.nvidia.com/cudnn to download cuDNN after registration. The version in this tutorial is cuDNN v5.1 for CUDA 7.5.

Uncompress the file using:

```
tar xzvf cudnn-7.5-linux-x64-v5.1.tgz
```

Copy the cuDNN files into the toolkit directory:

```
sudo cp cuda/include/cudnn.h /usr/local/cuda/include
sudo cp cuda/lib64/libcudnn* /usr/local/cuda/lib64
sudo chmod a+r /usr/local/cuda/include/cudnn.h /usr/local/cuda/lib64/libcudnn*
```

Now let’s verify your CUDA is installed successfully by compiling one of the samples deviceQuery:

```
cp -r /usr/local/cuda/samples ~/cuda-samples
pushd ~/cuda-samples
make
popd
~/cuda-samples/bin/x86_64/linux/release/deviceQuery
```

If you can see the correct output then everything is OK.

In the next, we are going to use Virtualenv to install TensorFlow. Virtualenv is a tool to keep the dependencies required by different Python projects in separate places, which makes it easier to manage your Python projects.

### Install pip and Virtualenv:
---

Use the following command to install pip and virtualenv:

```
sudo apt-get install python-pip python-dev python-virtualenv
```

Create a Virtualenv environment in the directory `~/tensorflow`:

```
virtualenv --system-site-packages ~/tensorflow
```

Activate the environment:

```
source ~/tensorflow/bin/activate
```

Select the correct version of TensorFlow (Ubuntu 64 bit with GPU enabled):

```
(tensorflow)$ export TF_BINARY_URL=https://storage.googleapis.com/tensorflow/linux/gpu/tensorflow-0.11.0rc0-cp27-none-linux_x86_64.whl
(tensorflow)$ pip install --upgrade $TF_BINARY_URL
```

### Test the Installation
---

Open a Python prompt and type the following:

```python
...
>>> import tensorflow as tf
>>> hello = tf.constant('Hello, TensorFlow!')
>>> sess = tf.Session()
>>> print(sess.run(hello))
Hello, TensorFlow!
>>> a = tf.constant(10)
>>> b = tf.constant(32)
>>> print(sess.run(a + b))
42
>>>
```

There are maybe some output information regarding to your GPU card on the screen when you execute the Python code, which is totally fine as long as the output information is not error output. 