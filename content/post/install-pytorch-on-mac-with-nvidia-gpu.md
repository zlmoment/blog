+++
date = "2017-08-20T12:00:00"
draft = false
tags = ["pytorch", "deep learning"]
title = "Install PyTorch on Mac with Nvidia GPU"
math = false
summary = """ """

[header]
image = ""
caption = ""

+++

{{% toc %}}

Since PyTorch doesn’t provide binary package for Mac OS with GPU support, I have to compile the PyTorch from source. In this post, I will keep a note of what I did in the whole process. The whole process is based on the official tutorial: https://github.com/pytorch/pytorch#from-source. This post will assume you have already install CUDA 8.0, and cuDNN v6.0 (April 27, 2017) for CUDA 8.0 correctly.

## Download Xcode
---

Download the newest version of Xcode from App Store. The version I’m using is 8.3.2.

![](/img/blog/xcode1.png)

### Update and Upgrade Homebrew
---

Type the following command to update Homebrew package index and upgrade your exsiting packages.

```
brew update
brew upgrade
```

### Install Conda Environment
---

Anaconda environment is highly recommended by official tutorial since we will get a high-quality BLAS library (MKL) and you get a controlled compiler version regardless of your Linux distro.

Here I will install [Miniconda](https://conda.io/docs/user-guide/install/index.html), a mini version of Anaconda that includes only conda and its dependencies. If you prefer to have conda plus over 720 open source packages, install Anaconda.

```
brew cask search conda
brew cask install miniconda
```

Then add the conda path to your system variable $PATH, and use  source command to make it work.

```
export PATH=/usr/local/miniconda3/bin:"$PATH"
```

Now check your Python, you will find your Python is now the conda one with version 3.6:

![](/img/blog/python_output.png)

### Install Extra Python Packages
---

There are some Python packages that would be useful. You can install them using the following command:

```
conda install matplotlib ipython jupyter
conda install numpy pyyaml setuptools cmake cffi
```

### Install whatever useful for you or for your project.
---

Compile and Install From PyTorch Source

Use the following command to check out the source repo of PyTorch:

```
git clone https://github.com/pytorch/pytorch.git
```

Now check your Clang version:

![](/img/blog/clangv1.png)

My Clang version is 8.1.0, however the current CUDA is not compatible with the Command Line Tool in this version of Clang. We would use the Command Line Tool from Xcode 8.2 instead. Go to [Apple Developer download website](https://developer.apple.com/download/more/), download the following tool:

![](/img/blog/cmd8.2.png)

After install it, now use the following command to switch the current Command Line Tool:

```
sudo xcode-select --switch /Library/Developer/CommandLineTools
```

Now type `clang --version` you would see the Clang version becomes 8.0.0:

```
➜  ~ clang --version
Apple LLVM version 8.0.0 (clang-800.0.42.1)
Target: x86_64-apple-darwin16.7.0
Thread model: posix
InstalledDir: /Library/Developer/CommandLineTools/usr/bin
```

Type the following command for CMAKE configuration:

```
export CMAKE_PREFIX_PATH=/usr/local/miniconda3/
```

Replace the `/usr/local/miniconda3/` to your own conda root directory.

Finally type the following command to compile and install PyTorch (it will take some time):

```
cd ~/pytorch
MACOSX_DEPLOYMENT_TARGET=10.9 CC=clang CXX=clang++ python setup.py install
```

### Verify Your Installation
---

Use the following command or run your own PyTorch program to verify your installation is correct.

Note: you can NOT run Python in the PyTorch source folder since there is a folder called torch in the same directory, which will confuse the Python which to import. See this [issue](https://github.com/pytorch/pytorch/issues/574) for details.

![](/img/blog/pytorch_succ.png)

Enjoy!