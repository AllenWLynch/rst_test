
********
Lisa CLI
********

Installing LISA using pip or conda adds the "lisa" command to your path. LISA's functionality is divided into three main subcommands:

* `lisa oneshot`_: one genelist
* `lisa multi`_: multiple genelists
* `lisa regions`_: one genelist and a list of regions

Which are used depending on the evidence you have on hand. 

See the `User Guide <docs/user_guide.rst>`_ for more usage information.
See the `Python API <docs/python_api.rst>`_ for more in-depth description of tests and parameters.


lisa oneshot
************

You have:

* one genelist

Use LISA to infer influential TFs from one gene list, with background epigenetic landscape modeled using public data. 
If you have multiple lists, this option will be slower than using "multi" due to data-loading time. 

*Example:*

$ lisa oneshot hg38 ./genelist.txt -b 501 -c 5 --seed=2556 --save_metadata > results.tsv

usage: lisa oneshot [-h] [--seed SEED] [--use_motifs]
                            [--save_metadata] [-o OUTPUT_PREFIX]
                            [--background_strategy {regulatory,random,provided}]
                            [--background_list BACKGROUND_LIST | -b NUM_BACKGROUND_GENES]
                            [-v VERBOSE] -c CORES
                            [-a {Direct,H3K27ac,DNase} [{Direct,H3K27ac,DNase} ...]]
                            {hg38,mm10} query_list

positional arguments:
  {hg38,mm10}           Find TFs associated with human (hg38) or mouse (mm10)
                        genes
  query_list            user-supplied gene lists. One gene per line in either
                        symbol or refseqID format

optional arguments:
  -h, --help            show this help message and exit
  --seed SEED           Random seed for gene selection. Allows for reproducing
                        exact results. (default: 2556)
  --use_motifs          Use motif hits instead of ChIP-seq peaks to represent
                        TF binding (only recommended if TF-of-interest is not
                        represented in ChIP-seq database). (default: chipseq)
  --save_metadata       Save json-formatted metadata from processing each gene
                        list. (default: False)
  -o OUTPUT_PREFIX, --output_prefix OUTPUT_PREFIX
                        Output file prefix. If left empty, will write results
                        to stdout. (default: None)
  --background_strategy {regulatory,random,provided}
                        Background genes selection strategy. LISA samples
                        background genes to compare to user's genes-of-
                        interest from a diverse regulatory background
                        (regulatory - recommended), randomly from all genes
                        (random), or uses a user-provided list (provided).
                        (default: regulatory)
  --background_list BACKGROUND_LIST
                        user-supplied list of backgroung genes. Used when
                        --background_strategy flag is set to "provided"
                        (default: None)
  -b NUM_BACKGROUND_GENES, --num_background_genes NUM_BACKGROUND_GENES
                        Number of sampled background genes to compare to user-
                        supplied genes (default: 3000)
  -v VERBOSE, --verbose VERBOSE
  -c CORES, --cores CORES
  -a {Direct,H3K27ac,DNase} [{Direct,H3K27ac,DNase} ...], --assays {Direct,H3K27ac,DNase} [{Direct,H3K27ac,DNase} ...]


lisa multi
**********
You have:
* multiple genelists

Use LISA to infer influential TFs from multiple lists. This function processes each genelist independently in the same manner as the "oneshot" command, but reduces data loading time. Useful when performing 
the test on up and down-regulated genes from multiple RNA-seq clusters.

*Example:*
``
$ lisa multi hg38 ./genelists/*.txt -b 501 -c 5 -o ./results/
``

usage: lisa multi [-h] [--seed SEED] [--use_motifs] [--save_metadata]
                          -o OUTPUT_PREFIX [-v VERBOSE]
                          [-b NUM_BACKGROUND_GENES] [--random_background] -c
                          CORES
                          [-a {Direct,H3K27ac,DNase} [{Direct,H3K27ac,DNase} ...]]
                          {hg38,mm10} query_lists [query_lists ...]

positional arguments:
  {hg38,mm10}           Find TFs associated with human (hg38) or mouse (mm10)
                        genes
  query_lists           user-supplied gene lists. One gene per line in either
                        symbol or refseqID format

optional arguments:
  -h, --help            show this help message and exit
  --seed SEED           Random seed for gene selection. Allows for reproducing
                        exact results. (default: 2556)
  --use_motifs          Use motif hits instead of ChIP-seq peaks to represent
                        TF binding (only recommended if TF-of-interest is not
                        represented in ChIP-seq database). (default: chipseq)
  --save_metadata       Save json-formatted metadata from processing each gene
                        list. (default: False)
  -o OUTPUT_PREFIX, --output_prefix OUTPUT_PREFIX
                        Output file prefix. (default: None)
  -v VERBOSE, --verbose VERBOSE
  -b NUM_BACKGROUND_GENES, --num_background_genes NUM_BACKGROUND_GENES
                        Number of sampled background genes to compare to user-
                        supplied genes. These genes are selection from other
                        gene lists. (default: 3000)
  --random_background   Use random background selection rather than
                        "regulatory" selection. (default: regulatory)
  -c CORES, --cores CORES
  -a {Direct,H3K27ac,DNase} [{Direct,H3K27ac,DNase} ...], --assays {Direct,H3K27ac,DNase} [{Direct,H3K27ac,DNase} ...]


lisa regions
************
You have:
* one genelist
* regions (250 - 1000 bp wide) of interest related to that list
* optional: a positive score/weight associated with each region (you may pass zero-weight regions, but they do not affect the test and will be filtered out)

Use LISA to infer TF influence on your geneset, but provide your regions-of-interest rather than building a background epigenetic model using public data. When providing 
your own regions, LISA uses higher resolution, more precise binding data to increase the power of the test. Your regions should be between ~250 and 1000 bp in width, and the 
associated score should be positive. Scores are often read-depth at those regions, but can be any metic you think may influence gene regulation.

*Example:*
``
$ lisa regions -r ./regions.bed -q ./genelist.txt -b 501 --save_metadata > results.tsv
``

usage: lisa regions [-h] [--seed SEED] [--use_motifs]
                            [--save_metadata] [-r REGIONS]
                            [-q--query_list Q__QUERY_LIST] [-o OUTPUT_PREFIX]
                            [--background_strategy {regulatory,random,provided}]
                            [--background_list BACKGROUND_LIST | -b NUM_BACKGROUND_GENES]
                            [-v VERBOSE]
                            {hg38,mm10}

positional arguments:
  {hg38,mm10}           Find TFs associated with human (hg38) or mouse (mm10)
                        genes

optional arguments:
  -h, --help            show this help message and exit
  --seed SEED           Random seed for gene selection. Allows for reproducing
                        exact results. (default: 2556)
  --use_motifs          Use motif hits instead of ChIP-seq peaks to represent
                        TF binding (only recommended if TF-of-interest is not
                        represented in ChIP-seq database). (default: chipseq)
  --save_metadata       Save json-formatted metadata from processing each gene
                        list. (default: False)
  -r REGIONS, --regions REGIONS
                        Bed file with columns: chr, start, end[, score]
                        (default: None)
  -q--query_list Q__QUERY_LIST
                        user-supplied gene list. One gene per line in either
                        symbol or refseqID format (default: None)
  -o OUTPUT_PREFIX, --output_prefix OUTPUT_PREFIX
                        Output file prefix. If left empty, will write results
                        to stdout. (default: None)
  --background_strategy {regulatory,random,provided}
                        Background genes selection strategy. LISA samples
                        background genes to compare to user's genes-of-
                        interest from a diverse regulatory background
                        (regulatory - recommended), randomly from all genes
                        (random), or uses a user-provided list (provided).
                        (default: regulatory)
  --background_list BACKGROUND_LIST
                        user-supplied list of backgroung genes. Used when
                        --background_strategy flag is set to "provided"
                        (default: None)
  -b NUM_BACKGROUND_GENES, --num_background_genes NUM_BACKGROUND_GENES
                        Number of sampled background genes to compare to user-
                        supplied genes (default: 3000)
  -v VERBOSE, --verbose VERBOSE


lisa oneshot
************

You have:

* one genelist

Use LISA to infer influential TFs from one gene list, with background epigenetic landscape modeled using public data. 
If you have multiple lists, this option will be slower than using "multi" due to data-loading time. 

*Example:*

$ lisa oneshot hg38 ./genelist.txt -b 501 -c 5 --seed=2556 --save_metadata > results.tsv

usage: lisa oneshot [-h] [--seed SEED] [--use_motifs]
                            [--save_metadata] [-o OUTPUT_PREFIX]
                            [--background_strategy {regulatory,random,provided}]
                            [--background_list BACKGROUND_LIST | -b NUM_BACKGROUND_GENES]
                            [-v VERBOSE] -c CORES
                            [-a {Direct,H3K27ac,DNase} [{Direct,H3K27ac,DNase} ...]]
                            {hg38,mm10} query_list

positional arguments:
  {hg38,mm10}           Find TFs associated with human (hg38) or mouse (mm10)
                        genes
  query_list            user-supplied gene lists. One gene per line in either
                        symbol or refseqID format

optional arguments:
  -h, --help            show this help message and exit
  --seed SEED           Random seed for gene selection. Allows for reproducing
                        exact results. (default: 2556)
  --use_motifs          Use motif hits instead of ChIP-seq peaks to represent
                        TF binding (only recommended if TF-of-interest is not
                        represented in ChIP-seq database). (default: chipseq)
  --save_metadata       Save json-formatted metadata from processing each gene
                        list. (default: False)
  -o OUTPUT_PREFIX, --output_prefix OUTPUT_PREFIX
                        Output file prefix. If left empty, will write results
                        to stdout. (default: None)
  --background_strategy {regulatory,random,provided}
                        Background genes selection strategy. LISA samples
                        background genes to compare to user's genes-of-
                        interest from a diverse regulatory background
                        (regulatory - recommended), randomly from all genes
                        (random), or uses a user-provided list (provided).
                        (default: regulatory)
  --background_list BACKGROUND_LIST
                        user-supplied list of backgroung genes. Used when
                        --background_strategy flag is set to "provided"
                        (default: None)
  -b NUM_BACKGROUND_GENES, --num_background_genes NUM_BACKGROUND_GENES
                        Number of sampled background genes to compare to user-
                        supplied genes (default: 3000)
  -v VERBOSE, --verbose VERBOSE
  -c CORES, --cores CORES
  -a {Direct,H3K27ac,DNase} [{Direct,H3K27ac,DNase} ...], --assays {Direct,H3K27ac,DNase} [{Direct,H3K27ac,DNase} ...]


lisa multi
**********
You have:
* multiple genelists

Use LISA to infer influential TFs from multiple lists. This function processes each genelist independently in the same manner as the "oneshot" command, but reduces data loading time. Useful when performing 
the test on up and down-regulated genes from multiple RNA-seq clusters.

*Example:*
``
$ lisa multi hg38 ./genelists/*.txt -b 501 -c 5 -o ./results/
``

usage: lisa multi [-h] [--seed SEED] [--use_motifs] [--save_metadata]
                          -o OUTPUT_PREFIX [-v VERBOSE]
                          [-b NUM_BACKGROUND_GENES] [--random_background] -c
                          CORES
                          [-a {Direct,H3K27ac,DNase} [{Direct,H3K27ac,DNase} ...]]
                          {hg38,mm10} query_lists [query_lists ...]

positional arguments:
  {hg38,mm10}           Find TFs associated with human (hg38) or mouse (mm10)
                        genes
  query_lists           user-supplied gene lists. One gene per line in either
                        symbol or refseqID format

optional arguments:
  -h, --help            show this help message and exit
  --seed SEED           Random seed for gene selection. Allows for reproducing
                        exact results. (default: 2556)
  --use_motifs          Use motif hits instead of ChIP-seq peaks to represent
                        TF binding (only recommended if TF-of-interest is not
                        represented in ChIP-seq database). (default: chipseq)
  --save_metadata       Save json-formatted metadata from processing each gene
                        list. (default: False)
  -o OUTPUT_PREFIX, --output_prefix OUTPUT_PREFIX
                        Output file prefix. (default: None)
  -v VERBOSE, --verbose VERBOSE
  -b NUM_BACKGROUND_GENES, --num_background_genes NUM_BACKGROUND_GENES
                        Number of sampled background genes to compare to user-
                        supplied genes. These genes are selection from other
                        gene lists. (default: 3000)
  --random_background   Use random background selection rather than
                        "regulatory" selection. (default: regulatory)
  -c CORES, --cores CORES
  -a {Direct,H3K27ac,DNase} [{Direct,H3K27ac,DNase} ...], --assays {Direct,H3K27ac,DNase} [{Direct,H3K27ac,DNase} ...]


lisa regions
************
You have:
* one genelist
* regions (250 - 1000 bp wide) of interest related to that list
* optional: a positive score/weight associated with each region (you may pass zero-weight regions, but they do not affect the test and will be filtered out)

Use LISA to infer TF influence on your geneset, but provide your regions-of-interest rather than building a background epigenetic model using public data. When providing 
your own regions, LISA uses higher resolution, more precise binding data to increase the power of the test. Your regions should be between ~250 and 1000 bp in width, and the 
associated score should be positive. Scores are often read-depth at those regions, but can be any metic you think may influence gene regulation.

*Example:*
``
$ lisa regions -r ./regions.bed -q ./genelist.txt -b 501 --save_metadata > results.tsv
``

usage: lisa regions [-h] [--seed SEED] [--use_motifs]
                            [--save_metadata] [-r REGIONS]
                            [-q--query_list Q__QUERY_LIST] [-o OUTPUT_PREFIX]
                            [--background_strategy {regulatory,random,provided}]
                            [--background_list BACKGROUND_LIST | -b NUM_BACKGROUND_GENES]
                            [-v VERBOSE]
                            {hg38,mm10}

positional arguments:
  {hg38,mm10}           Find TFs associated with human (hg38) or mouse (mm10)
                        genes

optional arguments:
  -h, --help            show this help message and exit
  --seed SEED           Random seed for gene selection. Allows for reproducing
                        exact results. (default: 2556)
  --use_motifs          Use motif hits instead of ChIP-seq peaks to represent
                        TF binding (only recommended if TF-of-interest is not
                        represented in ChIP-seq database). (default: chipseq)
  --save_metadata       Save json-formatted metadata from processing each gene
                        list. (default: False)
  -r REGIONS, --regions REGIONS
                        Bed file with columns: chr, start, end[, score]
                        (default: None)
  -q--query_list Q__QUERY_LIST
                        user-supplied gene list. One gene per line in either
                        symbol or refseqID format (default: None)
  -o OUTPUT_PREFIX, --output_prefix OUTPUT_PREFIX
                        Output file prefix. If left empty, will write results
                        to stdout. (default: None)
  --background_strategy {regulatory,random,provided}
                        Background genes selection strategy. LISA samples
                        background genes to compare to user's genes-of-
                        interest from a diverse regulatory background
                        (regulatory - recommended), randomly from all genes
                        (random), or uses a user-provided list (provided).
                        (default: regulatory)
  --background_list BACKGROUND_LIST
                        user-supplied list of backgroung genes. Used when
                        --background_strategy flag is set to "provided"
                        (default: None)
  -b NUM_BACKGROUND_GENES, --num_background_genes NUM_BACKGROUND_GENES
                        Number of sampled background genes to compare to user-
                        supplied genes (default: 3000)
  -v VERBOSE, --verbose VERBOSE


lisa oneshot
************

You have:

* one genelist

Use LISA to infer influential TFs from one gene list, with background epigenetic landscape modeled using public data. 
If you have multiple lists, this option will be slower than using "multi" due to data-loading time. 

*Example:*

$ lisa oneshot hg38 ./genelist.txt -b 501 -c 5 --seed=2556 --save_metadata > results.tsv

usage: lisa oneshot [-h] [--seed SEED] [--use_motifs]
                            [--save_metadata] [-o OUTPUT_PREFIX]
                            [--background_strategy {regulatory,random,provided}]
                            [--background_list BACKGROUND_LIST | -b NUM_BACKGROUND_GENES]
                            [-v VERBOSE] -c CORES
                            [-a {Direct,H3K27ac,DNase} [{Direct,H3K27ac,DNase} ...]]
                            {hg38,mm10} query_list

positional arguments:
  {hg38,mm10}           Find TFs associated with human (hg38) or mouse (mm10)
                        genes
  query_list            user-supplied gene lists. One gene per line in either
                        symbol or refseqID format

optional arguments:
  -h, --help            show this help message and exit
  --seed SEED           Random seed for gene selection. Allows for reproducing
                        exact results. (default: 2556)
  --use_motifs          Use motif hits instead of ChIP-seq peaks to represent
                        TF binding (only recommended if TF-of-interest is not
                        represented in ChIP-seq database). (default: chipseq)
  --save_metadata       Save json-formatted metadata from processing each gene
                        list. (default: False)
  -o OUTPUT_PREFIX, --output_prefix OUTPUT_PREFIX
                        Output file prefix. If left empty, will write results
                        to stdout. (default: None)
  --background_strategy {regulatory,random,provided}
                        Background genes selection strategy. LISA samples
                        background genes to compare to user's genes-of-
                        interest from a diverse regulatory background
                        (regulatory - recommended), randomly from all genes
                        (random), or uses a user-provided list (provided).
                        (default: regulatory)
  --background_list BACKGROUND_LIST
                        user-supplied list of backgroung genes. Used when
                        --background_strategy flag is set to "provided"
                        (default: None)
  -b NUM_BACKGROUND_GENES, --num_background_genes NUM_BACKGROUND_GENES
                        Number of sampled background genes to compare to user-
                        supplied genes (default: 3000)
  -v VERBOSE, --verbose VERBOSE
  -c CORES, --cores CORES
  -a {Direct,H3K27ac,DNase} [{Direct,H3K27ac,DNase} ...], --assays {Direct,H3K27ac,DNase} [{Direct,H3K27ac,DNase} ...]


lisa multi
**********
You have:
* multiple genelists

Use LISA to infer influential TFs from multiple lists. This function processes each genelist independently in the same manner as the "oneshot" command, but reduces data loading time. Useful when performing 
the test on up and down-regulated genes from multiple RNA-seq clusters.

*Example:*
``
$ lisa multi hg38 ./genelists/*.txt -b 501 -c 5 -o ./results/
``

usage: lisa multi [-h] [--seed SEED] [--use_motifs] [--save_metadata]
                          -o OUTPUT_PREFIX [-v VERBOSE]
                          [-b NUM_BACKGROUND_GENES] [--random_background] -c
                          CORES
                          [-a {Direct,H3K27ac,DNase} [{Direct,H3K27ac,DNase} ...]]
                          {hg38,mm10} query_lists [query_lists ...]

positional arguments:
  {hg38,mm10}           Find TFs associated with human (hg38) or mouse (mm10)
                        genes
  query_lists           user-supplied gene lists. One gene per line in either
                        symbol or refseqID format

optional arguments:
  -h, --help            show this help message and exit
  --seed SEED           Random seed for gene selection. Allows for reproducing
                        exact results. (default: 2556)
  --use_motifs          Use motif hits instead of ChIP-seq peaks to represent
                        TF binding (only recommended if TF-of-interest is not
                        represented in ChIP-seq database). (default: chipseq)
  --save_metadata       Save json-formatted metadata from processing each gene
                        list. (default: False)
  -o OUTPUT_PREFIX, --output_prefix OUTPUT_PREFIX
                        Output file prefix. (default: None)
  -v VERBOSE, --verbose VERBOSE
  -b NUM_BACKGROUND_GENES, --num_background_genes NUM_BACKGROUND_GENES
                        Number of sampled background genes to compare to user-
                        supplied genes. These genes are selection from other
                        gene lists. (default: 3000)
  --random_background   Use random background selection rather than
                        "regulatory" selection. (default: regulatory)
  -c CORES, --cores CORES
  -a {Direct,H3K27ac,DNase} [{Direct,H3K27ac,DNase} ...], --assays {Direct,H3K27ac,DNase} [{Direct,H3K27ac,DNase} ...]


lisa regions
************
You have:
* one genelist
* regions (250 - 1000 bp wide) of interest related to that list
* optional: a positive score/weight associated with each region (you may pass zero-weight regions, but they do not affect the test and will be filtered out)

Use LISA to infer TF influence on your geneset, but provide your regions-of-interest rather than building a background epigenetic model using public data. When providing 
your own regions, LISA uses higher resolution, more precise binding data to increase the power of the test. Your regions should be between ~250 and 1000 bp in width, and the 
associated score should be positive. Scores are often read-depth at those regions, but can be any metic you think may influence gene regulation.

*Example:*
``
$ lisa regions -r ./regions.bed -q ./genelist.txt -b 501 --save_metadata > results.tsv
``

usage: lisa regions [-h] [--seed SEED] [--use_motifs]
                            [--save_metadata] [-r REGIONS]
                            [-q--query_list Q__QUERY_LIST] [-o OUTPUT_PREFIX]
                            [--background_strategy {regulatory,random,provided}]
                            [--background_list BACKGROUND_LIST | -b NUM_BACKGROUND_GENES]
                            [-v VERBOSE]
                            {hg38,mm10}

positional arguments:
  {hg38,mm10}           Find TFs associated with human (hg38) or mouse (mm10)
                        genes

optional arguments:
  -h, --help            show this help message and exit
  --seed SEED           Random seed for gene selection. Allows for reproducing
                        exact results. (default: 2556)
  --use_motifs          Use motif hits instead of ChIP-seq peaks to represent
                        TF binding (only recommended if TF-of-interest is not
                        represented in ChIP-seq database). (default: chipseq)
  --save_metadata       Save json-formatted metadata from processing each gene
                        list. (default: False)
  -r REGIONS, --regions REGIONS
                        Bed file with columns: chr, start, end[, score]
                        (default: None)
  -q--query_list Q__QUERY_LIST
                        user-supplied gene list. One gene per line in either
                        symbol or refseqID format (default: None)
  -o OUTPUT_PREFIX, --output_prefix OUTPUT_PREFIX
                        Output file prefix. If left empty, will write results
                        to stdout. (default: None)
  --background_strategy {regulatory,random,provided}
                        Background genes selection strategy. LISA samples
                        background genes to compare to user's genes-of-
                        interest from a diverse regulatory background
                        (regulatory - recommended), randomly from all genes
                        (random), or uses a user-provided list (provided).
                        (default: regulatory)
  --background_list BACKGROUND_LIST
                        user-supplied list of backgroung genes. Used when
                        --background_strategy flag is set to "provided"
                        (default: None)
  -b NUM_BACKGROUND_GENES, --num_background_genes NUM_BACKGROUND_GENES
                        Number of sampled background genes to compare to user-
                        supplied genes (default: 3000)
  -v VERBOSE, --verbose VERBOSE

