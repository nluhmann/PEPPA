Quick Start
***************
If you installed PEPPA via PIP, run
::

  PEPPA --testunit

To download an examples/ folder which contains GFF files of four *Escherichia coli* ST131 genomes. 

If you installed PEPPA via git pull, the examples/ folder is already present in your PEPPA/ root folder. 

* Test PEPPA

::

  PEPPA -p examples/ST131 -P examples/GCF_000010485.combined.gff.gz examples/*.gff.gz

* Test PEPPA_parse

::

  PEPPA_parser -g examples/ST131.PEPPA.gff -s examples/PEPPA_out -t -c -a 95

Key parameters for PEPPA
===========================
GFFs
-------------------------

The main inputs for PEPPA. The nucleotide sequences can be integrated in the same file or in a separate file. Details see `Inputs <inputs.rst>`_

-p or --prefix
-------------------------

The prefix for the output files, as well as intermediate files. The final outputs are:

1. <prefix>.PEPPA.gff
2. <prefix>.allele.fasta

Details see `Outputs <outputs.rst>`_.

--match_identity FLOAT
-------------------------
match_identity controls the minimal identity of an alignment to be considered in the pan-genome construction. PEPPA is insensitive to the value of this parameter, as long as it is low enough to cover the genetic diversity of the genomes. However, reducing this value will increase the run time of the program. 

By default, PEPPA accepts a minimum match identity of 65%. And the minimum match identity that has been tested (in *Mycoplasma*) is 40%. Use a lower value if you run PEPPA between genomes from different genera.

--min_cds INTEGER
-------------------------
This controls the minimal size of a gene to be considered in the final pan-genome. Short genes are less reliable and also hard to detect in a similarity-based search. 

By default, PEPPA ignores genes with <= 120 bps in their sizes. You can reduce the minimum size of accepted genes if you trust the original annotations (e.g., 90 bps to be consistent with the default settings in `prodigal  <http://gensoft.pasteur.fr/docs/prodigal/2.50/_README>`_). 

--pseudogene FLOAT
-------------------------
A coding region in the genome is assigned as a pseudogene, if its size is shorter than other orthologous genes by a certain proportion.

By default, PEPPA sets --pseudogene 0.8, therefore any gene that is >=80% of the representative gene will be assign as an "intact" gene, otherwise a "pseudogene". 

See `Parameters <parameters.rst>`_ for description of other paramters implemented in PEPPA. 

--orthology <nj,ml,sbh>
-------------------------
Three algorithms for separating paralogous genes from orthologous genes. 

* sbh: Single BLAST hit. Paralogous genes will be determined by their greater distances to the representative genes. Least accurate (comparable to MCL used by many other pan-genome pipelines), and is the most efficient in both run time and storage space. 
* nj: [Default] Neighbor-joining algorithm (based on RapidNJ). Quick and scalable to 1,000's of genomes. However, it might be less accurate than a maximum-likelihood algorithm. 
* ml: Maximum-likelihood algorithm (based on FastTree). The most accurate algorithm. Still fast for 10's of genomes but is not suggested for > 100 genomes. 

--genes FILENAME,FILENAME
-------------------------
This option allows you to use genes that may not be present in the current set of genomes. For example, you can align genes that have been described in `ecocyc <https://ecocyc.org/>`_ or used by `core genome MLST schemes <http://enterobase.warwick.ac.uk/species/senterica/download_data>` onto your genomes. Also see --priority to priorties the external genes in the alignments.

This option accepts multiple MULTI-FASTA files delimited by comma. 

--priority FILENAME,FILENAME:FILENAME
----------------------------------------
This parameter is very important if you want to make use of existing knowledge of some genes, such as those that have been valided by manual revise, transcriptomics, or protein structures. The genes included in the listed filenames will be used as representatives over the unlisted genes. 

This option accepts multiple MULTI-FASTA files delimited by comma(,), or colon(:). The files connected by colons are treated equally, whereas those separated by commas have different priorities. 

For example, --priority FILE_1,FILE_2:FILE_3,FILE_4 will give priorities as: 

+--------+------+
|Priority|FILE  |
+========+======+
|1       |FILE_1|
+--------+------+
|2       |FILE_2|
+--------+------+
|2       |FILE_3|
+--------+------+
|3       |FILE_4|
+--------+------+
