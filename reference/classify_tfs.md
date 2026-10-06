# Identify TFs and classify them in families

TFs are classified using the classification schemes of PlantTFDB,
TAPscan, and Plant-TFClass.

## Usage

``` r
classify_tfs(
  domain_annotation = NULL,
  scheme = c("all", "PlantTFDB", "TAPscan", "PlantTFClass")
)
```

## Arguments

- domain_annotation:

  A list of data frames with domain annotation, as returned by
  [`annotate_domains()`](annotate_domains.md).

- scheme:

  Character indicating which classification scheme(s) to return. One of
  'all' (all schemes), 'PlantTFDB', 'TAPscan', or 'PlantTFClass'.
  Default: 'all'.

## Value

A data frame with one row per gene and the following variables (if
**scheme = "all"**):

- Gene:

  Gene ID.

- PlantTFDB_family:

  TF family according to PlantTFDB.

- PlantTFDB_subfamily:

  TF subfamily according to PlantTFDB.

- TAPscan_family:

  TF family according to TAPscan.

- TAPscan_subfamily:

  TF subfamily according to TAPscan.

- TAP_class:

  Class of transcription-associated protein (TAP) according to TAPscan:
  transcription factor (TF), transcriptional regulator (TR), or putative
  TAP (PT). See `data(tap_class)`.

- PlantTFClass_superclass:

  TF superclass according to Plant-TFClass.

- PlantTFClass_class:

  TF class according to Plant-TFClass.

- PlantTFClass_family:

  TF family according to Plant-TFClass.

Only genes classified by at least one scheme are included. Genes not
classified by a scheme have NA in the respective variables. Genes
assigned to more than one PlantTFDB family have families (and
subfamilies, in the same order) separated by ';'.

If **scheme** is 'PlantTFDB', 'TAPscan', or 'PlantTFClass', only
variable **Gene** and the variables of the chosen scheme are returned,
and only genes classified by that scheme are included. Note that TAPscan
classifies not only TFs, but also transcriptional regulators and
putative TAPs, which can be distinguished using the variable
**TAP_class**.

## Details

PlantTFDB and TAPscan classifications are based on the domains found in
each gene. Plant-TFClass is a structure-based classification of TFs into
superclasses, classes, and families, and Plant-TFClass classifications
are inferred from TAPscan classifications using a correspondence table
between the two classification systems. When a TAPscan subfamily
corresponds to more than one Plant-TFClass family (e.g., TAPscan's AP2
includes Plant-TFClass's AP2, ERF/DREB, and RAV), PlantTFDB subfamilies
are used to resolve the ambiguity. If the ambiguity cannot be resolved
(e.g., the gene was not classified by PlantTFDB), all possible
classifications are reported, separated by ';'.

In all schemes, classification levels without a more specific level are
filled with the name of the more general level. For example, PlantTFDB
family bHLH has no subfamilies, so its subfamily is bHLH.

The classification schemes are available in the data sets
`planttfdb_scheme` (PlantTFDB), `tapscan_scheme` (TAPscan), and
`planttfclass_scheme` (correspondence between TAPscan and
Plant-TFClass).

## References

Jin, J., Tian, F., Yang, D. C., Meng, Y. Q., Kong, L., Luo, J., & Gao,
G. (2017). PlantTFDB 4.0: toward a central hub for transcription factors
and regulatory interactions in plants. Nucleic Acids Research, 45(D1),
D1040-D1045.

Petroll, R., Varshney, D., Hiltemann, S., Finke, H., Schreiber, M., de
Vries, J., & Rensing, S. A. (2025). Enhanced sensitivity of TAPscan v4
enables comprehensive analysis of streptophyte transcription factor
evolution. The Plant Journal, 121(1), e17184.

Blanc-Mathieu, R., Dumas, R., Turchi, L., Lucas, J., & Parcy, F. (2024).
Plant-TFClass: a structural classification for plant transcription
factors. Trends in Plant Science, 29(1), 40-51.

## Examples

``` r
data(gsu_annotation)
families <- classify_tfs(gsu_annotation)
head(families)
#>         Gene     PlantTFDB_family PlantTFDB_subfamily TAPscan_family
#> 1 AIG92585.1                 <NA>                <NA>            HAT
#> 2 gsu04730.1             Nin-like            Nin-like         RWP-RK
#> 3 gsu05000.1             Nin-like            Nin-like         RWP-RK
#> 4 gsu06140.1 GARP;MYB superfamily G2-like;MYB-related            MYB
#> 5 gsu06730.1                 <NA>                <NA>           bZIP
#> 6 gsu07030.1                 <NA>                <NA>            PHD
#>   TAPscan_subfamily TAP_class  PlantTFClass_superclass
#> 1              GNAT        TR                     <NA>
#> 2               RKD        TF Helix-turn-helix domains
#> 3               RKD        TF Helix-turn-helix domains
#> 4       MYB-related        TF Helix-turn-helix domains
#> 5              bZIP        TF            Basic domains
#> 6               PHD        TR                     <NA>
#>                    PlantTFClass_class PlantTFClass_family
#> 1                                <NA>                <NA>
#> 2                              RWP-RK              RWP-RK
#> 3                              RWP-RK              RWP-RK
#> 4          Tryptophan cluster factors         MYB-related
#> 5 Basic leucine zipper factors (bZIP)                bZIP
#> 6                                <NA>                <NA>

# Only TAPscan classifications, with TAP classes (TF, TR, or PT)
tapscan_families <- classify_tfs(gsu_annotation, scheme = "TAPscan")
table(tapscan_families$TAP_class)
#> 
#>  PT  TF  TR 
#>   9 143  96 
```
