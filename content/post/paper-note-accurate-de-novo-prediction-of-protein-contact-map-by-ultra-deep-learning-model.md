+++
date = "2017-09-10T00:34:00"
draft = false
tags = ["paper note", "contact prediction"]
title = "Paper note - Accurate De Novo Prediction of Protein Contact Map by Ultra-Deep Learning Model"
math = true
summary = """ """

[header]
image = ""
caption = ""

+++

{{% toc %}}

For contact prediction, direct evolutionary coupling analysis (DCA). However, **DCA** is effective on only some proteins with a very large number of sequence homologs. Paper link is [here](http://journals.plos.org/ploscompbiol/article?id=10.1371/journal.pcbi.1005324).

### Contact definition
---

Two residues form a contact if they are spatially proximal in the native structure, i.e., the Euclidean distance of their $C_\beta$ atoms less than 8Å.

We define that a contact is short-, medium- and long-range when the sequence distance of the two residues in a contact falls into [6, 11], [12, 23], and [24,], respectively.

### Existing contact prediction methods
---

Existing contact prediction methods roughly belong to two categories: **evolutionary coupling analysis (ECA)** and **supervised machine learning**.

ECA predicts contacts by identifying co-evolved residues in a protein, such as EVfold, PSICOV, CCMpred, Gremlin, plmDCA. DCA usually needs a large number of sequence homologs. Supervised machine learning predicts contacts from a variety of information, e.g., SVMSEQ, CMAPpro, PconsC2, MetaPSICOV, PhyCMAP and CoinDCA-NN. Among them, PconsC2, CoinDCA-NN and MetaPSICOV are based on neural network. 

### Intuition
---

If we treat a protein contact map as an **image**, then protein contact prediction is similar to (but not exactly same as) pixel-level image labeling. There are some obvious differences:

* Contact map can't be resized
* Contact map has more input features
* Contact map output labels are extreamly unbalanced

### Input features
---

**1D network** (it learns the sequential context of a residue):

* Sequence profile (PSSM with dimension $L \times 20$)
* Predicted 3-state secondary structure ($L \times 3$)
* Predicted 3-state solvent accessibility ($L \times 3$)

**2D network** (it learns contact occurrence patterns or high-order residue correlation):

* Output from 1D network (The output of this 1D convolutional network is converted to a 2-dimensional (2D) matrix by outer concatenation (an operation similar to outer product))
* Direct co-evolutionary information (from CCMPred)
* Pairwise contact
* Distance potential

**Process:**

* For a **training** protein, we run PSI-BLAST (with E-value 0.001 and 3 iterations) to search the NR (non-redundant) database (October 2012) to find its sequence homologs, and then build its MSA, PSSM and predict other features (SS, SA).

* For a **testing** protein, we generate **4** different MSAs by running HHblits (with 3 iterations and E-value set to 0.001 and 1, respectively) to search through the **uniprot20 HMM** library released in November 2015 and February 2016. From each individual MSA, we derive one PSSM and predict the SS and SA accordingly. That is, for each test protein we generate 4 sets of input features and accordingly 4 different contact predictions. Then we average these 4 predictions to obtain the final contact prediction. This averaged contact prediction is about 1–2% better than that predicted from a single set of features.

**The impact of different input features:**

1. Co-evolution strength from CCMPred (accuracy ~0.15 worse)
2. The depth of our deep model (9 and 30 layers have top L/10 accuracy ~0.1 and ~0.03 worse than a 60-layer model)
3. The pairwise contact potential and mutual information (accuracy 0.02-0.03)
4. The secondary structure and solvent accessibility (accuracy 0.01-0.02)

### Network Structure
---

![](/img/blog/contact-1.PNG)

For the $2^{nd}$ network, for a residual block, $X _ l$ and $X _ {l+1}$ represent pairwise features with dimension $L \times L \times n _ l$ and $L \times L \times n _ {l+1}$. Typically, we enforce $n _ l ≤ n _ {l+1} $ since one position at a higher level is supposed to carry more information. If dimension doesn't match, pad zeros.

Filter size for 1D is $17$, for 2D is $3 \times 3$ or $5 \times 5$.

Batch normalization is before activation, mean 0, standard deviation 1.

Our experimental results show that with ~60 hidden neurons at each position and ~60 convolution layers for the $2^{nd}$ residual network, our model can yield pretty good performance. 

### Conversion of sequential features to pairwise features
---

let $ v = \\{ v_1, v_2, ..., v_i, ..., v_L \\} $ be the final output of the first network, for a pair of residues $i$ and $j$, we concatenate $v _ i$, $v _ {\frac{(i+j)}{2}}$ and $v _ j$ to a single vector and use it as one input feature of this residue pair.

### Loss function
---

Since the ratio of contacts among all the residue pairs is very small, to make the training algorithm converge fast, we assign a larger weight to the residue pairs forming a contact. The weight is assigned such that the total weight assigned to contacts is approximately 1/8 of the number of non-contacts in the training set.

### Regularization and optimization
---

To prevent overfitting, we employ $L_2-norm$ regularization. We use SGD algorithm to minimize the objective function. 

It takes 20–30 epochs to obtain a very good solution. The whole algorithm is implemented by Theano.

### Proteins of different lengths
---

We sort all training proteins by length and group proteins of similar lengths into minibatches. In the case that they do not, we add zero padding to shorter proteins.

We have tested minibatches with only a single protein or with several proteins. Both work well. However, it is much easier to implement minibatches with only a single protein.

### Data
---

Our training set is a subset of PDB25 created in February 2015. PDB25 list: [click me](http://dunbrack.fccc.edu/PISCES.php)

We exclude a protein from the training set if it satisfies one of the following conditions:

* sequence length smaller than 26 or larger than 700
* resolution worse than 2.5Å
* has domains made up of multiple protein chains
* no DSSP information
* there is inconsistency between its PDB, DSSP and ASTRAL sequences

To remove redundancy with the test sets, we exclude any training proteins sharing >25% sequence identity or having BLAST E-value <0.1 with any test proteins. 

In total there are 6767 proteins in our training set, from which we have trained 7 different models. For each model, we randomly sampled ~6000 proteins from the training set to train the model and used the remaining proteins to validate the model and determine the hyper-parameters (i.e., regularization factor). The final model is the average of these 7 models.

### Results
---

Here is the result on only the CASP11 test proteins. For more results, [click me](http://journals.plos.org/ploscompbiol/article?id=10.1371/journal.pcbi.1005324#sec006).

![](/img/blog/contact-2.PNG)

Note: compared to another paper using DCA only [link](https://papers.nips.cc/paper/6488-protein-contact-prediction-from-amino-acid-co-evolution-using-convolutional-networks-for-graph-valued-images.pdf).

![](/img/blog/contact-3.PNG)

Some other conculusions:

* When a query protein has no close templates, our contact-assisted modeling may work better than template-based modeling;
* Contact-assisted modeling shall be particularly useful for membrane proteins; 
* Our deep learning model does not predict contacts by simply copying contacts from the training proteins since our predicted contacts may result in much better 3D models than homology modeling built from the training proteins of our deep model.

### Discussion
---

* The latest PDB25 has more than 10,000 proteins, which can provide many more training proteins.
* We may feed our predicted contacts to Rosetta to build 3D models. Compared to CNS, Rosetta makes use of **energy function** and **more local structural restraints** through fragment assembly.
* Instead of predicting contacts, our deep learning model can predict inter-residue distance distribution (i.e., distance matrix), which provides finer-grained information than contact maps.

