# retroPlus

This is a SnakeMake pipeline based on RetroSeq https://github.com/tk2/RetroSeq,  a tool for discovery and genotyping of transposable element variants.
The pipeline runs RetroSeq configured to seach for HERV-K insertions (can look for any other mobile elements, see below how to change that), filters RetroSeq predictions, verifies the insertions by assembling the regions around each insertion and running the assembled contigs through RepeatMasker, and finally annotates insertions.

<img width="794" alt="image" src="https://user-images.githubusercontent.com/51742526/128677961-017e99c9-bdd0-4951-bbc3-e0057a3e1fe1.png">


Users can choose the level of filtering and verification of predicted insertions, as well as a few steps of downstream analysis: comparing the predictions with the known HERV-K insertions to separate them into previously reported and novel insertions, as well as using AnnotSV to further annotate the insertions with genes and regulatory elements, and their potential clinical significance.


As the snakemake pipeline is modular and providing different outputs, one way to call it is to specify the output file.

# Example 1

To produce a file with retroseq prediction which have been filtered and annotated, you would call it:

snakemake --use-conda --use-envmodules --cores 5 <RESULTS_DIRECTORY>/<YOUR_SAMPLE_PREFIX>.annotatedFiltered.tsv

This would run retroseq, filter the results, and run AnnotSV to annotate them. The prerequisites are that either a <YOUR_SAMPLE_PREFIX>.bam or <YOUR_SAMPLE_PREFIX>.cram exist in the respective bam or cram directories specified in file config.yaml.


# Example 2

To produce a file with retroseq prediction which have been filtered and verified with the extra verification step (assembling the region and running throught RepeatMasker) and then annotated, you would call it:

snakemake --use-conda --use-envmodules --cores 5 <RESULTS_DIRECTORY>/<YOUR_SAMPLE_PREFIX>.annotatedVerified.tsv


# Example 3

To take filtered and verified predictions and to mark known and novel HERV-K insertions, you would call:
snakemake --use-conda --use-envmodules --cores 5 <RESULTS_DIRECTORY>/ {<YOUR_SAMPLE_PREFIX>.novelHitsFV.bed,<YOUR_SAMPLE_PREFIX>.knownHitsFV.bed}


# Example 4
To run it on the cluster, you add to the command line:
 
--cluster "sbatch -p YOUR_PARTITION --mem-per-cpu=7G"

# Example 5

It can be run with multiple files simultaneously, snakemake will split it in different cores. For example, to get annotated and verified predictions for several files (BAM or CRAM), you would call it:

snakemake --use-conda --use-envmodules --cores 5 <RESULTS_DIRECTORY>/{BAM1_prefix,BAM2_prefix, BAM3_prefix}.annotatedVerified.tsv

To call in on the cluster

nohup snakemake -s SnakefileRetroPlus --use-conda --use-envmodules --cores 5 --cluster "sbatch -p brc --mem-per-cpu=7G" /mnt/lustre/groups/herv_project/snakemake/retroseq/results/{LP6008463-DNA_G04.annotatedFiltered.tsv,LP6008463-DNA_G04.annotatedFiltered.html,LP6008463-DNA_G04.annotatedVerified.tsv,LP6008463-DNA_G04.annotatedVerified.html,LP6008463-DNA_G04.novelHitsF.bed,LP6008463-DNA_G04.novelHitsFV.bed,LP6008463-DNA_G04.knownHitsF.bed,LP6008463-DNA_G04.knownHitsFV.bed}


## Installing Dependencies

## RepeatMasker
https://github.com/rmhubley/RepeatMasker
RepeatMasker is only needed for the verification step.  Without it you can still run RetroSeq, Filter insertions, mark known and novel insertions and run functional annotation. If you do not want the extra verification step, you do not need to install RepeatMasker.

Important:
After the installation of RepeatMasker, update the installation path in the config.yaml file.

## AnnotSV 
Follow the instructions to download and install AnnotSV and knotAnnotSV.  This is only needed if you are going to run the annotation step of RetroPlus pipeline.  

https://lbgi.fr/AnnotSV/

https://github.com/mobidic/knotAnnotSV

Important:
After the installation, update the path of the two install directories (to AnnotSV and knotAnnotSV) in the config.yaml file.
