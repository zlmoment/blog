+++
date = "2017-09-24T02:51:00"
draft = false
tags = ["hhsearch", "hhsuite"]
title = "Some Notes about HHSuite"
math = false
summary = """ """

[header]
image = ""
caption = ""

+++

{{% toc %}}

The following summary is mainly from the HHsuite official user guide. For complete guide please refer to [link](https://github.com/soedinglab/hh-suite).

### Summary
---

**HHsearch** takes as input a multiple sequence alignment (MSA) or profile HMM and searches a database of HMMs (e.g. PDB, Pfam, or InterPro) for homologous proteins. **HHblits** can build highquality MSAs starting from single sequences or from MSAs. Compared to PSIBLAST, HHblits is faster, up to twice as sensitive and produces more accurate alignments.

### HMMs
---

HMMs: profile hidden Markov models. Profile HMMs are a concise representation of multiple sequence alignments (MSAs).

A profile HMM is much better suited than a single sequence to find homologous sequences and calculate accurate alignments. By representing both the query sequence and the database sequences by profile HMMs, HHsearch and HHblits are more sensitive for detecting and aligning remotely homologous proteins than methods based on pairwise sequence comparison or profile-sequence comparison. 

Hblits can build high-quality multiple sequence alignments (MSAs) starting from a single sequence or from an MSA.

HHsearch takes as input an MSA (e.g. built by HHblits) or a profile HMM and searches a database of HMMs for homologous proteins.

### HHSuite Database
---

The following HHsuite databases, which can be searched by HHblits and HHsearch, can be downloaded at [here](http://wwwuser.gwdg.de/~compbiol/data/hhsuite/databases/hhsuite_dbs/):

```text
1 uniprot20 based on UniProt db from EBI/SIB/PIR, clustered to 20 % seq. identity
3 pdb70 representatives from PDB (70% max. sequence identity), updated weekly
4 scop70 representatives from SCOP (70% max. sequence identity)
5 pfamA Pfam A database from Sanger Inst., http://www.sanger.ac.uk/Software/Pfam/
```

Note that, in order to generate multiple sequence alignments (MSAs) **by iterative sequence searching** using **HHblits**, you need to search the uniprot20, since since only this databases cover essentially all of the sequence universe. The pdb70, pfamA, and pdb70 are not appropriate to build MSAs by iterative searches.

In the pdb70 and scop70 databases, each master sequence from the original sequence database is represented by an MSA. The MSAs are built by HHblits searches starting with the master sequence as a query. As the sequences in this type of database do not cover the entire sequence space, they are not suited for iterative searches. 


### Programs
---

```text
hhblits        (Iteratively) search an HHsuite database with a query sequence or MSA
hhsearch       Search an HHsuite database with a query MSA or HMM
hhmake         Build an HMM from an input MSA
hhfilter       Filter an MSA by max sequence identity, coverage, and other criteria
hhalign        Calculate pairwise alignments etc. for two HMMs/MSAs
hhconsensus    Calculate the consensus sequence for an A3M/FASTA input file

reformat.pl    Reformat one or many MSAs
addss.pl       Add PSIPRED predicted secondary structure to an MSA or HHM file
hhmakemodel.pl Generate MSAs or coarse 3D models from HHsearch or HHblits results
hhmakemodel.py Generates coarse 3D models from HHsearch or HHblits results and modifies cif files such that they are compatible with MODELLER
hhsuitedb.py   Build HHsuite database with prefiltering, packed MSA/HMM, and index files
splitfasta.pl  Split a multiple-sequence FASTA file into multiple single-sequence files
renumberpdb.pl Generate PDB file with indices renumbered to match input sequence indices 
Align.pm       Utility package for local and global sequence-sequence alignment
HHPaths.pm     Configuration file with paths to the PDB, BLAST, PSIPRED etc.
mergeali.pl    Merge MSAs in A3M format according to an MSA of their seed sequences
pdb2fasta.pl   Generate FASTA sequence file from SEQRES records of globbed pdb files
cif2fasta.py   Generate a FASTA sequence from the pdbx_seq_one_letter_code entry of the entity_poly of globbed cif files
pdbfilter.pl   Generate representative set of PDB/SCOP sequences from pdb2fasta.pl output
pdbfilter.py   Generate representative set of PDB/SCOP sequences from cif2fasta.py output
```

### Usage
---

If the input file is an MSA or a single sequence, HHsearch calculates an HMM from it and then aligns this query HMM to all HMMs in the database using the Viterbi algorithm. The HHblits tool can be used in much the same way as HHsearch. It takes the same input data and produces a results file in the same format as HHsearch. Due to its fast prefilter, HHblits runs between 30 and 3000 times faster than HHsearch at the cost of only a few percent lower sensitivity.

**Generating a multiple sequence alignment using HHblits**

To generate an MSA for a sequence or initial MSA in query.a3m, the database to be searched should cover the entire sequence space, such as uniprot20 or nr20. The option `-oa3m <msa_file>` tells HHblits to generate an output MSA from the significant hits, `-n 1` specifies a single search.

```
$ hhblits -cpu 4 -i data/query.seq -d dbs/uniprot20 -oa3m query.a3m -n 1
```

After the search, query.a3m will contain the MSA in A3M format.

We could do a second search iteration, starting with the MSA from the previous search, to add more sequences. Since the MSA generated after the previous search contains more information than the single sequence in query.seq, searching with this MSA will probably result in many more homologous database matches.

```
$ hhblits -cpu 4 -i query.a3m -d dbs/uniprot20 -oa3m query.a3m -n 
```

Instead, we could directly perform two search iterations starting from query.seq:

```
$ hhblits -cpu 4 -i data/query.seq -d dbs/uniprot20 -oa3m query.a3m -n 2
```

In practice, it is recommended to use between **1 and 4** iterations for building MSAs, depending on the trade-off between reliability and specificity on one side and sensitivity for remotely homologous sequences on the other side. The more search iterations are done, the higher will be the risk of non-homologous sequences or sequence segments entering the MSA and recruiting more of their kind in subsequent iterations.

### What is HMM-HMM comparison and why is it so powerful?
---

When searching for remote homologs, it is wise to make use of as much information about the query and database proteins as possible in order to better distinguish true from false positives and to produce optimal alignments. This is the reason why sequence-sequence comparison is inferior to profile-sequence comparison.

Profile Hidden Markov Models (HMMs) are similar to simple sequence profiles, but in addition to the amino acid frequencies in the columns of a multiple sequence alignment they contain information about the frequency of inserts and deletions at each column.

### When can the HHsuite be useful for me?
---

In cases where conventional sequence search methods fail, HHblits and HHsearch quite often allow to make inferences from more remotely homologous relationships. 

### How can I verify if a database match is homologous?
---

**Check probability and E-value:**

Sequence identity is therefore **not** an appropriate measure of relatedness anymore. The **estimated probability of the template** to be (at least partly) homologous to your query sequence is the most important criterion to decide whether a template HMM is actually homologous or just a high-scoring chance hit.

The **E-value** is an alternative measure of statistical significance. It tells you how many chance hits with a score better than this would be expected if the database contained only hits unrelated to the query. At E-values below one, matches start to get marginally significant. 

The E-value gives the average number of false positives (’wrong hits’) with a score better than the one for the template when scanning the database. It is a measure of reliability: Evalues near to 0 signify a very reliable hit. An E-value of 10 means about 10 wrong hits are expected to be found in the database with a score at least this good.

The **probability** is a more sensitive measure than the **E-value**.

**Others:**

See user guide for details:

* Check if homology is biologically suggestive or at least reasonable
* Check secondary structure similarity
* Check relationship among top hits
* Check for possible conserved motifs
* Check residues and role of conserved motifs
* Check query and template alignments
* Realign with other parameters
* Build the query and/or database MSAs more aggressively
* Try out other tools
* Verify predictions experimentally

### Should I use the `-global` option to build MSAs
---

**Never use -global when building MSAs by iterative searches with HHblits**. Remember that global alignments are very greedy alignments, where alignments stretch to the ends of either query of database HMM, no matter what. Global alignments will therefore often lead to non-homologous segments getting included in the MSA, which is catastrophic, as these false segments will lead to many false positive matches when searching with this MSA. But you may use -global when searching (with a single iteration) for global matches of your query HMMs through your customized HHsuite database, for example.

For what is the local and global alignments, see [here](https://biology.stackexchange.com/questions/11263/what-is-the-difference-between-local-and-global-sequence-alignments).

### Why do I get different results using hhblits and hhsearch 
---

There are two reasons. First, some hits that hhsearch shows might not have passed the prefilter in hhblits.

Second, while the probabilities and P-values of hhblits and hhsearch should be the identical for the same matches, the E-values are only similar. The reason is that hhblits heuristically combines the E-values of the second prefilter with the E-values from the full HMM-HMM Viterbi alignment into total E-values. These E-values are slightly better in distinguishing true from false hits, because they combine the partly independent information from two comparisons.
