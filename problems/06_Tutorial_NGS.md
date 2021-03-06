guitData and scripts for this Tutorial and Lectures are available at [https://github.com/hyphaltip/CSHL_2012_NGS](https://github.com/hyphaltip/CSHL_2012_NGS).



Preparation Steps
=================

1. Checkout my github repo for this course.

Using Git on the command line (or install Git for Mac)

    $git clone git://github.com/hyphaltip/htbda_perl_class.git

 
The rename_seq.pl and other tools are in examples/NGS

1. Download Broad IGV viewer at
[http://www.broadinstitute.org/igv/](http://www.broadinstitute.org/igv/)

2. Download the Saccharomyces genome from [SGD
site](http://downloads.yeastgenome.org/sequence/S288C_reference/genome_releases/S288C_reference_genome_R64-1-1_20110203.tgz). Uncompress this and get the .fsa file which is the genome. 

You could do this like, but it may be broken/down.

    $ curl -O http://downloads.yeastgenome.org/sequence/S288C_reference/genome_releases/S288C_reference_genome_R64-1-1_20110203.tgz
    $ tar zxf S288C_reference_genome_R64-1-1_20110203.tgz

Do this instead

    $tar zxf /srv/projects/db/GEN220/S288C_reference_genome_R64-1-1_20110203.tgz

Run this script to fix the chromosome names in the download file so the will match the GFF file.

    perl htbda_perl_class/examples/NGS/rename_seq.pl S288C_reference_genome_R64-1-1_20110203/S288C_reference_sequence_R64-1-1_20110203.fsa > Saccharomyces.fa

  * You need to fix this GFF file so it doesn't have any sequence, to
    do this a grep to find where the '>' lines are where the sequence
    as fasta is in there and find the first one.

Commands to run

    grep -n ">" S288C_reference_genome_R64-1-1_20110203/saccharomyces_cerevisiae_R64-1-1_20110208.gff
    # note the number for the first '>' - there are a few lines before that we want to drop so
    # and the last position in the file we want is 16425 so we can use head 
    $ head -n 16425 S288C_reference_genome_R64-1-1_20110203/saccharomyces_cerevisiae_R64-1-1_20110208.gff > saccharomyces_cerevisiae_R64-1-1_20110208.noseq.gff

  * Use this saccharomyces_cerevisiae_R64-1-1_20110208.noseq.gff for GFF file later needs.

3. Obtain the data, you can link the fastq files you want to process from ```/srv/projects/db/GEN220/fastq``` by doing 
   ```ln -s /srv/projects/db/GEN220/fastq``` in your directory where you will work.

Notes
=====

Use this system to get the programs ready to use on the command line on biocluster

Get your own machine on biocluster

    qsub -I

Then when you have a new machine you want to do this on the command line

    module load stajichlab
    module load samtools
    module load java
    module load bwa
    module load sickle
    module load fastx_toolkit
    export PICARD=/usr/local/java/common/lib/picard-tools/
    export GATK=/opt/GATK/latest/


To run against GATK you will need to use this path to the .jar file /opt/GATK/latest/GenomeAnalysisTK.jar

To run with Picard you will want to use this path to the .jar files /usr/local/java/common/lib/picard-tools/
    

Tutorial
========
You can use any of the fastq file pairs that are in the fastq folder you linked in above (located in /srv/projects/db/GEN220/fastq). They are from the same strain but are just different runs. For consistency with the lecture slides you could use the SRR567756 sample (SRR567756_1.fastq and SRR567756_2.fastq).

1. Trim FASTQ data for quality using [sickle](https://github.com/najoshi/sickle) - run ```sickle pe``` to see how to run PE options

2. Compare the FASTQC quality report for one of the files (_1 or _2) files both before and after trimming. Set this up in the background so you can run it and do other things in the meantime.

```fastqc -h``` to get help

3. Align reads to the genome using BWA. This requires you to also build and index for the genome. See the [lecture notes](http://courses.stajich.org/public/gen220/lectures/NGS/NGS_DNA.slides.html#slide34).

3. Fix the Read groups see [this slide](http://courses.stajich.org/public/gen220/lectures/NGS/NGS_DNA.slides.html#slide51)

4. Realign reads with Picard and GATK [based on lecture](http://courses.stajich.org/public/gen220/lectures/NGS/NGS_DNA.slides.html#slide40).

1. Call SNPs with SAMTools - refer to the SAMtools manpage on mpileup for more details. [http://samtools.sourceforge.net/](http://samtools.sourceforge.net/)

1. Call SNPs with GATK, using [example from the lecture](http://courses.stajich.org/public/gen220/lectures/NGS/NGS_DNA.slides.html#slide42)

1. Run Filtering steps on GATK output SNPs to remove potential biased or low-quality ones using options [provided in lecture](http://courses.stajich.org/public/gen220/lectures/NGS/NGS_DNA.slides.html#slide45).

1. Calculate the total number of remaining SNPs. Count the lines or use [vcftools](http://vcftools.sourceforge.net/).

1. For advanced users, intersect this list of SNPs (in the VCF file)
with the GFF for the genome to determine which SNPs are in coding
regions.  Read up on
[BEDTools](http://code.google.com/p/bedtools/). The genome annotation in GFF is available
in the folder where the genome was downloaded from [SGD](http://yeastgenome.org).
 
9. Open the genome file for Saccharomces in IGV.  Then add the GFF
file as annotation track. Then BAM file, and VCF file in IGV to view
the SNPs in context of the gene annotation and the read-depth

Feel free to try this also with your own favorite organism. Many
datasets exist in the [SRA](http://www.ncbi.nlm.nih.gov/sra) from
genome resequencing. To extend the problem, download more than 4
strains so you can apply comparisons between individuals instead of
just between one individual and the reference.

For example, here is the [Drosophila reference
panel](http://www.ncbi.nlm.nih.gov/bioproject/36679) which included
sequencing 192 individuals. Or find something smaller (10 C.elegans
for example).


