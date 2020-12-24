
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

lisa.FromGenes
**************

Interface for performing LISA test for TF influence using public chromatin accessibility data. Given just a set of genes, LISA will identify a subset of a large database
of public H3K27ac and DNase profiles that represents a model for multiple chromatin states around those genes. LISA then assesses the influence of TF binding 
on your genes-of-interest vs a sampling of background genes through through those representative datasets, and aggregates the effects to produce a final p-value.

This model is useful for integrating accessibility and binding data when you have strictly a list of associated genes (from scRNA-seq, for example). If you have 
genes-of-interest, as well as regions-of-interest, you may use the more specific test provided by ``lisa.FromRegions``.
    

**lisa.FromGenes(self, species, cores = 1, assays = ['Direct','H3K27ac','DNase'], isd_method = 'chipseq')**
    Initialize the LISA test using public data.

    :params:
    * **species:** {'hg38', 'mm10'}
    * **cores:** *int* number of cores to use. For optimal performance, allocate 1, 2, 5, or 10 cores. More cores is faster.
    * **assays:** *list* of {"Direct","H3K27ac","DNase"}, default is all tests
    * **isd_method:** {"chipseq", "motifs"} use ChIP-seq data or motifs to mark TF binding locations.
    
    :returns:
    * **lisa object**
        

    **.predict(self, query_list, background_list = [], background_strategy = 'regulatory', num_background_genes = 3000, seed = 2556)**
        Predict TF influence given a set of genes.

        :params:
        * **query_list:** *list* genes-of-interest, in either Symbol of RefSeqID format. Must provide between 20 to 500 genes.
        * **background_list:** *list* user-specified list of background genes to compare with query_list. Must contain more genes than query list and entire list will be used. If provided, ```background_strategy``` must be set to "provided".
        * **background_strategy:** {"regulatory","random","provided"}, regulatory will sample background genes from a stratified sample of TADs and regulatory states, random will randomly sample from all non-query genes.
        * **num_background_genes:** *int* Number of genes to use as comparison to query genes. More background genes make test slower, but more stable.
        * **seed:** *int* Seed for gene selection and regression model initialization.

        :returns:
        * **results:** Dictionary with each key representing a table column, sorted by "summary_p_value" field. The dictionary can be passed directly to a the pandas constructor: ``results_df = pd.DataFrame(results.todict())``.
        * **metadata:** Dictionary with test metadata. Includes query genes provided and background genes that were selected. This 
        metadata dict also contains information on the accessibility datasets that were selected to represent the chromatin landscape around you genes-of-interest, for example, the tissue and cell line from which the profiles were derived.
        


lisa.FromRegions
****************

Interface for performing LISA test for TF influence using user-provided regions. The regions may be accompanied with a positive weight or score that
notes the strength of that region for your metric of interest. Often, that metric is ATAC-seq or DNase read depth at that region, but you may provide any 
score as long as it is positive. LISA will rescale your metric automatically to suite the test. Regions should be more than 100 bp wide, but less than 1000 bp 
to ensure specificy for TF and motif hits within the regions. Peaks of ~300 bp are optimal since motifs hits can enrich ~75 to 100 bp away from peak summits. 

This test also allows more flexibility to change LISA's function for mapping genomic regions' influence on nearby genes. By default, LISA uses 'Regulatory Potential' 
with a decay of 10000 bp, meaning the regions over a gene's TSS recieve maximum influence, and influence decays by half every 10K bp. This decay rate can be increased to 
allow long-range distal elements more weight, or reduced to prioritize promoter influence. The most powerful extension of this flexibility is the ability to specify a 
custom genes x regions matrix, where every region's influence is mapped to every gene. 

This interface outputs results in the same format as the ``FromGenes`` interface.
    

**lisa.FromRegions(self, species, regions, region_scores = None, rp_map = 'basic', rp_decay = 10000, isd_method = 'chipseq')**
    Initialize the LISA test using user-defined regions.

    :params:
    * **species:** {'hg38', 'mm10'} 
    * **regions:** list orf lists/tuples with format [('chr', start, end[, score]), ... ], specifying user-defined regions. The score column is optional and if not provided, all 
        regions will be given same weight. This parameter may also be the filename of a bed file with the same format.
    * **region_scores:** *list or np.ndarray of shape (len(regions), )* (optional) Region scores/weights. Must be same length as regions. User may not provide regions with a score column and this parameter at the same time.
    * **rp_map:** *str, list, scipy.sparse_matrix, np.ndarray* RP map type, currently only supports "basic". User may also pass their own RP map of scipy.sparse_matrix or np.ndarry type in the shape (genes x regions)
    * **rp_decay:** *float, int* Decay rate of region influence on gene based on distance from TSS. Increase to prioritize distal regions, decrease to prioritize promoters. Default of 10000 bp is balanced.
    * **isd_method:** {"chipseq", "motifs"} use ChIP-seq data or motifs to mark TF binding locations.
    
    :returns:
    * **lisa object**
        

    **.predict(self, query_list, background_list = [], background_strategy = 'regulatory', num_background_genes = 3000, seed = 2556)**
        Predict TF influence given a set of genes.
        
        :params:
        * **query_list:** *list* genes-of-interest, in either Symbol of RefSeqID format. Must provide between 20 to 500 genes.
        * **background_list:** *list* user-specified list of background genes to compare with query_list. Must contain more genes than query list and entire list will be used. If provided, ```background_strategy``` must be set to "provided".
        * **background_strategy:** {"regulatory","random","provided"}, regulatory will sample background genes from a stratified sample of TADs and regulatory states, random will randomly sample from all non-query genes.
        * **num_background_genes:** *int* Number of genes to use as comparison to query genes. More background genes make test slower, but more stable.
        * **seed:** *int* Seed for gene selection and regression model initialization.

        :returns:
        * **results:** Dictionary with each key representing a table column, sorted by "summary_p_value" field. The dictionary can be passed directly to a the pandas constructor: ``results_df = pd.DataFrame(results.todict())``.
        * **metadata:** Dictionary with test metadata. Includes query genes provided and background genes that were selected.
        
