
*******************************************
LISA: Landscape In-Silico deletion Analysis
*******************************************

LISA is a statistical test that finds the most influential Transcription Factors related to a set of genes. We leverage integrative modeling of a comprehensive dataset 
of 100s of chromatin accessiblity samples and 1000s of ChIP experiments to make predictions. Particularly, LISA models how much the *cis*-regulatory elements around 
a gene are influenced by deleting elements associated with a TF (a process we call *insilico* deletion). For more information, see 
`<https://genomebiology.biomedcentral.com/articles/10.1186/s13059-020-1934-6>`_.

Interfaces
**********

* `lisa.FromGenes`_
* `lisa.FromRegions`_

usage: make_docs.py oneshot [-h] [--seed SEED] [--use_motifs]
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
                        exact results.
  --use_motifs          Use motif hits instead of ChIP-seq peaks to represent
                        TF binding (only recommended if TF-of-interest is not
                        represented in ChIP-seq database).
  --save_metadata       Save json-formatted metadata from processing each gene
                        list.
  -o OUTPUT_PREFIX, --output_prefix OUTPUT_PREFIX
                        Output file prefix. If left empty, will write results
                        to stdout.
  --background_strategy {regulatory,random,provided}
                        Background genes selection strategy. LISA samples
                        background genes to compare to user's genes-of-
                        interest from a diverse regulatory background
                        (regulatory - recommended), randomly from all genes
                        (random), or uses a user-provided list (provided).
  --background_list BACKGROUND_LIST
                        user-supplied list of backgroung genes. Used when
                        --background_strategy flag is set to "provided"
  -b NUM_BACKGROUND_GENES, --num_background_genes NUM_BACKGROUND_GENES
                        Number of sampled background genes to compare to user-
                        supplied genes
  -v VERBOSE, --verbose VERBOSE
  -c CORES, --cores CORES
  -a {Direct,H3K27ac,DNase} [{Direct,H3K27ac,DNase} ...], --assays {Direct,H3K27ac,DNase} [{Direct,H3K27ac,DNase} ...]


usage: make_docs.py multi [-h] [--seed SEED] [--use_motifs] [--save_metadata]
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
                        exact results.
  --use_motifs          Use motif hits instead of ChIP-seq peaks to represent
                        TF binding (only recommended if TF-of-interest is not
                        represented in ChIP-seq database).
  --save_metadata       Save json-formatted metadata from processing each gene
                        list.
  -o OUTPUT_PREFIX, --output_prefix OUTPUT_PREFIX
                        Output file prefix.
  -v VERBOSE, --verbose VERBOSE
  -b NUM_BACKGROUND_GENES, --num_background_genes NUM_BACKGROUND_GENES
                        Number of sampled background genes to compare to user-
                        supplied genes. These genes are selection from other
                        gene lists.
  --random_background   Use random background selection rather than
                        "regulatory" selection.
  -c CORES, --cores CORES
  -a {Direct,H3K27ac,DNase} [{Direct,H3K27ac,DNase} ...], --assays {Direct,H3K27ac,DNase} [{Direct,H3K27ac,DNase} ...]


usage: make_docs.py regions [-h] [--seed SEED] [--use_motifs]
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
                        exact results.
  --use_motifs          Use motif hits instead of ChIP-seq peaks to represent
                        TF binding (only recommended if TF-of-interest is not
                        represented in ChIP-seq database).
  --save_metadata       Save json-formatted metadata from processing each gene
                        list.
  -r REGIONS, --regions REGIONS
                        Bed file with columns: chr, start, end[, score]
  -q--query_list Q__QUERY_LIST
                        user-supplied gene list. One gene per line in either
                        symbol or refseqID format
  -o OUTPUT_PREFIX, --output_prefix OUTPUT_PREFIX
                        Output file prefix. If left empty, will write results
                        to stdout.
  --background_strategy {regulatory,random,provided}
                        Background genes selection strategy. LISA samples
                        background genes to compare to user's genes-of-
                        interest from a diverse regulatory background
                        (regulatory - recommended), randomly from all genes
                        (random), or uses a user-provided list (provided).
  --background_list BACKGROUND_LIST
                        user-supplied list of backgroung genes. Used when
                        --background_strategy flag is set to "provided"
  -b NUM_BACKGROUND_GENES, --num_background_genes NUM_BACKGROUND_GENES
                        Number of sampled background genes to compare to user-
                        supplied genes
  -v VERBOSE, --verbose VERBOSE

