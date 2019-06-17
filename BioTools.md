# Bio tools for a typical workflow

## 1. Summary
In the introduction, we mentioned a general workflow that can help us find SNPs, and in this article, we will talk about what are the tools doing each step and some basic explanation.

## 2. Workflow review and details
Based on the workflow in the introduction, we will see how to apply each step to a yeast sample.

0. We need to get the reads for a yeast sample, and we need to get the yeast genome. 

1. After getting the reads, we need to do adapter trimming. This step will use the tool trim_galore. A example call to this command is 

    `trim_galore --illumina --fastqc {reads.fq}`. 
    
    Read more about this command using `trim_galore --help`.

2. After trimming, we need to do alignment. This step will use BWA-MEM or our tool. A example BWA-MEM command is 
    
    `bwa mem -M -t {num-threads} -R "{read-group-header}" {genome-file.fa} {reads1-file.fq} {reads2-file.fq} > {output-file.sam}`. 

    This step will generate the SAM files, we can convert it to BAM files using samtools.

3. After alignment, we need to fix our SAM file in order to cooperate with downstream apps. 
    
    A good SAM/BAM file should fullfill the following constraints:
    1. a valid header (several SQ lines, with one RG line, with one PG line)
    2. valid records (each record should have all the fields required, and the fileds cannot be conflict. For example, the CIGAR field is required for each record, for the ones that we don't align, we have to put "*" to indicate it's unavailable. We need to have proper FLAGS field etc. )
    3. sorted records.

    Check the SAM file we get, if it's already fullfill some of the constraints, we could skip the corresponding steps.

    The header RG region could be fixed using picard tools, GATK4 or some other tools. Google for more information (Suggestion key words: picard tools AddOrReplaceReadGroups, gatk4 AddOrReplaceReadGroups). Picard tools are included in GATK4, that's why they seem to be really similar in usage and command names.

    Valid records have to be ensured by the alignment tools.

4. To sort the file, we could use samtools, picard tools, or GATK4. Google for samtools sort, picard tools sortsam, gatk4 sortsam, etc.

5. Mark duplicates. We need to use GATK MarkDuplicates for this step.

6. (optional)In old pipelines, we still need to do a re-alignment here, we will call GATK RealignerTargetCreator, and GATK IndelRealigner.

7. Base Recalibration. GATK BaseRecalibrator and then GACT PrintReads.

8. Variant call. GATK HaplotypeCaller.

## 3. Practice

Try to write your own script from above clues for a whole GATK pipeline, and try not to refer to the existing scripts. 

Using documentation from https://software.broadinstitute.org/gatk/ 

You can enter the GATK command (for example baserecalibration) to the right top textbox on that webpage, and click on the GATK tab in the search results, and the first one is the detailed documentation of that command. 

Of course, make good use of google.



[Previous](README.md)
[Next](InstallTools.md)