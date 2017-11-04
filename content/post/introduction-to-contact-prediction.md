+++
date = "2017-10-05T15:51:00"
draft = false
tags = ["contact prediction", "bioinformatics"]
title = "Introduction to Contact Prediction"
math = true
summary = """ """

[header]
image = ""
caption = ""

+++

{{% toc %}}

Most of the following materials come from article [link](https://link.springer.com/protocol/10.1007%2F978-1-60327-429-6_3).

### Introduction
---

Analysis of a protein’s evolutionary history may seem irrelevant to protein structure prediction. Indeed, a protein’s folded structure depends entirely on the laws of physics and is dictated by the protein’s amino acid sequence and its environment.

### Sequence-Based Approaches to Contact Prediction
---

At the most fine-grained level, an MSA is constructed for a given protein and the information in pairs of columns is used to predict which residues are in contact. 

The fundamental assumption is that for residues that are in contact, the corresponding columns will be in some way more highly related to each other than for residues that are not.

![](/img/blog/contact1.png)
*From protein sequence to contact prediction. A typical workflow taking a protein’s sequence, extracting sequence/amino acid properties, encoding the information, applying a learning algorithm and finally making contact pair predictions to be used for structure prediction.*


### Contact Filtering
---

To remove those predicted contacts that are in some way physically unrealizable. The simplest and perhaps most effective method of filtering is contact occupancy. 

### Evaluating Contact Predictors
---

There are many definitions of residue contact used in the literature. Some use the $C\alpha$ distance, that is, the distance between the $\alpha$ carbon atoms of the residue pair, whereas others prefer the $C\beta$ distance.

The most common minimum separation used to define a contact pair is 8Å. It is also usual to **exclude** residue pairs that are separated along the amino acid sequence by less than some fixed number of residues, since short-range contacts are less interesting and easier to predict than long-range ones.

For a given target protein, the **prediction accuracy** $A_N$ on $N$ predicted contacts is defined to be $$A_N = N_c/N$$ where $N_c$ is the number of the predicted contacts that are indeed contacts for a given minimum sequence separation. Typically $N$ is taken be one of $L$, $L/2$, $L/5$, or $L/10$, where $L$ is the length of the sequence.

For most proteins, the actual number of contacts (using the 8Å definition) is in the range $L$ to $2L$. It has become relatively standard to report results on the best $L/2$ predictions with a maximum distance of 8Å between $C\beta$ atoms ($C\alpha$ for glycine), with a minimum sequence separation of 6.

The **prediction coverage** is defined to be $N_c/T_c$, where $T_c$ is the total number of contacts pairs for the protein. 
