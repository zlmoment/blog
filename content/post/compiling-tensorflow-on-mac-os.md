+++
date = "2017-05-12T12:00:00"
draft = false
tags = ["tensorflow", "deep learning", "mac os"]
title = "Compiling Tensorflow on Mac OS"
math = false
summary = """ """

[header]
image = ""
caption = ""

+++

{{% toc %}}


In the release note of tensorflow v1.1.0, there is a section about Mac GPU support: 

> TensorFlow 1.1.0 will be the last time we release a binary with Mac GPU support. Going forward, we will stop testing on Mac GPU systems. We continue to welcome patches that maintain Mac GPU support, and we will try to keep the Mac GPU build working.

In this case, I tried to compile GPU on Mac OS. After lots of errors, here is the finalized compiling process. The main steps are from tensorflow official tutorial: https://www.tensorflow.org/install/install_sources.

While, first of all, install Cuda and cuDNN. In this case, I use Cuda 8.0 and cuDNN 6.0.

### My Environment Configuration
---

Here is the configuration in my .zprofile file (since I use oh-my-zsh):

```
export CUDA_HOME=/usr/local/cuda
export DYLD_LIBRARY_PATH="$CUDA_HOME/lib:$CUDA_HOME:$CUDA_HOME/extras/CUPTI/lib"
export LD_LIBRARY_PATH=$DYLD_LIBRARY_PATH

# Setting PATH for Python 3.5
# The original version is saved in .zprofile.pysave
PATH="/Library/Frameworks/Python.framework/Versions/3.5/bin:${PATH}"
export PATH
```

### Install Command Line Tool 7.3
---

Source: [here](https://github.com/arrayfire/arrayfire/issues/1384). CLT 8.0 or newer version doesn’t work. You’ll see the following error message:

> nvcc fatal : The version (‘80000’) of the host compiler (‘Apple clang’) is not supported

Go to [Apple Developer](https://developer.apple.com/) and download the following CLT 7.3. Install it and run:

```
sudo xcode-select --switch /Library/Developer/CommandLineTools
```

Then verify the clang version by running `clang --version`.

### Clone the TensorFlow repository
---

Run the following commands to use branch r1.1:

```
git clone https://github.com/tensorflow/tensorflow
cd tensorflow
git checkout r1.1
```

### Install Bazel
---

Install JDK 8 from this page and Homebrew from this page, use the following command to install Bazel:

```
brew install bazel
```

### Install Python dependencies
---

I use Python3 for this case.

```
sudo pip3 install six numpy wheel
```

### Install Tensorflow for GPU prerequisites
---

```
brew install coreutils
```

### Configure the installation
---

Run `./configure` in tensorflow folder. The following output is my configuration for your reference.

```
Please specify the location of python. [Default is /usr/bin/python]:/usr/local/bin/python3
/ Please specify optimization flags to use during compilation when bazel option "--config=opt" is specified [Default is -march=native]: 
Do you wish to build TensorFlow with Google Cloud Platform support? [y/N] 
No Google Cloud Platform support will be enabled for TensorFlow
Do you wish to build TensorFlow with Hadoop File System support? [y/N] 
No Hadoop File System support will be enabled for TensorFlow
Do you wish to build TensorFlow with the XLA just-in-time compiler (experimental)? [y/N] 
No XLA support will be enabled for TensorFlow
Found possible Python library paths:
  /Library/Frameworks/Python.framework/Versions/3.5/lib/python3.5/site-packages
Please input the desired Python library path to use.  Default is [/Library/Frameworks/Python.framework/Versions/3.5/lib/python3.5/site-packages]
 
Using python library path: /Library/Frameworks/Python.framework/Versions/3.5/lib/python3.5/site-packages
Do you wish to build TensorFlow with OpenCL support? [y/N] 
No OpenCL support will be enabled for TensorFlow
Do you wish to build TensorFlow with CUDA support? [y/N] Y
CUDA support will be enabled for TensorFlow
Please specify which gcc should be used by nvcc as the host compiler. [Default is /usr/bin/gcc]: 
Please specify the CUDA SDK version you want to use, e.g. 7.0. [Leave empty to use system default]: 8.0
Please specify the location where CUDA 8.0 toolkit is installed. Refer to README.md for more details. [Default is /usr/local/cuda]: 
Please specify the Cudnn version you want to use. [Leave empty to use system default]: 6
Please specify the location where cuDNN 6 library is installed. Refer to README.md for more details. [Default is /usr/local/cuda]: 
Please specify a list of comma-separated Cuda compute capabilities you want to build with.
You can find the compute capability of your device at: https://developer.nvidia.com/cuda-gpus.
Please note that each additional compute capability significantly increases your build time and binary size.
[Default is: "3.5,5.2"]: 5.2,6.1
 
INFO: Starting clean (this may take a while). Consider using --expunge_async if the clean takes more than several minutes.
........................................................
INFO: All external dependencies fetched successfully.
Configuration finished
```

### Build the pip package
---

In this step, I had the following error for two days and finally found the solution in this post. The error message is from that post but mine is almost the same.

```
dyld: Library not loaded: @rpath/libcudart.8.0.dylib
  Referenced from: /private/var/tmp/_bazel_zarzen/bdf1cb43f3ff02468b610730bd03f348/execroot/tensorflow/bazel-out/host/bin/tensorflow/python/gen_array_ops_py_wrappers_cc
  Reason: image not found
/bin/bash: line 1: 92702 Trace/BPT trap: 5       
bazel-out/host/bin/tensorflow/python/gen_array_ops_py_wrappers_cc @tensorflow/python/ops/hidden_ops.txt 1 > bazel-out/local_darwin-opt/genfiles/tensorflow/python/ops/gen_array_ops.py
Target //tensorflow/tools/pip_package:build_pip_package failed to build
```

Some people said we need to disable SIP, and it turns out we don’t have to. Turning off SIP will put our system on risk of malware. The solution is as followed (from the post I mentioned above).

Find the file `genrule-setup.sh` in  `<tensorflow source dir>/bazel-tensorflow/external/bazel_tools/tools/genrule/`.

If the timestamp of this file changes then bazel build will fail saying the file is corrupted. So before modifying this file make a note of the timestamp.

```
stat genrule-setup.sh
```

You should get an output like this:

```
16777220 25929227 -rwxr-xr-x 1 user wheel 0 242 "Oct 12 23:46:28 2016" "Oct 10 21:49:39 2026" "Oct 12 21:49:39 2016" "Oct 12 21:49:38 2016" 4096 8 0 genrule-setup.sh
```

Note down the second timestamp “Oct 10 21:49:39 2026” from the above output

edit the genrule-setup.sh file and add the environment configuration to the end of the file

```
export DYLD_LIBRARY_PATH=/usr/local/cuda/lib
```

Then change the timestamp to the original timestamp

```
touch -t YYYYMMDDhhmm.SS genrule-setup.sh
```

for e.g.

```
touch -t 202610102149.39 genrule-setup.sh
```

Finally, create a symbolic link to avoid 1Segmentation fault: 111 error

```
ln -sf /usr/local/cuda/lib/libcuda.dylib /usr/local/cuda/lib/libcuda.1.dylib
```

Now restart the build

```
bazel build -c opt --config=cuda //tensorflow/tools/pip_package:build_pip_package
```

Everything works fine. The compiling process took me exactly 23 mins.

### Generate wheel file and install it
---

```
bazel-bin/tensorflow/tools/pip_package/build_pip_package /tmp/tensorflow_pkg
sudo pip3 install /tmp/tensorflow_pkg/tensorflow-*.whl
```

Everything is completed!

