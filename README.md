# Bio-RNASeq
Allows the analysis of RNA-Seq data from BAM files all the way to Differential Gene Expression.

[![Build Status](https://travis-ci.org/sanger-pathogens/Bio-RNASeq.svg?branch=master)](https://travis-ci.org/sanger-pathogens/Bio-RNASeq)   
[![License: GPL v3](https://img.shields.io/badge/License-GPL%20v3-brightgreen.svg)](https://github.com/sanger-pathogens/Bio-RNASeq/blob/master/GPL-LICENCE)

## Content
  * [Introduction](#introduction)
  * [Installation](#installation)
    * [Required dependencies](#required-dependencies)
  * [Usage](#usage)
    * [Calculating Read Counts](#calculating-read-counts)
    * [Merging GFF Files](#merging-gff-files)
    * [Quantifying Differential Gene Expression](#quantifying-differential-gene-expression)
  * [License](#license)
  * [Feedback/Issues](#feedbackissues)
  * [Citation](#citation)

## Introduction
Bio-RNASeq consists of three components. The __rna_seq_expression__ script takes in an aligned sequence file and a corresponding annotation file and creates a spreadsheet with expression values. The script can only handle a single GFF file, so ff the annotation is broken up into several files, __gff3_concat__ can be used to merge the annotation into one single GFF3 compatible file. __differential_expression_with_deseq__ allows analysis of differential gene expression directly from the output of rna_seq_expression, or from a bespoke expression dataset. It makes use of the DESeq Bioconductor R package to carry out differential gene expression analysis.

## Installation
Bio-RNASeq has the following dependencies:

### Required dependencies
 * [samtools](https://github.com/samtools)
 * [R](http://www.r-project.org/)
 * [Bioconductor](http://www.bioconductor.org/)
 * [DESeq](http://bioconductor.org/packages/release/bioc/html/DESeq.html)

You will also need to download samtools v0.1.18 and build it on your system. Bio-RNASeq makes use of the Samtools v0.1.18 C API. You can get it [here](https://github.com/samtools/samtools/tree/0.1.18).

Once you've downloaded this, in a bash terminal, in the samtools v0.1.18 directory, run
```
make
```
__NOTE:__ You don't need to run `make install`. You don't need to install the older version of samtools on your system.

To install Bio-RNASeq, please see the details provided below. If you encounter an issue when installing Bio-RNASeq please contact your local system administrator. If you encounter a bug please log it [here](https://github.com/sanger-pathogens/Bio-RNASeq/issues) or email us at path-help@sanger.ac.uk

Clone the repo:
```
git clone https://github.com/sanger-pathogens/Bio-RNASeq.git
```
To install the required perl modules, in the cloned repo run:
```
dzil authordeps | cpanm
dzil listdeps | cpanm
```
Make sure the tests pass:
```
dzil test
```
Install Bio-RNASeq:
```
dzil install
```
## Usage

There are three components to this application.

### Calculating Read Counts
```
rna_seq_expression
Usage:
  -s|sequence_file           <aligned BAM file>
  -a|annotation_file         <annotation file (GFF)>
  -p|protocol                <standard|strand_specific>
  -o|output_base_filename    <Optional: base name and location to use for output files>
  -q|minimum_mapping_quality <Optional: minimum mapping quality>
  -c|no_coverage_plots       <Dont create Artemis coverage plots>
  -i|intergenic_regions      <Include intergenic regions>
  -b|bitwise_flag            <Only include reads which pass filter>
  -k|parallel_processes      <Number of CPUs to use, defaults to 1>
  -h|help                    <print this message>

This script takes in an aligned sequence file (BAM) and a corresponding annotation file (GFF) and creates a spreadsheet with expression values.
The BAM must be aligned to the same reference that the annotation refers to and must be sorted.
```
The RPKM values are calculated according to two different methodologies:

 1. total number of reads on the bam file that mapped to the reference genome

 2. total number of reads on the bam file that mapped to gene models in the reference genome

The *expression.csv file will contain both datasets. 

Coverage plots compatible with Artemis will also be produced. You can download [Artemis here](http://sanger-pathogens.github.io/Artemis/)

Example usage:
```
rna_seq_expression -s [filename.bam] -a [filename.gff] -p [standard|strand_specific] -o [./foobar]
```

### Merging GFF Files
```
gff3_concat
Usage:
  -i|input_dir        <full path to the directory containing the gff files to concatenate>
  -o|output_dir       <full path to the directory where the concatenated gff file will be written to>
  -t|tag              <the name to tag the concatenated gff file with>
  -h|help             <print this message>

  This script will concatenate several GFF files into one, maintaining GFF3 compatibility.
  It takes in the location of the collection of GFF files [-i|input_dir];
  The path where the final concatenated GFF file should be written to [-o|output_dir];
  A customised tag for the newly created GFF file [t|tag].
```
Example	usage:
```
gff3_concat -i [full path to directory of gff files] -o [full path to output directory] -t [name of the newly merged GFF file]
```

### Quantifying Differential Gene Expression
```
differential_expression_with_deseq
Usage:
  -i|input         <A file with the list of samples to analyse and their corresponding files of expression values
                    in the format ("filepath","condition","replicate","lib_type"). lib_type defaults to paired-end
                    if not specified on the samples file>
  -o|output        <The name of the file that will be used as the DeSeq analysis input. NOTE - The file will be
                    writen wherever you're running deseq_run from>
  -c|column        <Optional: Number of the column you want to use as your read count from your expression files.
                              Defaults to the second column in the expression file if no value is specified>
  -h|help          <print this message>

It makes use of the DESeq Bioconductor R package to carry out differential gene expression analysis.

Simon Anders and Wolfgang Huber (2010): Differential expression
  analysis for sequence count data. Genome Biology 11:R106

It takes as input a file with the list of samples to analyse and their corresponding files of expression
values in the format ("filepath","condition","replicate","lib_type"). It parses this file and accesses
all the files defined in the first column and extracts a gene list and the read counts from them.
It generates a matrix ready for DESeq analysis. It's final output will be a spreadsheet (.csv)
that can be loaded subsequently into a DESeq session or can be visualised in Excel.
```
Example	usage:
```
differential_expression_with_deseq -i [file containing list of files to analyse and key descriptions] -o [name to be used for all the output files generated] -c [Number of the read count column (1-1000)]
```
## License
Bio-RNASeq is free software, licensed under [GPLv3](https://github.com/sanger-pathogens/Bio-RNASeq/blob/master/GPL-LICENCE).

## Feedback/Issues
Please report any issues to the [issues page](https://github.com/sanger-pathogens/bio-rnaseq/issues) or email path-help@sanger.ac.uk

## Citation
_Simon Anders and Wolfgang Huber (2010): Differential expression  analysis for sequence count data. Genome Biology 11:R106_
