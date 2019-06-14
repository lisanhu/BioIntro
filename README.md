# Introduction Information for DNA Sequence Alignment

__by:__ Sanhu Li

## 1. Required Background
It's best to ensure you know something about the following points before you can understand this document.

1. DNA double helix structure, and A-T/U, C-G, connection.

## 2. What is it doing?
DNA sequence alignment is an algorithm, given input as many many reads, output alignment result.

Reads: Reads are short character sequences of DNA, which we can get from sequencing instruments. Refer to [fastq wiki page](https://en.wikipedia.org/wiki/FASTQ_format) to understand what information could be provided by the reads. Mostly the sequence names, the DNA sequences, and the base-wise quality scores.

Alignment result: For a particular read, we will need to output a lot of information about it. Including the gene/genome name that produce this read, the location of the read starting from the beginning of the gene/genome, the aligned text, the CIGAR string, etc. The fields listed above are far from enough what we need to output. Please refer to [the sam file format definition](https://samtools.github.io/hts-specs/SAMv1.pdf) for more information.

CIGAR string: It's a format that can represent how we decide to align two sequences. [More info](https://genome.sph.umich.edu/wiki/SAM#What_is_a_CIGAR.3F).

## 3. What are the challenges? (Maybe more)
1. Reads are not exact matches
1. There are repeating regions in the genomes, making it harder to decide where is a particular read comes from
1. Huge amount of data to be processed
1. No good evaluation that can convince everyone
1. etc...


## 4. The general idea
Most current tools are using a seed-and-extend strategy
1. For one read, try to get some initial information, which allows us to scan certain regions in the genome rather than scan all locations in the genome. Such information is usually obtained by taking a (or several) slice(s) from the read, called seeds, and do exact string matching on these seeds assuming they don't have any differences from the genes/genome. The exact locations of the seeds, are treated as candidate locations of the read. Because if the assumption is true, the read must come from those locations instead of anywhere on the genome.
1. After deciding the candidate locations, we need to scan each location and choose the best one for the read, and most sequence aligners are only reporting one location for a read, even if there might be a tie in deciding which location the read really comes from. Usually we will apply local alignment algorithms, such as Smith Waterman algorithm, to decide how we align the read to the genome and decide the quality of this location to be able to select the best one in the future.
1. [Smith Waterman algorithm](https://en.wikipedia.org/wiki/Smith%E2%80%93Waterman_algorithm) is a must-read if you don't fully understand what is sequence alignment, why we need it, etc.

## 5. More comments about General idea
The general idea part is something we must do in the algorithms, but it's far from what we are really doing, we need to do much more things other than that. This section is a brief description about what information is needed and some basic idea of how the information is acquired. Note: it's not a full list since I have not yet confirmed these are all the information we need.

From the general idea, we should have the location of the read (in the whole genome), and the alignment (CIGAR string). The following is a list of what we need.

The output format for sequence alignment tools is SAM format. The record region contains the following information that is not naive and needs to be computed (naive ones such as read name will not be put here):

1. FLAG. FLAG contains a lot of information about the read/reads. Including: 
    * template having multiple segments in sequencing (??? what is a template?)
    * each segment properly aligned according to the aligner (???)
    * segment unmapped (read unmapped?)
    * next segment in the template unmapped (either the pair, or the mate of this read is not mapped.)
    * SEQ being reverse complemented (This read is from the other strand)
    * SEQ of the next segment in the template being reverse complemented (The mate/pair(?) read is from the other strand)
    * the first segment in the template (???)
    * the last segment in the template (???)
    * secondary alignment (???)
    * not passing filters, such as platform/vendor quality controls (???)
    * PCR or optical duplicate (???)
    * supplementary alignment (???)

1. RNAME. The name of the reference gene the read belongs to. Or, it's a name of one of the sequences in the [FASTA file](https://en.wikipedia.org/wiki/FASTA_format). We need to convert the location in the genome(file), to the name of the gene.

1. POS. The location of the read in the gene or named sequence. We need to convert location in the genome(file) to location within the gene (sequence).

1. MAPQ. The mapping quality, or, in other words, how confident your aligner trust this result is correct. There's a equation in the SAM specification, but it needs you to define your confidence, which result in diversity in computing this part. Need to design a scoring mechanism and compute for your own.

1. CIGAR. The alignment result of the read. It's usually computed using Smith-Waterman-like algorithms, such as Banded Smith Waterman etc. This info has been computed from the general idea.

1. RNEXT. Reference sequence name of the primary alignment of the NEXT read in the template.  Forthe last read, the next read is the first read in the template.

1. PNEXT: 1-based Position of the primary alignment of the NEXT read in the template.  

1. TLEN:  signed  observed  Template  LENgth.   If  all  segments  are  mapped  to  the  same  reference,  theunsigned observed template length equals the number of bases from the leftmost mapped base to therightmost mapped base.  The leftmost segment has a plus sign and the rightmost has a minus sign.The sign of segments in the middle is undefined. 

1. QUAL: ASCII of base QUALity plus 33 (same as the quality string in the Sanger FASTQ format).

This section is not fully explained yet, please contact me if you have further questions.

## 6. A typical workflow to find Variants (GATK workflow)

1. Adapter trimming. The reads are still hot from the instrument, and the instrument has sticked some slices, called adapters in those reads. If we don't remove the adapters in the reads, it will obviously affect our result. At the same time, this step will remove some low quality bases from the input FASTQ data.

1. Sequence alignment. Producing the SAM/BAM file. Note: BAM/SAM files are having the same content, but BAM files are more machine friendly (compact). They can easily be converted to the other format and most tools are accepting both formats.
 
1. Post-process of the SAM files. Because there are different aligners in the market and they are doing things differently. Some of them will output a file with correct header, some with in-correct header, some without header, so we need to fix the SAM format to make it ready for down-stream applications.

1. Sort the SAM file according to the reference name and location. The aim at here is we want a base wise look at all the reads. For example, if we have a 50x coverage of the genome, meaning for each base, we have 50 copies in 50 reads. We want to check the genome base by base and see for the 1st gene, the 1st base, what are the contents in those reads? If all the reads for this location says it's an A, but in reference genome it's a C, it means the sample is having an A here, which should be an SNP. If only one of those 50 reads is saying it's an A, the other 49 reads are saying it's a C, so we know the A might be an instrument error. We need high coverage, we need to sort the reads based on the location because we want to elimate the instrument errors and figure out what's real in the sample in order to decide whether it's an SNP or something. If we don't sort, just try to do this directly, it's really time consuming.

1. Mark duplicates. I'm not sure what is this step doing. ~~It hears like to mark some duplicate regions which are produced by the nature of genes (human genome mostly have 2 copies of every gene, because we have a pair of chromosomes, some plants may have 6 copies of the same gene. It's really hard to tell which is the real location, because if the regions are the same, you have no way to tell). After marking these duplicate regions, the down stream apps may be able to combine the reads in both regions together to help with something?~~ This part needs to be clarified. 

    According to GACT best practice document, it seems this step is looking for read pairs that are mapped to the same location and mark one of them as duplicate. In this way, we can reduce the number of reads to be considered in further steps. Still need to be clarified, not sure my understanding is correct.

1. Base-recalibration. I have no idea what it's doing. ~~Just guess it's trying to look at the whole genome base by base and decide what do we have at this location? Is it an SNP or an insertion or something else? Note this step will consume SAM/BAM file and produce VCF file.~~

    Previous understanding is wrong, it's not producing VCF file, it's to update the base-wise scores for each record in SAM/BAM.

1. Do variant call (HaplotypeCaller). After we did base-recalibration, ~~we should be able to put the results in a vcf file?~~ we actually have reads with good base-wise scores, and the locations of the reads are mapped already, we can easily assembly the reads to get the sample genome. Variant call is to scan and compare the assembled genome with the reference genome to identify the variants (VCF file).

1. From GACT official document, before base-recalibration, old pipelines still require us to do an indel realignment. It's basically looking at a region, get the reads in this region, do a re-alignment (Smith-Waterman or Needleman-Wunsch) to make alignment (CIGARs) in this region more accurate because aligners are usually doing bad in the end of the reads. However, it's no longer required if you plan to use a variant caller that performs a haplotype assembly step, such as HaplotypeCaller or MuTect2.

## 7. More Readings
This section will list some readings that will help you understand more about the process, and the suggested sessions are usually very short, easy to understand, and could help correct some wrong understandings from the above text.
1. [Introduction to the GATK Best Practices](https://software.broadinstitute.org/gatk/best-practices/), section 2, "Analysis phases". Remember currently, we are only wishing to get the VCF files, which means we end at phase 2.
2. [Data pre-processing for variant discovery](https://software.broadinstitute.org/gatk/best-practices/workflow?id=11165), section "Main Steps"
3. [Germline short variant discovery (SNPs + Indels)](https://software.broadinstitute.org/gatk/best-practices/workflow?id=11145), section "Main Steps > Call variants per-sample", the other sections are optional and are usually not relevant.

Although only certain sections are mentioned here, it's highly recommended to read other sections in those documentations. 