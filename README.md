Description
===========
Texomer is a statistical tool designed to perform allele-specific, tumor-deconvoluted transcriptome-exome integration of the bulk whole exome (WES) and whole transcriptome sequencing (WTS) data obtained from autologous patient tissue samples, such as those generated by the cancer cell atlas (TCGA).  It examines the germline SNP and somatic SNV sites in the WES and WTS data and estimates:
* tumor purity in the bulk DNA and RNA data, respectively  
* intratumor heterogeneity score in bulk DNA
* SNV allele-specific DNA copy number levels in the tumor compartment of the bulk DNA data
* SNV allele-specific RNA expression levels in the tumor compartment of the bulk RNA data
* differential allelic cis-regulatory effect (DACRE) score for each variant allele

If the input includes only the bulk DNA data, Texomer will output only estimates at the DNA level.

Publication
===========
Nature Methods volume 16, pages401–404 (2019)

Brief Communication
Published: 15 April 2019

Integrated transcriptomic–genomic tool Texomer profiles cancer tissues

Fang Wang, Shaojun Zhang, Tae-Beom Kim, Yu-yu Lin, Ramiz Iqbal, Zixing Wang, Vakul Mohanty, Kanishka Sircar, Jose A. Karam, Michael C. Wendl, Funda Meric-Bernstam, John N. Weinstein, Li Ding, Gordon B. Mills & Ken Chen 


System requirements and dependency
==================================
Texomer runs on a x86_64 Linux system, Python 2.7. It depends on samtools and bedtools to extract reads covering variant sites from WTS data.

It also requires R (version >= 3.4)
to run and has dependency on the R packages:

	bbmle, emdbook, copynumber,TitanCNA, facets, mixtools, ASCAT and sequenza.

***Samtools, bedtools and all R packeages which Texomer depends on are already incorporated in this release. Users do not need to load or install them.***

Installation
============
Please download and copy the distribution to your specific location. If you are cloning from github, ensure that you have git-lfs installed.

For example, if the downloaded distribuition is Texomer-2.0.tar.gz.
	Type 'tar zxvf Texomer-2.0.tar.gz'

Then, run Texomer.py in the resulting folder.

Usage
=====
```
Options:
  --version             show program's version number and exit
  -h, --help            Show this help message and exit.
  -p TEXOMER, --Texomer=TEXOMER
                        the path of Texomer
  -I INPUT, --Input=INPUT
                        the form of input file: BAM, Varscan, and Defined									
  -t TUMOR, --Tumor=TUMOR
                        Tumor WES bam file.
  -n NORMAL, --Normal=NORMAL
                        Normal WES bam file.
  -o OUTPATH, --outpath=OUTPATH
                        the output path.
  -r RNA, --RNA=RNA     RNA-seq data bam file       
  -g GERMLINE, --germline=GERMLINE
                        You can input your own germline mutation file together with
                        somatic mutation file (-s). The file include 8 columns:
                        chromosome, position, RefAllele, Altallele,
                        read counts of RefAllele in normal, read counts of
                        Altallele in normal, read counts of RefAllele in tumor
                        and read counts of Altallele in tumor with header
                        seperated by tab.
  -s SOMATIC, --somatic=SOMATIC
                        You can input your own somatic mutation file together with
                        germline mutation file (-g). The file include 8 columns:
                        chromosome, position, RefAllele, Altallele, read
                        counts of RefAllele in normal, read counts of
                        Altallele in normal, read counts of RefAllele in tumor
                        and read counts of Altallele in tumor with header
                        seperated by tab.
  -v VARSCAN, --varscan=VARSCAN
                        the Varscan2 output file based on somatic calling
  -e SNVEXPRESS, --snvexpress=SNVEXPRESS
                        The allelic read count of mutation from RNA-seq
                        including 7 columns: chromosome, position, ref, alt,
                        refnum, altnum and type (germline or somatic)
  -u ITER, --iter=ITER  Optimization using somatic mutation, 0 corresponding to
                        no optimization and 1 is optimization. The default = 1
  -f REFERENCE, --reference=REFERENCE
                        The location of reference sequence.


```

Input files
===========

DNA input files:

	Three kinds of input files are allowed in Texomer:

	(1) BAM files of tumor and normal tissue from WES

	(2) The output generated by Varscan, which includes both germline and somatic variants;

	(3) Two Defined files: one contains germline variants and the other contains somatic mutations.

	The format of these two files are the same. It should include 8 tab-delimited columns:
		chromosome (start from "chr"),
		position,
		reference allele,
		alternative allele,
		ref allelic read counts in the normal sample,
		alt allelic read counts in the normal sample,
		ref allelic read counts in the tumor sample,
		alt allelic read counts in the tumor sample.

RNA input files:

	Two kinds of input files are allowed in Texomer:

	(1) RNA-seq bam file.

	(2) Allelic read counts of mutation from the RNA-seq bam, which includes 7 tab-delimited cloumns:
		chromosome (start from "chr"),
		position,
		reference allele,
		alternative allele,
		reference allelic read count from the RNA-seq bam,
		alternative allelic read count from the RNA-seq bam,
		type of mutation (germline or somatic).    

Output files
============

There are multiple files:

If the input includes a RNA input file, Texomer would generate output at both DNA and RNA levels. Otherwise, it will output only estimation at the DNA level.

	(1) output.segment.txt file:
		position of copy nuymber segments;
		allele-specific copy number (Dmajor and Dminor),
		allele-specific expression levels(Rmajor and Rminor), and
		posterior probability of discordant expression corresponding to each segment.

	(2) output.mutation.txt file:
		position of mutations,
		reference(ref) and alternative(alt) allele,
		allelic read count from RNA-seq(refNum and altNum),
		mutation type(germline or somatic),
		allele specific copy number (altD corresponding to alternative allele and wildD corresponding to reference allele),
		allele specific expression level (altR corresponding to alternative allele and wildR corresponding to reference allele),
		posterior probability of discordant expression and DACRE score for each mutation.

	(3) output.summaryres.txt file:
		tumor purity at the DNA and/or the RNA level;
		ploidy and intra-tumor heterogeneity at the DNA level;
		whole genome doubling (WGD) estimates the proportion of amplified genomic region.

Run Texomer:
***The python script for easy run of Texomer is in the release directory. You can tune the
parameters as you wish.***

	Python Texomer.py [-t <tumor bam file>] [-n <normal bam file>] [-r <RNA bam file>] [-o <output path>] [-v <Varscan output>] [-g <Defined germline mutation input file>] [-s <Defined somatic mutation input file>] [-u <optimization>] [-e <Defined expression file of mutation>] [-f location of reference sequence] –p <Texomer path> –I <input form>
[...] contains optional parameters. The mandatory arguments are -p and -I. The form of input includes BAM, Varscan, and Defined.	 

About the default parameters
========================
Texomer optimizes estimation of tumor purity and allele specific copy numbers by combining both germline SNPs and somatic SNVs.

By default, -u is 1, which turns on iterative optimization.

A user can set -u 0 to turn off the iterative optimization.


Examples
========
Try Texomer in the package directory on the different example datasets

**Example 1: Input defined mutation file**

	python Texomer.py -p ./ -I Defined -g ./example/germline.input -s ./example/somatic.input.vcf -e ./example/RNA.SNV -o ./res1

if you want to run Texomer on only the DNA data:

	python Texomer.py -p ./ -I Defined -g ./example/germline.input -s ./example/somatic.input.vcf -o ./res1

*germline.input:*

	chr	pos	ref	alt	refNumN	altNumN	refNumT	altNumT
	chr1	12198	G	C	51	40	26	25
	chr1	12383	G	A	31	22	15	19

*somatic.input:*

	chr	pos	ref	alt	refNumN	altNumN	refNumT	altNumT
	chr1	16757604	G	A	170	0	45	8
	chr1	23083363	G	A	34	0	22	3

*RNA.SNV:*

	chr	pos	ref	alt	refNum	altNum	type
	chr1	12198	G	C	8	0	germline
	chr1	12383	G	A	2	0	germline

**Example 2: Input a varscan2 file**

	python Texomer.py -p ./ -I Varscan -v ./example/varscan.snp -r ./example/RNA.bam -o ./res2

*Bam input files are not provided on GitHub because of the limitation of file size.*


**Example 3: Input WES bam files**

	python Texomer.py -p ./ -I BAM -t ./example/Tumor.bam -n ./example/Normal.bam -r ./example/RNA.bam -f ./reference.fa -o ./res3
>The bam file should be aligned based on GRCH38.
>If you input bam files, Texomer will run longer because it needs to call mutations from the bam files.


**Texomer output**

*output.summaryres.txt*

	DNApurity	0.272030490933697
	Heterogeneity	0.362166913670839
	Ploidy	2.43420373296619
	RNApurity	0.333623992547959
	WGD 0.33

*output.segment.txt*

	chr	start	end	Dmajor	Dminor	Rmajor	Rminor	RTEL	BayesP
	chr1	12383	149854059	3	0	3	0	3	0.00271288011564652
	chr1	149904572	151142541	11	0	11	0	11	0.00976386546342944



>- Dmajor: the DNA copy number of the major allele.
>- Dminor: the DNA copy number of the minor allele.
>- Rmajor: the RNA expressoin level of the major allele.
>- Rminor: the RNA expression level of the minor allele.
>- RTEL: the total RNA expression level of the two alleles.
>- BayesP: the posterior probability that the RNA expression level is discordant with the copy number level

*output.mutations.txt*

	chr	pos	ref	alt	refNum	altNum	type	altD	altR	wildD	wildR	BayesP	eASEL	AEI	DACRE
	chr1	14464	A	T	161	29	Germline	0	0.869112773207088	3	4.26647728279266	0.165069332450197	0.869112773207088	-2.29543007263941	-2.99135865987574
	chr1	14677	G	A	562	275	Germline	0	0.565025922875357	3	3.23925254542283	0.0153830861293918	0.565025922875357	-2.51927198724942	-2.26822077283764

>- refNum: read counts of the reference allele from RNA-seq data.
>- altNum: read counts of the alernative allele from the RNA-seq data.
>- type: the types of the mutation are either Germline or Somatic.
>- altD: the copy number of the alternative allele.
>- altR: the expression level of the alternative allele.
>- wildD: the copy number of the reference (wildtype) allele.
>- wildR: the expression level of the reference (wildtype) allele.
>- BayesP: the posterior probability that the RNA expression level is discordant with the DNA copy number level.
>- eASEL: difference between the expression and the copy number levels of the alternative allele (altR-altD) .
>- AEI: difference between the expression levels of the alternative and the reference alleles (altR-wildR).
>- DACRE: the differential allelic cis-regulatory score

Developer
=========
Fang Wang (fwang9@mdanderson.org)

Draft date
==========
Nov. 1, 2018
