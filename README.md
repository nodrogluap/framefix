# framefix
Adjust a FastA DNA genome file's ORFs to correct frameshifts by best Diamond match.

This is a simple script that corrects homopolymer stretches (e.g. GGGGGGGG) that putatively cause 
artifactual frameshifts in open reading frames of a prokaryotic genome. These false positive frameshifts 
are common in nanopore-derived genome assemblies, and the threshold for detection is based on 
that chemistry (homopolymers of 6bp+ for pore type R9.4).  Frameshifts are corrected parsimoniously by adding or 
subtracting one base to restore the frame described by the best [Diamond] match against a provided
protein database. The exception is if the parsimonious 
edit makes the homopolymer less than 6bp, in which case two bases are added to the homopolymer stretch 
to restore the reading frame. I would suggest running this script after other polishing methods such as 
nanopolish or Medaka.

## Prerequisites

This program assumes you have [Diamond](https://github.com/bbuchfink/diamond) installed, 
and a local indexed copy of a protein database of some sort. 
I typically use [UniRef90](https://www.uniprot.org/downloads).

## Usage

The program is a simple Perl script with no code dependencies, so once 
executable (e.g. ```chmod u+x framefix```) it can be run like so:

``bash
./framefix FastA 1e-25 output_prefix /path/to/uniref90/diamond_index
``

Where 1e-25 is the e-value threshold to consider a match evidence of a frameshift. 
Three output files are generated ```output_prefix.homopolymers_query.txt``` which lists the homopolymers 
and their genomic context that were submitted for inquiry to Diamond, ```output_prefix.diamond_nr.txt```
which is the best Diamond output for each of those queries (note that we are not doing taxomnomic 
assignment with this code, so best hit is sufficient), and ```output_prefix.frameshift_adjusted.fna```
which has the parsimonious edits based on those Diamond matches applied to your input FastA genome file.
