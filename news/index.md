# Changelog

## planttfhunter 1.13.1

This release adds two new classification schemes (TAPscan and
Plant-TFClass) and redesigns the API around a simpler workflow:
[`annotate_domains()`](../reference/annotate_domains.md) -\>
[`classify_tfs()`](../reference/classify_tfs.md) -\>
[`get_tf_counts()`](../reference/get_tf_counts.md).

NEW FEATURES

- TFs can now be classified using the classification scheme of TAPscan
  v4 (Petroll et al., 2025), in addition to PlantTFDB’s scheme. The
  TAPscan classification algorithm (tapscan_classify.pl v4.76, with
  rules v82 and coverage values v11) was ported to R and reproduces the
  results of the original Perl implementation. TAPscan classifications
  include a variable `TAP_class` indicating whether each family contains
  transcription factors (TF), transcriptional regulators (TR), or
  putative TAPs (PT).

- TFs are also classified into superclasses, classes, and families from
  the structure-based classification of Plant-TFClass (Blanc-Mathieu et
  al., 2024). Plant-TFClass classifications are inferred from TAPscan
  classifications using a correspondence table between the two systems.
  When a TAPscan subfamily corresponds to more than one Plant-TFClass
  family (TAPscan’s AP2, ABI3/VP1, and MADS), PlantTFDB subfamilies are
  used to resolve the ambiguity; when that is not possible, all possible
  classifications are reported, separated by ‘;’.

- PlantTFDB classifications now include both families (e.g., AP2/ERF)
  and subfamilies (e.g., ERF), as in PlantTFDB’s classification scheme.

- New function [`annotate_domains()`](../reference/annotate_domains.md)
  to annotate sequences with the domains used by PlantTFDB and TAPscan.
  It has a `threads` argument to run HMMER with multiple threads.

- New data sets `tapscan_scheme` (TAPscan’s classification rules),
  `planttfclass_scheme` (correspondence between TAPscan and
  Plant-TFClass), and `tap_class` (classes, descriptions, and references
  for TAPscan families).

BREAKING CHANGES

- `annotate_pfam()` was replaced by
  [`annotate_domains()`](../reference/annotate_domains.md), which
  returns a list of two data frames (`PlantTFDB` and `TAPscan`) with
  variables `Gene`, `Domain`, `qlen`, `c_evalue`, `hmm_from`, and
  `hmm_to`.

- [`classify_tfs()`](../reference/classify_tfs.md) now takes the output
  of [`annotate_domains()`](../reference/annotate_domains.md) as input
  (data frames returned by `annotate_pfam()` are no longer accepted),
  and returns a data frame with one row per gene and variables `Gene`,
  `PlantTFDB_family`, `PlantTFDB_subfamily`, `TAPscan_family`,
  `TAPscan_subfamily`, `TAP_class`, `PlantTFClass_superclass`,
  `PlantTFClass_class`, and `PlantTFClass_family`. Note that the
  classifications previously reported in the variable `Family` (e.g.,
  ERF) are now in `PlantTFDB_subfamily`, and `PlantTFDB_family` contains
  PlantTFDB families (e.g., AP2/ERF). Genes assigned to more than one
  PlantTFDB family have families (and subfamilies) separated by ‘;’.

- [`get_tf_counts()`](../reference/get_tf_counts.md) now takes a named
  list of data frames with TF classifications (as returned by
  [`classify_tfs()`](../reference/classify_tfs.md)) as input, instead of
  a list of `AAStringSet` objects. This way, domains are annotated only
  once, and TFs can be counted in different ways (and filtered, if
  needed) without running HMMER again. By default, TFs are counted per
  PlantTFDB family (previously, PlantTFDB subfamily).

- Data set `classification_scheme` was renamed to `planttfdb_scheme`,
  and names ‘M_type’, ‘MYB_related’, and ‘LBD (AS2/LOB)’ were changed to
  ‘M-type’, ‘MYB-related’, and ‘LBD’, respectively, to match the output
  of [`classify_tfs()`](../reference/classify_tfs.md).

- Data sets `gsu_families` and `tf_counts` were removed, as they can be
  obtained in a few milliseconds with `classify_tfs(gsu_annotation)` and
  [`get_tf_counts()`](../reference/get_tf_counts.md), respectively. Data
  sets `gsu` and `gsu_annotation` were updated.

BUG FIXES

- HD-ZIP (via HD-ZIP_I/II), TALE (via BELL), and WOX TFs were never
  identified, because the names of these domains did not match the names
  of their profile HMMs. Such TFs were classified as HB-other.

- Trihelix and STAT TFs were never identified for the same reason.

- Genes with two B-box domains and a CCT domain were classified as both
  CO-like and DBB. They are now classified as CO-like only.

- [`get_tf_counts()`](../reference/get_tf_counts.md) returned an error
  when species names were not in alphabetical order, and did not reorder
  species metadata to match the order of species in the count matrix.

## planttfhunter 0.99.2

CHANGES

- Made small changes suggested by Bioconductor reviewer.

## planttfhunter 0.99.0

NEW FEATURES

- Added a `NEWS.md` file to track changes to the package.
