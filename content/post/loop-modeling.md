+++
date = "2017-09-19T04:07:00"
draft = false
tags = ["paper note", "loop modeling"]
title = "Paper note - Loop Modeling"
math = true
summary = """ """

[header]
image = ""
caption = ""

+++

{{% toc %}}

This article is kind of a literature review of loop modeling but not complete. 

### Paper 1: [2000] - Modeling of loops in protein structures
---

First (I think) paper about loop modeling is: Modeling of loops in protein structures ([link](https://www.ncbi.nlm.nih.gov/pmc/articles/PMC2144714/pdf/11045621.pdf)).

**Loop Definition:**

The regions outside helices and strands were defined as loops.

![](/img/blog/lm-1.png)
*Distribution of loop length. The bars are divided into two parts to indicate the fraction of predictions expected to be in the good and medium classes gray and in the bad class white.*

Thus, loops often determine the functional specificity of a given protein framework.

Segments of up to **9** residues sometimes have entirely unrelated conformations in different proteins. Thus, the conformation of a given segment is also influenced by the **core stem** regions that span the loop and by the structure of the rest of a protein that cradles the loop. The **stems** are defined as the main-chain atoms that precede and follow the loop, but are not part of it. They span the loop and are part of the core of the fold. 

**Methods:**

1. Ab initio

    The ab initio loop prediction is based on a conformation search or enumeration of conformations in a given environment, guided by a scoring or energy function. 

2. Database based

    The database approach to loop prediction consists of finding a segment of main chain that fits the two stem regions of a loop.

3. Combined


**Accuracy Criteria:**

Two kinds of superposition: **global** and **local**.

The $RMSD _ {local}$ was picked as the primary measure of loop accuracy. $RMSD _ {local}$ for Ca atoms is almost the same as that for all the main-chain atoms. The $RMSD _ {local}$ for N, Ca, and C is always slightly smaller than that for all the main-chain atoms. 

A good prediction has $RMSD _ {local}$ smaller than 1 Å, a bad prediction has $RMSD _ {local}$ larger than 2 Å, and a medium prediction falls between these two extremes. 

### Paper 2: [2006] - A fold-recognition approach to loop modeling
---

In this paper, the author said there are two definitions of loop. 

1. Loops are segments that do not correspond to helix or strand.
2. Loops are regions that typically occurring between secondary-structure elements, where there are insertions and deletions in the target sequence relative to that of the templates. Simply speaking, it is the gap in the templates.

This paper is focus on the second definition. The basic approach is to first take out the gap region plus the stem/anchor regions on two sides from the target sequence, then find local alignments of this sequence segment as local template. Each gap has a local template selected by three criteria: 1) by solvation energy, 2) by alignment score, and 3) by the GenTHREADER neural-network score. The approach is shown in the graph below.

![](/img/blog/lm-2.png)


### Paper 3: [2014] - Protein Loop Modeling Using a New Hybrid Energy Function and Its Application to Modeling in Inaccurate Structural Environments
---

In many practical applications, however, the protein loops to be modeled are located in inaccurate structural environments. In this paper, a loop modeling method based on global optimization of this new energy function is tested on loop targets situated in different levels of environmental errors, ranging from experimental structures to structures perturbed in backbone as well as side chains and template-based model structures. 

Why do we need loop modeling? 

> However, due to large variations in loop sequences, homologous proteins often lack structural information in the loop region, thereby making template-based approaches difficult to apply.

Why in inaccurate environments?

> A number of ab initio loop modeling methods have been reported to show successful results in reconstructing loops in high-resolution crystal structures. However, loops of interest for practical applications are often situated in non-ideal conditions. Therefore, in the next era of loop modeling, achieving atomic-accuracy predictions in framework structures with errors will become a new challenge.

How to mimic inaccurate environments?

> In this study, ‘global’ structure is perturbed to mimic actual situations of loop modeling in globally inaccurate frameworks.

**Test Sets**

There are 4 test sets with different degrees of error in the framework structure. 

First, let's define Set 1 and Set 2:

* Set 1: **20 8-residue loops** from Jacobson et al. and **20 12-residue loops** from Zhu et al.
* Set 2: **33 12-residue loops** from Fiser et al.

The we can categorize the 4 tests sets as follows:

1. Set 1 + Set 2
2. Set 1, but with perturbed side chain structures for the residues surrounding the loops (downloaded from [here](http://www.jacobsonlab.org/decoy.htm))
3. Set 1, but the framework structures are perturbed in the overall structure, including the backbone. ([download link](http://galaxy.seoklab.org/suppl/ps2.html))
4. 23 loop targets in template-based models

**Results**

We will only focus on the 3rd and 4th test sets, i.e. the backbone-perturbed test set and template based test set. The backbone-perturbed test set includes 20 8-residue loops and 20 12-residue loops.

* Results on 8-res: [link](http://journals.plos.org/plosone/article/file?id=info%3Adoi/10.1371/journal.pone.0113811.s005&type=supplementary)
* Resutls on 12-res: [link](http://journals.plos.org/plosone/article/file?id=info%3Adoi/10.1371/journal.pone.0113811.s006&type=supplementary)
* Results on template based: [link](http://journals.plos.org/plosone/article/file?id=info%3Adoi/10.1371/journal.pone.0113811.s007&type=supplementary)

