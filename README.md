# semi_global_long_read_aligner.py

This is a script to align error-prone long reads (e.g. PacBio or Nanopore) to one or more references in a semi-global manner. It uses GraphMap to do the actual alignment and then filters the resulting alignments and outputs summaries.

Semi-global alignment allows for unpenalised end gaps, but the alignment will continue until one of the two sequences ends. This includes cases where the two sequences overlap and cases where one sequence is contained within the other:

```
  AAAAA        AAAAAAAAAAA         AAAAAAAA     AAAAAAAA
  |||||          |||||||           |||||           |||||
BBBBBBBBB        BBBBBBB       BBBBBBBBB           BBBBBBBBB
```

This tool is intended for cases where the reads and reference are expected to match perfectly (or at least as perfectly as error-prone long reads can match). An example of an appropriate case would be if the reference sequences are assembled contigs of a bacterial strain and the long reads are from the same strain.

### Required arguments:
* `--ref`: FASTA file containing one or more reference sequences
* `--reads`: FASTQ file of long reads
* `--sam`: SAM file of resulting alignments

### Some important optional arguments:
* `--no_graphmap`: If you use this flag, the aligner will align all reads by itself. If you don't use this flag (i.e. the default behaviour), the aligner will first align reads using GraphMap, which is faster. Then it will only try to align reads for which GraphMap failed or overlap the end of a reference.
* `--graphmap_path`: Use this argument to specify the location of the GraphMap executable. If not used, this script will just try `graphmap` (i.e. assumes that GraphMap is in your PATH).
* `--scores`: Comma-delimited string of alignment scores: 'match, mismatch, gap open, gap extend'. Examples: '3,-6,-5,-2' (the default), '5,-6,-10,0' (BLASR's default), '2,-5,-2,-1' (BWA-MEM's default), '2,-3,-5,-2' (blastn's default).
* `--min_len`: Alignments shorter than this length (in bp) will not be included in the results.
* `--threads`: The number of CPU threads to use (default is to use all available CPUs).
* `--verbosity`: How much stdout to produce. 0 = no stdout. 1 = default. 2 = extra output (display the alignments for each read). 3 = lots of extra output (display the attempted alignments for each read). 4 = tons (I use this for debugging - you probably shouldn't use it).

### Build instructions
This aligner uses a C++ shared library, so it must be compiled first. Just run `make` in the respository directory and it should (hopefully) build everything required.

Required inputs:
  1) FASTA file of one or more reference sequences
  2) FASTQ file of long reads

Output: SAM file of alignments

# assembly_checker.py

This is a script which allows a user to assess whether a completed bacterial genome assembly is correct, using long reads. It requires long read alignments (produced by `semi_global_long_read_aligner.py`) and produces summary tables and plots.

# hybrid_assembler.py

This script is a work in progress. It will ultimately conduct a hybrid assembly of Illumina reads and long reads, using SPAdes to produce an Illumina read assembly graph and then scaffolding the graph with long reads. It will work in a streaming, real-time fashion, using the long reads as they are made available.

