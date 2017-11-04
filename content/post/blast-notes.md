+++
date = "2017-09-02T23:57:00"
draft = false
tags = ["blast", "bioinformatics"]
title = "Some Notes about BLAST+"
math = false
summary = """ """

[header]
image = ""
caption = ""

+++

{{% toc %}}


### Make BLAST Database
---

Basic command:

```
makeblastdb -in all_fasta.fasta -dbtype prot -parse_seqids -out mydb
```

Parameter:

- `in`: the sequences file
- `dbtype`: type of database
- `out`: output db name

### blastp
---

Compare protein sequence to protein database.

```
blastp -query seq.fasta -out seq.blast -db dbname -outfmt 6 -evalue 1e-5 -num_threads 8
```

Parameter:

- `query`: input sequence
- `out`: output file
- `db`: db name
- `outfmt`: output file format, details [here](https://www.ncbi.nlm.nih.gov/books/NBK279675/).
- `evalue`: evalue threshold
- `num_threads`: number of threads


### HSP
---

The High-scoring Segment Pair (HSP) is the fundamental unit of BLAST algorithm output. 

An HSP consists of two sequence fragments of arbitrary but equal length whose alignment is locally maximal and for which the alignment score meets or exceeds a threshold or cutoff score. A set of HSPs is thus defined by two sequences, a scoring system, and a cutoff score. 

In the programmatic implementations of the BLAST algorithm described here, each HSP consists of a segment from the query sequence (Query) and one from a database sequence (Subject).


### Output Explanation
---

In the `Alignments` section in the output file, here are some explanation of some variables. The details can be found [here](https://www-bimas.cit.nih.gov/blastinfo/blastexample.html). This link give detailed explanations to every item in the output file.

- **Score**: For the segment pair display, the score is the sum of the scoring matrix values in the segment pair being display
- **Bit**: The raw score is converted to bits of information by multiplying by lambda (see the Statistics output
- **Expect**: the number of times one might Expect to see such a match (or a better one) merely by chance
- **Identities**: The number and fraction of total residues in the HSP which are identic
- **Positives**: The number and fraction of residues for which the  alignment scores  have positive values.

**Between the Query and Subject:**

Between the rwo lines of Query and Subject (database) sequence is a line indicat-ing  the  specific residues which are identical, as well as those which are non-identical but nevertheless have positive alignment scores defined in the scoring matrix that was used (the BLOSUM62 matrix in this case). Identical letters or residues,  when paired with each other, are not highlighted if their alignment score is negative or zero. 


### Parse Result Using Python
---

We can use Biopython package to parse the BLAST output. See [link](https://github.com/bigwiv/Biopython-cn/blob/master/cn/chr07.rst) for details. Here is a summary code example:

First, make sure the output format is set to XML. Parameters for output format can be found [here](https://www.ncbi.nlm.nih.gov/books/NBK279675/).

```
from Bio.Blast import NCBIXML
result_handle = open("my_blast.xml")
blast_record = NCBIXML.read(result_handle)
for alignment in blast_record.alignments:
    for hsp in alignment.hsps:
        print('****Alignment****\n')
        print('sequence:', alignment.title)
        print('length:', alignment.length)
        print('e value:', hsp.expect)
        print(hsp.query[0:75] + '...')
        print(hsp.match[0:75] + '...')
        print(hsp.sbjct[0:75] + '...')
```

The variables set can be found in the following UML. However, in this UML, the parameters are not complete. See this [link](http://biopython.org/DIST/docs/api/Bio.Blast.Record-pysrc.html) to the source code for the complete parameters.

![](/img/blog/BlastRecord.png)

### References
---

[Linux系统中NCBI BLAST+本地化教程](http://blog.shenwei.me/local-blast-installation/)

[BLAST+使用方法](http://www.yelinsky.com/blog/archives/421.html)