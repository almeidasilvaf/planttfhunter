# Annotate protein sequences with domains used for TF classification

Protein sequences are scanned with HMMER for domains used by the
classification schemes of **PlantTFDB** and **TAPscan**. For PlantTFDB,
PFAM and self-built profile HMMs are used with the domain-specific score
cutoffs defined by PlantTFDB (or an E-value threshold, for domains
without cutoffs). For TAPscan, TAPscan v4 profile HMMs are used with
their gathering (GA) thresholds, as in the original TAPscan
implementation.

## Usage

``` r
annotate_domains(seq = NULL, evalue = 1e-05, threads = 1)
```

## Arguments

- seq:

  An `AAStringSet` object. The sequences in this object must represent
  only the translated sequences of primary (or longest) transcripts. To
  create such an object from a FASTA file, use
  [`Biostrings::readAAStringSet()`](https://rdrr.io/pkg/Biostrings/man/XStringSet-io.html).

- evalue:

  Numeric indicating the E-value threshold for `hmmsearch` to be used
  for PlantTFDB domains without pre-defined domain cutoffs. Default:
  1e-05.

- threads:

  Numeric indicating the number of threads to be used by `hmmsearch`.
  Default: 1.

## Value

A list of 2 data frames named **PlantTFDB** and **TAPscan**, with domain
annotation for each classification scheme. Both data frames have one row
per domain hit and the following variables:

- Gene:

  Character, gene ID.

- Domain:

  Character, domain ID or name.

- qlen:

  Numeric, length of the profile HMM.

- c_evalue:

  Numeric, conditional E-value of the domain hit.

- hmm_from, hmm_to:

  Numeric, start and end coordinates of the alignment in the profile
  HMM.

Rows are kept in the same order as in the HMMER output, as this order is
used by the TAPscan classification algorithm.

## Examples

``` r
data(gsu)
seq <- gsu[1:5]
if(hmmer_is_installed()) {
    annotate_domains(seq)
}
```
