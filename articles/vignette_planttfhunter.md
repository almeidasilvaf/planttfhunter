# Genome-wide identification and classification of transcription factors in plant genomes

## Introduction

Transcription factors (TFs) are proteins that bind to cis-regulatory
elements in promoter regions of genes and regulate their expression.
Identifying them in a genome is useful for a variety of reasons, such as
exploring their evolutionary history across clades and inferring gene
regulatory networks.
*[planttfhunter](https://github.com/almeidasilvaf/planttfhunter)* allows
users to identify plant TFs from whole-genome protein sequences and
classify them into families and subfamilies (when applicable) using the
classification schemes implemented in
[PlantTFDB](http://planttfdb.gao-lab.org/) and
[TAPscan](https://tapscan.plantcode.cup.uni-freiburg.de/), as well as
into structure-based families, classes, and superclasses as in
Plant-TFClass. As
*[planttfhunter](https://github.com/almeidasilvaf/planttfhunter)*
interoperates with core Bioconductor packages (i.e., `AAStringSet`
objects as input, `SummarizedExperiment` objects as output), it can be
easily incorporated in pipelines for TF identification and
classification in large-scale genomic data sets.

## Installation

You can install
*[planttfhunter](https://github.com/almeidasilvaf/planttfhunter)* with
the following code:

\
`if`` ``(``!`[`requireNamespace`](https://rdrr.io/r/base/ns-load.html)`(``"BiocManager"``, quietly ``=`` ``TRUE``)``)`` ``{`\
`    `[`install.packages`](https://rdrr.io/r/utils/install.packages.html)`(``"BiocManager"``)`\
`}`\
\
`BiocManager``::`[`install`](https://bioconductor.github.io/BiocManager/reference/install.html)`(``"planttfhunter"``)`

Loading package after installation:

\
[`library`](https://rdrr.io/r/base/library.html)`(`[`planttfhunter`](https://github.com/almeidasilvaf/planttfhunter)`)`

## Data description

In this vignette, we will use protein sequences of TFs from the algae
species *Galdieria sulphuraria* as an example, as its proteome is very
small. The proteome file was downloaded from the PLAZA Diatoms database
(Osuna-Cruz et al. 2020), and it was filtered to keep only genes with
TF-related domains for demonstration purposes. The object `gsu` stores
the protein sequences in an `AAStringSet` object.

\
[`data`](https://rdrr.io/r/utils/data.html)`(``gsu``)`\
`gsu`\
`#> AAStringSet object of length 527:`\
`#>       width sequence                                        names               `\
`#>   [1]   383 ``M``S``VLL``QTS``R``G``D``IVV``D``L``F``T``D``LA``P``...``E``K``AL``F``Q``E``HRR``SS``Y``S``K``TS``KR``Y``K``* gsu00340.1`\
`#>   [2]   544 ``MV``ED``H``D``ST``L``N``VV``N``P``Q``K``V``P``S``G``S``R``...``G``R``VII``T``A``G``Y``S``G``E``I``R``I``F``E``N``I``D``L``* gsu04410.1`\
`#>   [3]   537 ``MV``ED``H``D``ST``L``N``VV``N``P``Q``K``V``P``S``G``V``P``...``G``R``VII``T``A``G``Y``S``G``E``I``R``I``F``E``N``I``D``L``* gsu04430.1`\
`#>   [4]   301 ``M``E``S``M``SQT``KR``W``S``C``VI``Y``Q``V``D``S``IL``P``...``P``N``I``S``L``QQ``LL``G``DEDE``I``S``W``S``G``L``P``* gsu04730.1`\
`#>   [5]   232 ``M``E``K``GP``C``S``HK``W``K``Q``L``H``V``G``D``Y``Q``H``D``N``...``P``N``I``S``L``QQ``LL``G``DEDE``I``S``W``S``G``L``P``* gsu05000.1`\
`#>   ...   ... ...`\
`#> [523]   365 ``MI``T``E``V``D``N``P``M``S``F``V``H``S``E``H``F``M``N``Y``SS``...``RRH``S``R``L``P``V``F``QT``L``EE``K``S``D``I``H``S``K``* gsu99270.1`\
`#> [524]   747 ``M``K``NNS``V``SS``G``ED``S``G``NT``V``D``N``A``SS``Y``...``M``F``E``Y``DEED``S``E``A``SN``A``S``D``AAI``N``E``* gsu99880.1`\
`#> [525]   284 ``M``TS``FY``I``KK``G``I``T``F``SS``IV``Y``N``H``N``Y``K``...``L``F``Q``R``II``E``I``N``K``N``Y``N``P``LI``Q``L``Q``R``I``* AIG92462.1`\
`#> [526]   219 ``M``K``Y``K``LLVI``DDE``L``S``I``R``QS``L``KK``Y``L``...``T``F``T``R``S``R``T``E``LV``R``Y``AI``K``NN``LII``E``* AIG92471.1`\
`#> [527]   156 ``ML``DD``TT``LII``K``S``I``P``F``SN``K``E``I``K``V``F``...``FY``KK``L``G``F``VI``E``P``N``K``T``K``VL``F``L``SN``* AIG92585.1`

## Algorithm description

TF identification and classification is based on the presence of
signature protein domains, which are identified using profile hidden
Markov models (HMMs). Three classification schemes are available:

1.  **PlantTFDB** (Jin et al. 2017): TFs are classified into families
    and subfamilies based on DNA-binding, auxiliary, and forbidden
    domains.

2.  **TAPscan v4** (Petroll et al. 2025): transcription-associated
    proteins (TAPs), which include TFs, transcriptional regulators
    (TRs), and putative TAPs (PTs), are classified in over 130 families
    based on rules specifying domains that each family should and should
    not have. Domain hits are identified using gathering thresholds and
    domain-specific coverage cutoffs.
    *[planttfhunter](https://github.com/almeidasilvaf/planttfhunter)*
    uses an R implementation of TAPscan’s original Perl script, which
    reproduces the results of the original implementation.

3.  **Plant-TFClass** (Blanc-Mathieu et al. 2024): TFs are classified
    into families, classes, and superclasses based on the 3D structure
    of their DNA-binding domains. Plant-TFClass classifications are
    inferred from TAPscan classifications using a correspondence table
    between the two systems. Some TAPscan families correspond to more
    than one Plant-TFClass family (e.g., TAPscan’s AP2 family includes
    Plant-TFClass’s AP2, ERF/DREB, and RAV families). In such cases,
    PlantTFDB subfamilies are used to resolve the ambiguity, and if that
    is not possible (e.g., because the gene was not classified by
    PlantTFDB), all possible classifications are reported, separated by
    ‘;’.

In all schemes, classification levels without a more specific level are
filled with the name of the more general level. For example, PlantTFDB
family bHLH has no subfamilies, so its subfamily is also bHLH, and
Plant-TFClass class GRAS has no families, so its family is also GRAS.

The classification schemes are available as data sets in this package,
and you can see them below (click to expand).

PlantTFDB classification scheme (`data(planttfdb_scheme)`)

| Family | Subfamily | DBD | Auxiliary | Forbidden |
|:---|:---|:---|:---|:---|
| AP2/ERF | AP2 | AP2 (\>=2) (PF00847) | NA | NA |
| AP2/ERF | ERF | AP2 (1) (PF00847) | NA | NA |
| AP2/ERF | RAV | AP2 (PF00847) and B3 (PF02362) | NA | NA |
| B3 superfamily | ARF | B3 (PF02362) | Auxin_resp (PF06507) | NA |
| B3 superfamily | B3 | B3 (PF02362) | NA | NA |
| BBR-BPC | BBR-BPC | GAGA_bind (PF06217) | NA | NA |
| BES1 | BES1 | DUF822 (PF05687) | NA | NA |
| bHLH | bHLH | HLH (PF00010) | NA | NA |
| bZIP | bZIP | bZIP_1 (PF00170) | NA | NA |
| C2C2 | CO-like | zf-B_box (PF00643) | CCT (PF06203) | NA |
| C2C2 | Dof | Zf-Dof (PF02701) | NA | NA |
| C2C2 | GATA | GATA-zf (PF00320) | NA | NA |
| C2C2 | LSD | Zf-LSD1 (PF06943) | NA | Peptidase_C14 (PF00656) |
| C2C2 | YABBY | YABBY (PF04690) | NA | NA |
| C2H2 | C2H2 | zf-C2H2 (PF00096) | NA | RNase_T (PF00929) |
| C3H | C3H | Zf-CCCH (PF00642) | NA | RRM_1 (PF00076) or Helicase_C (PF00271) |
| CAMTA | CAMTA | CG1 (PF03859) | NA | NA |
| CPP | CPP | TCR (PF03638) | NA | NA |
| DBB | DBB | zf-B_box (\>=2) (PF00643) | NA | NA |
| E2F/DP | E2F/DP | E2F_TDP (PF02319) | NA | NA |
| EIL | EIL | EIN3 (PF04873) | NA | NA |
| FAR1 | FAR1 | FAR1 (PF03101) | NA | NA |
| GARP | ARR-B | G2-like | Response_reg (PF00072) | NA |
| GARP | G2-like | G2-like | NA | NA |
| GeBP | GeBP | DUF573 (PF04504) | NA | NA |
| GRAS | GRAS | GRAS (PF03514) | NA | NA |
| GRF | GRF | WRC (PF08879) | QLQ (PF08880) | NA |
| HB | HD-ZIP | Homeobox (PF00046) | HD-ZIP_I/II or SMART (PF01852) | NA |
| HB | TALE | Homeobox (PF00046) | BELL or ELK (PF03789) | NA |
| HB | WOX | homeobox (PF00046) | Wus type homeobox | NA |
| HB | HB-PHD | homeobox (PF00046) | PHD (PF00628) | NA |
| HB | HB-other | homeobox (PF00046) | NA | NA |
| HRT-like | HRT-like | HRT-like | NA | NA |
| HSF | HSF | HSF_dna_bind (PF00447) | NA | NA |
| LBD | LBD | DUF260 (PF03195) | NA | NA |
| LFY | LFY | FLO_LFY (PF01698) | NA | NA |
| MADS | M-type | SRF-TF (PF00319) | NA | NA |
| MADS | MIKC | SRF-TF (PF00319) | K-box (PF01486) | NA |
| MYB superfamily | MYB | Myb_dna_bind (\>=2) (PF00249) | NA | SWIRM (PF04433) |
| MYB superfamily | MYB-related | Myb_dna_bind (1) (PF00249) | NA | SWIRM (PF04433) |
| NAC | NAC | NAM (PF02365) | NA | NA |
| NF-X1 | NF-X1 | Zf-NF-X1 (PF01422) | NA | NA |
| NF-Y | NF-YA | CBFB_NFYA (PF02045) | NA | NA |
| NF-Y | NF-YB | NF-YB | NA | NA |
| NF-Y | NF-YC | NF-YC | NA | NA |
| Nin-like | Nin-like | RWP-RK (PF02042) | NA | NA |
| NZZ/SPL | NZZ/SPL | NOZZLE (PF08744) | NA | NA |
| S1Fa-like | S1Fa-like | S1FA (PF04689) | NA | NA |
| SAP | SAP | SAP | NA | NA |
| SBP | SBP | SBP (PF03110) | NA | NA |
| SRS | SRS | DUF702 (PF05142) | NA | NA |
| STAT | STAT | STAT | NA | NA |
| TCP | TCP | TCP (PF03634) | NA | NA |
| Trihelix | Trihelix | Trihelix | NA | NA |
| VOZ | VOZ | VOZ | NA | NA |
| Whirly | Whirly | Whirly (PF08536) | NA | NA |
| WRKY | WRKY | WRKY (PF03106) | NA | NA |
| ZF-HD | ZF-HD | ZF-HD_dimer (PF04770) | NA | NA |

TAPscan classification scheme (`data(tapscan_scheme)`)

Genes are assigned to a family (and subfamily, if any) if they have all
domains in column *Should* and none of the domains in column
*Should_not*. Numbers in parentheses indicate the required number of
copies of a domain.

| Family | Subfamily | Rule | Should | Should_not | TAP_class |
|:---|:---|:---|:---|:---|:---|
| ABI3/VP1 | ABI3/VP1 | ABI3/VP1 | B3 | AP2 or Auxin_resp or WRKY | TF |
| ADA2 | ADA2 | ADA2 | Myb_DNA-binding and zz-ADA2 | NA | TR |
| Alfin-like | Alfin-like | Alfin-like | Alfin-like | Homeobox or PHD or zf-TAZ | TF |
| ALOG | ALOG | ALOG | ALOG | NA | TF |
| AP2 | AP2 | AP2 | AP2 | CRF | TF |
| AP2 | CRF | CRF | AP2 and CRF | NA | TF |
| ARF | ARF | ARF | Auxin_resp | NA | TF |
| Argonaute | Argonaute | Argonaute | PAZ and Piwi | NA | TR |
| ARID | ARID | ARID | ARID | NA | TF |
| Aux/IAA | Aux/IAA | Aux/IAA | AUX_IAA | Auxin_resp or B3 | TR |
| BBR/BPC | BBR/BPC | BBR/BPC | GAGA_bind | NA | TF |
| BES1 | BES1 | BES1 | BES1_N | NA | TF |
| bHLH | bHLH | bHLH | HLH | TCP | TF |
| bHLH | bHLH_TCP | bHLH_TCP | TCP | NA | TF |
| bHSH | bHSH | bHSH | TF_AP-2 | NA | TF |
| BSD domain containing | BSD domain containing | BSD domain containing | BSD | NA | PT |
| bZIP | bZIP | bZIP1 | bZIP_1 | HLH or Homeobox | TF |
| bZIP | bZIP | bZIP2 | bZIP_2 | HLH or Homeobox | TF |
| bZIP | bZIP | bZIPAUREO | bZIP_AUREO | HLH or Homeobox | TF |
| bZIP | bZIP | bZIPCDD | bZIP_CDD | HLH or Homeobox | TF |
| C2C2_CO-like | C2C2_CO-like | C2C2_CO-like | CCT and zf-B_box | GATA or PLATZ or tify | TF |
| C2C2_Dof | C2C2_Dof | C2C2_Dof | zf-Dof | GATA | TF |
| C2C2_GATA | C2C2_GATA | C2C2_GATA | GATA | tify or zf-Dof | TF |
| C2C2_YABBY | C2C2_YABBY | C2C2_YABBY | YABBY | NA | TF |
| C2H2 | C2H2 | C2H2 | zf-C2H2 | C2H2-IDD or zf-MIZ | TF |
| C2H2 | C2H2_IDD | C2H2_IDD | C2H2-IDD and zf-C2H2 | NA | TF |
| C3H | C3H | C3H | zf-CCCH | AP2 or Myb_DNA-binding (2) or Myb_DNA-binding (3) or Myb_DNA-binding (4) or SRF-TF or zf-C2H2 | TF |
| CAMTA | CAMTA | CAMTA | CG-1 and IQ | NA | TF |
| Coactivator p15 | Coactivator p15 | Coactivator p15 | PC4 | NA | TR |
| CPP | CPP | CPP | TCR | NA | TF |
| CSD | CSD | CSD | CSD | NA | TF |
| CudA | CudA | CudA | SH2 and STAT_bind | NA | TF |
| DBP | DBP | DBP | DNC and PP2C | NA | TF |
| DDT | DDT | DDT | DDT | Alfin-like or Homeobox | TR |
| Dicer | Dicer | Dicer | DEAD and Helicase_C and Ribonuclease_3 and dsrm | Piwi | TR |
| DUF246 domain containing/O-FucT | DUF246 domain containing/O-FucT | DUF246 domain containing/O-FucT | O-FucT | NA | PT |
| DUF296 domain containing | DUF296 domain containing | DUF296 domain containing | DUF296 | NA | PT |
| DUF547 domain containing | DUF547 domain containing | DUF547 domain containing | DUF547 | NA | PT |
| DUF632 domain containing | DUF632 domain containing | DUF632 domain containing | DUF632 | NA | PT |
| DUF833 domain containing/TANGO2 | DUF833 domain containing/TANGO2 | DUF833 domain containing/TANGO2 | TANGO2 | NA | PT |
| E2F/DP | E2F/DP | E2F/DP | E2F_TDP | NA | TF |
| EIL | EIL | EIL | EIN3 | NA | TF |
| ET | ET | GIY_YIG | GIY_YIG | NA | TF |
| ET | ET | HRT | HRT | NA | TF |
| FHA | FHA | FHA | FHA | NA | TR |
| GARP_ARR-B | GARP_ARR-B | GARP_ARR-B_G2 | G2-like_Domain and Response_reg | CCT | TF |
| GARP_ARR-B | GARP_ARR-B | GARP_ARR-B_Myb | Myb_DNA-binding and Response_reg | CCT | TF |
| GARP_G2-like | GARP_G2-like | GARP_G2-like | G2-like_Domain | Myb_DNA-binding or Response_reg | TF |
| GeBP | GeBP | GeBP | DUF573 | NA | TF |
| GIF | GIF | GIF | SSXT | NA | TR |
| GRAS | GRAS | GRAS | GRAS | NA | TF |
| GRF | GRF | GRF | QLQ and WRC | NA | TF |
| HAT | CBP | CBP | CBP and zf-TAZ | BTB | TR |
| HAT | GNAT | GNAT | Acetyltransf_1 | PHD | TR |
| HAT | MYST | MYST | zf-MYST | NA | TR |
| HAT | TAFII250 | TAFII250 | DUF3591 | NA | TR |
| HD-LD | HD-LD | HD-LD | LD | NA | TF |
| HD-NDX | HD-NDX | HD-NDX | NDX | NA | TF |
| HD-other | HD-other | HD-other | Homeobox | BEL or EIN3 or PHD or PINTOX or WOX_HD or bZIP_1 | TF |
| HD-SAWADEE | HD-SAWADEE | HD-SAWADEE | SAWADEE | NA | TF |
| HD_DDT | HD_DDT | HD_DDT | DDT and Homeobox and WHIM1 and WSD | NA | TF |
| HD_PHD | HD_PHD | HD_PHD | Homeobox and PHD | NA | TF |
| HD_PINTOX | HD_PINTOX | HD_PINTOX | Homeobox and PINTOX | NA | TF |
| HD_PLINC | HD_PLINC | HD_PLINC | ZF-HD_dimer | NA | TF |
| HD_TALE | HD_TALE | HD_TALE | Homeobox_KN | BEL or KNOX1 or KNOX2 | TF |
| HD_TALE_BEL | HD_TALE_BEL | HD_TALE_BEL | BEL and Homeobox_KN | NA | TF |
| HD_TALE_KNOX1 | HD_TALE_KNOX1 | HD_TALE_KNOX1 | Homeobox_KN and KNOX1 and KNOX2 | KNOXC | TF |
| HD_TALE_KNOX2 | HD_TALE_KNOX2 | HD_TALE_KNOX2 | Homeobox_KN and KNOX1 and KNOX2 and KNOXC | NA | TF |
| HD_WOX | HD_WOX | HD_WOX | WOX_HD | NA | TF |
| HDZ | C1HDZ | C1HDZ | C1HDZ and Homeobox | MEKHLA or START | TF |
| HDZ | C2HDZ | C2HDZ | C2HDZ and Homeobox | MEKHLA or START | TF |
| HDZ | C3HDZ | C3HDZ | Homeobox and MEKHLA and START | NA | TF |
| HDZ | C4HDZ | C4HDZ | Homeobox and START | MEKHLA | TF |
| HMG | HMG | HMG | HMG_box | ARID or YABBY | TR |
| HSF | HSF | HSF | HSF_DNA-bind | NA | TF |
| IWS1 | IWS1 | IWS1 | Med26 | NA | TR |
| Jumonji_Other | Jumonji_Other | Jumonji_Other | JmjC | NA | NA |
| Jumonji_PKDM7 | Jumonji_PKDM7 | Jumonji_PKDM7 | FYRC and FYRN and JmjC and JmjN and zf-C5HC2 | NA | NA |
| LBD | LOB1 | LOB1 | DUF260 | HLH or Homeobox or bZIP_1 or bZIP_2 | TF |
| LBD | LOB2 | LOB2 | LOB2 | NA | TF |
| LDL/FLD | LDL/FLD | LDL/FLD | SWIRM | Myb_DNA-binding | NA |
| LFY | LFY | LFY | FLO_LFY | NA | TF |
| LIM | LIM | LIM | LIM (\>=2) | NA | TF |
| LUG | LUG | LUG | LUFS_Domain | NA | TR |
| MADS | MADS | MADS | SRF-TF | K-box | TF |
| MADS_MIKC | MADS_MIKC | MADS_MIKC | K-box and SRF-TF | NA | TF |
| MBF1 | MBF1 | MBF1 | MBF1 | NA | TR |
| Med6 | Med6 | Med6 | Med6 | NA | TR |
| Med7 | Med7 | Med7 | Med7 | NA | TR |
| mTERF | mTERF | mTERF | mTERF | NA | TR |
| MYB | MYB-2R | MYB-2R | Myb_DNA-binding (2) | G2-like_Domain or Response_reg or trihelix | TF |
| MYB | MYB-3R | MYB-3R | Myb_DNA-binding (3) | G2-like_Domain or Response_reg or trihelix | TF |
| MYB | MYB-4R | MYB-4R | Myb_DNA-binding (4) | G2-like_Domain or Response_reg or trihelix | TF |
| MYB | MYB-related | MYB-related | Myb_DNA-binding | ARID or G2-like_Domain or Myb_DNA-binding (2) or Myb_DNA-binding (3) or Myb_DNA-binding (4) or Response_reg or trihelix | TF |
| MYB-related | SWI/SNF_SWI3 | SWI/SNF_SWI3 | Myb_DNA-binding and SWIRM | NA | TR |
| NAC | NAC | NAC | NAM | NA | TF |
| NFY | NF-YA | NF-YA | CBFB_NFYA | bZIP_1 or bZIP_2 | TF |
| NFY | NF-YB | NF-YB | NF-YB | NF-YC | TF |
| NFY | NF-YC | NF-YC | NF-YC | HMG_box or NF-YB | TF |
| NZZ | NZZ | NZZ | NOZZLE | NA | TF |
| OFP | OFP | OFP | Ovate | NA | TR |
| PcG_EZ | PcG_EZ | PcG_EZ | CXC and SET | NA | NA |
| PcG_FIE | PcG_FIE | PcG_FIE | FIE_clipped_for_HMM and WD40 | NA | NA |
| PcG_MSI | PcG_MSI | PcG_MSI | CAF1C_H4-bd and WD40 | FIE_clipped_for_HMM | NA |
| PcG_VEFS | PcG_VEFS | PcG_VEFS | VEFS-Box | zf-C2H2 | NA |
| PHD | PHD | PHD | PHD | ARID or Alfin-like or DDT or HMG_box or Homeobox or JmjC or JmjN or Myb_DNA-binding or SWIB or zf-CCCH or zf-MIZ or zf-TAZ | TR |
| PLATZ | PLATZ | PLATZ | PLATZ | NA | TF |
| Pseudo ARR-B | Pseudo ARR-B | Pseudo ARR-B | CCT and Response_reg | tify | TF |
| RB | RB | RB | RB_B | NA | TF |
| Rcd1-like | Rcd1-like | Rcd1-like | Rcd1 | NA | TR |
| Rel | Rel | Rel | RHD_DNA_bind | NA | TF |
| RF-X | RF-X | RF-X | RFX_DNA_binding | NA | TF |
| RRN3 | RRN3 | RRN3 | RRN3 | NA | TR |
| Runt | Runt | Runt | Runt | NA | TF |
| RWP-RK | NLP | NLP | NLP and RWP-RK | NA | TF |
| RWP-RK | RKD | RKD | RWP-RK | NLP | TF |
| S1Fa-like | S1Fa-like | S1Fa-like | S1FA | NA | TF |
| SAP | SAP | SAP | STER_AP | NA | TF |
| SBP | SBP | SBP | SBP | NA | TF |
| SET | SET | SET | SET | CXC or Myb_DNA-binding or PHD or TCR or zf-C2H2 | TR |
| Sigma70-like | Sigma70-like | Sigma70-like | Sigma70_r2 and Sigma70_r3 and Sigma70_r4 | NA | TR |
| Sin3 | Sin3 | Sin3 | PAH | WRKY | TR |
| Sir2 | Sir2 | Sir2 | SIR2 | NA | TF |
| SOH1 | SOH1 | SOH1 | Med31 | NA | TR |
| SRS | SRS | SRS | DUF702 | NA | TF |
| SWI/SNF_BAF60b | SWI/SNF_BAF60b | SWI/SNF_BAF60b | SWIB | NA | TR |
| SWI/SNF_SNF2 | SWI/SNF_SNF2 | SWI/SNF_SNF2 | SNF2_N | AP2 or HMG_box or Myb_DNA-binding or PHD or zf-CCCH | TR |
| TEA | TEA | TEA | TEA | NA | NA |
| TFb2 | TFb2 | TFb2 | Tfb2 | NA | TR |
| tify | tify | tify | tify | NA | TF |
| TRAF | TRAF | TRAF | BTB | zf-TAZ | TR |
| Trihelix | Trihelix | Trihelix | trihelix | NA | TF |
| TUB | TUB | TUB | Tub | NA | TF |
| ULT | ULT | ULT | ULT_Domain | NA | TF |
| VARL | VARL | VARL | VARL | NA | TF |
| VOZ | VOZ | VOZ | VOZ_Domain | NA | TF |
| Whirly | Whirly | Whirly | Whirly | NA | TF |
| WRKY | WRKY | WRKY | WRKY | NA | TF |
| Zinc finger, AN1 and A20 type | Zinc finger, AN1 and A20 type | Zinc finger, AN1 and A20 type | zf-AN1 | zf-C2H2 | TR |
| Zinc finger, MIZ type | Zinc finger, MIZ type | Zinc finger, MIZ type | zf-MIZ | zf-C2H2 | TF |
| Zinc finger, ZPR1 | Zinc finger, ZPR1 | Zinc finger, ZPR1 | zf-ZPR1 | NA | TR |
| Zn_clus | Zn_clus | Zn_clus | Zn_clus | NA | TF |
| ZPR | ZPR | ZPR | ZPR | NA | TR |

Correspondence between TAPscan and Plant-TFClass
(`data(planttfclass_scheme)`)

TAPscan families/subfamilies with multiple rows correspond to more than
one Plant-TFClass family, and the PlantTFDB subfamily of each gene
(column *PlantTFDB_subfamily*) is used to choose among them.

| TAPscan_family | TAPscan_subfamily | PlantTFClass_superclass | PlantTFClass_class | PlantTFClass_family | PlantTFDB_subfamily |
|:---|:---|:---|:---|:---|:---|
| MADS | MADS | Alpha-helices exposed by beta-structures | MADS box factors | Type I | M-type |
| MADS | MADS | Alpha-helices exposed by beta-structures | MADS box factors | Type II | MIKC |
| MADS_MIKC | MADS_MIKC | Alpha-helices exposed by beta-structures | MADS box factors | Type II | MIKC |
| BES1 | BES1 | Basic domains | Basic helix-loop-helix factors (bHLH) | BES/BZR | NA |
| bHLH | bHLH | Basic domains | Basic helix-loop-helix factors (bHLH) | bHLH | NA |
| bZIP | bZIP | Basic domains | Basic leucine zipper factors (bZIP) | bZIP | NA |
| ARF | ARF | Beta-barrel DNA-binding domains | B3 | ARF | NA |
| ABI3/VP1 | ABI3/VP1 | Beta-barrel DNA-binding domains | B3 | LAV | B3 |
| ABI3/VP1 | ABI3/VP1 | Beta-barrel DNA-binding domains | B3 | RAV | RAV |
| AP2 | AP2 | Beta-barrel DNA-binding domains | B3 | RAV | RAV |
| AP2 | CRF | Beta-barrel DNA-binding domains | B3 | RAV | RAV |
| AP2 | AP2 | Beta-hairpin exposed by an alpha/beta-scaffold | AP2/EREBP | AP2 | AP2 |
| AP2 | CRF | Beta-hairpin exposed by an alpha/beta-scaffold | AP2/EREBP | AP2 | AP2 |
| AP2 | AP2 | Beta-hairpin exposed by an alpha/beta-scaffold | AP2/EREBP | ERF/DREB | ERF |
| AP2 | CRF | Beta-hairpin exposed by an alpha/beta-scaffold | AP2/EREBP | ERF/DREB | ERF |
| CAMTA | CAMTA | Beta-hairpin exposed by an alpha/beta-scaffold | GCM domain factors | CAMTA | NA |
| NAC | NAC | Beta-hairpin exposed by an alpha/beta-scaffold | GCM domain factors | NAC | NA |
| VOZ | VOZ | Beta-hairpin exposed by an alpha/beta-scaffold | GCM domain factors | VOZ | NA |
| WRKY | WRKY | Beta-hairpin exposed by an alpha/beta-scaffold | GCM domain factors | WRKY | NA |
| DUF296 domain containing | DUF296 domain containing | Beta-sheet binding to DNA | A.T hook factors | AHL | NA |
| bHLH | bHLH_TCP | Beta-sheet binding to DNA | TCP | TCP | NA |
| ARID | ARID | Helix-turn-helix domains | ARID | ARID | NA |
| HMG | HMG | Helix-turn-helix domains | ARID | ARID | NA |
| E2F/DP | E2F/DP | Helix-turn-helix domains | Fork head / winged helix factors | E2F | NA |
| HSF | HSF | Helix-turn-helix domains | Heat shock factors | HSF | NA |
| DDT | DDT | Helix-turn-helix domains | Homeo domain factors | DDT | NA |
| HDZ | C1HDZ | Helix-turn-helix domains | Homeo domain factors | HD-ZIP | NA |
| HDZ | C2HDZ | Helix-turn-helix domains | Homeo domain factors | HD-ZIP | NA |
| HDZ | C3HDZ | Helix-turn-helix domains | Homeo domain factors | HD-ZIP | NA |
| HDZ | C4HDZ | Helix-turn-helix domains | Homeo domain factors | HD-ZIP | NA |
| HD-LD | HD-LD | Helix-turn-helix domains | Homeo domain factors | LD | NA |
| HD_PHD | HD_PHD | Helix-turn-helix domains | Homeo domain factors | PHD | NA |
| HD_PINTOX | HD_PINTOX | Helix-turn-helix domains | Homeo domain factors | PINTOX | NA |
| HD_PLINC | HD_PLINC | Helix-turn-helix domains | Homeo domain factors | PLINC | NA |
| HD-SAWADEE | HD-SAWADEE | Helix-turn-helix domains | Homeo domain factors | SAWADEE | NA |
| HD_TALE | HD_TALE | Helix-turn-helix domains | Homeo domain factors | TALE-type HD | NA |
| HD_TALE_BEL | HD_TALE_BEL | Helix-turn-helix domains | Homeo domain factors | TALE-type HD | NA |
| HD_TALE_KNOX1 | HD_TALE_KNOX1 | Helix-turn-helix domains | Homeo domain factors | TALE-type HD | NA |
| HD_TALE_KNOX2 | HD_TALE_KNOX2 | Helix-turn-helix domains | Homeo domain factors | TALE-type HD | NA |
| HD_WOX | HD_WOX | Helix-turn-helix domains | Homeo domain factors | WOX | NA |
| LFY | LFY | Helix-turn-helix domains | LEAFY | LEAFY | NA |
| RWP-RK | NLP | Helix-turn-helix domains | RWP-RK | RWP-RK | NA |
| RWP-RK | RKD | Helix-turn-helix domains | RWP-RK | RWP-RK | NA |
| GARP_ARR-B | GARP_ARR-B | Helix-turn-helix domains | Tryptophan cluster factors | GARP_ARR-B | NA |
| GARP_G2-like | GARP_G2-like | Helix-turn-helix domains | Tryptophan cluster factors | GARP_G2-like | NA |
| MYB | MYB-2R | Helix-turn-helix domains | Tryptophan cluster factors | MYB | NA |
| MYB | MYB-3R | Helix-turn-helix domains | Tryptophan cluster factors | MYB | NA |
| MYB | MYB-4R | Helix-turn-helix domains | Tryptophan cluster factors | MYB | NA |
| MYB | MYB-related | Helix-turn-helix domains | Tryptophan cluster factors | MYB-related | NA |
| MYB-related | SWI/SNF_SWI3 | Helix-turn-helix domains | Tryptophan cluster factors | MYB-related | NA |
| GeBP | GeBP | Helix-turn-helix domains | Tryptophan cluster factors | Storekeeper | NA |
| Trihelix | Trihelix | Helix-turn-helix domains | Tryptophan cluster factors | Trihelix | NA |
| EIL | EIL | Other all-alpha-helical DNA-binding domains | EIL | EIL | NA |
| C2C2_CO-like | C2C2_CO-like | Other all-alpha-helical DNA-binding domains | Heteromeric CCAAT-binding factors | CONSTANS | NA |
| NFY | NF-YB | Other all-alpha-helical DNA-binding domains | Heteromeric CCAAT-binding factors | NF-YA | NA |
| NFY | NF-YA | Other all-alpha-helical DNA-binding domains | Heteromeric CCAAT-binding factors | NF-YA | NA |
| C2C2_YABBY | C2C2_YABBY | Other all-alpha-helical DNA-binding domains | High-mobility group (HMG) domain factors | YABBY | NA |
| BBR/BPC | BBR/BPC | Yet undefined DNA-binding domains | BBR/BPC | BBR/BPC | NA |
| CPP | CPP | Yet undefined DNA-binding domains | CPP | CPP | NA |
| DBP | DBP | Yet undefined DNA-binding domains | DBP | DBP | NA |
| GRAS | GRAS | Yet undefined DNA-binding domains | GRAS | GRAS | NA |
| PLATZ | PLATZ | Yet undefined DNA-binding domains | PLATZ | PLATZ | NA |
| S1Fa-like | S1Fa-like | Yet undefined DNA-binding domains | S1Fa-like | S1Fa-like | NA |
| C2H2 | C2H2 | Zinc-coordinating DNA-binding domains | C2H2 zinc finger factors | C2H2 | NA |
| C2H2 | C2H2_IDD | Zinc-coordinating DNA-binding domains | C2H2 zinc finger factors | IDD | NA |
| GRF | GRF | Zinc-coordinating DNA-binding domains | C3H zinc finger factors | GRF | NA |
| SBP | SBP | Zinc-coordinating DNA-binding domains | C3H(C),C2HC zinc fingers-like factors | SBP | NA |
| ALOG | ALOG | Zinc-coordinating DNA-binding domains | HC3 zinc ribbon factors | ALOG | NA |
| C2C2_GATA | C2C2_GATA | Zinc-coordinating DNA-binding domains | Other C4 zinc finger-type factors | C4-GATA-related | NA |
| tify | tify | Zinc-coordinating DNA-binding domains | Other C4 zinc finger-type factors | C4-GATA-related | NA |
| C2C2_Dof | C2C2_Dof | Zinc-coordinating DNA-binding domains | Other C4 zinc finger-type factors | DOF | NA |
| LBD | LOB1 | Zinc-coordinating DNA-binding domains | Other C4 zinc finger-type factors | LBD | NA |
| LBD | LOB2 | Zinc-coordinating DNA-binding domains | Other C4 zinc finger-type factors | LBD | NA |

## Identifying and classifying TFs

To identify TFs from protein sequence data, you will use the function
[`annotate_domains()`](../reference/annotate_domains.md). This function
takes as input an `AAStringSet` object [^1] and returns a list of two
data frames (`PlantTFDB` and `TAPscan`) with the protein domains used by
each classification scheme. The HMMER program (Finn et al. 2011) is used
to scan protein sequences for the presence of DNA-binding protein
domains, as well as auxiliary and forbidden domains. Pre-built HMM
profiles can be found in the *extdata/* directory of this package. If
you have a multicore machine, you can speed up HMMER searches by
increasing the number of threads with the argument `threads`.

This is how you can run
[`annotate_domains()`](../reference/annotate_domains.md) [^2]:

\
[`data`](https://rdrr.io/r/utils/data.html)`(``gsu_annotation``)`\
\
`# Annotate TF-related domains using a local installation of HMMER`\
`if``(`[`hmmer_is_installed`](../reference/hmmer_is_installed.md)`(``)``)`` ``{`\
`    ``gsu_annotation`` ``<-`` `[`annotate_domains`](../reference/annotate_domains.md)`(``gsu``)`\
`}`` `\
\
`# Take a look at the first few lines of the output`\
[`lapply`](https://rdrr.io/r/base/lapply.html)`(``gsu_annotation``, ``head``)`\
`#> $PlantTFDB`\
`#>          Gene  Domain qlen c_evalue hmm_from hmm_to`\
`#> 1 gsu144370.1 PF00010   53  2.6e-15        2     50`\
`#> 2 gsu140730.1 PF00010   53  1.9e-10        1     53`\
`#> 3  gsu74290.1 PF00010   53  1.5e-07       10     46`\
`#> 4 gsu127100.1 PF00046   57  7.9e-13        7     57`\
`#> 5 gsu109490.1 PF00046   57  1.2e-12        3     54`\
`#> 6  AIG92462.1 PF00072  111  2.3e-34        1    111`\
`#> `\
`#> $TAPscan`\
`#>          Gene         Domain qlen c_evalue hmm_from hmm_to`\
`#> 1 gsu109490.1    Homeobox_KN   40  7.8e-24        1     40`\
`#> 2 gsu127100.1    Homeobox_KN   40  4.6e-19        1     40`\
`#> 3 gsu139710.1    Homeobox_KN   40  1.6e-13        1     33`\
`#> 4  gsu89160.1 Acetyltransf_1  117  4.9e-21        9    116`\
`#> 5  gsu14910.1 Acetyltransf_1  117  1.4e-18       19    117`\
`#> 6 gsu119600.1 Acetyltransf_1  117  1.4e-18       23    117`

Now that we have our TF-related domains, we can classify TFs in families
with the function [`classify_tfs()`](../reference/classify_tfs.md).

\
`# Classify TFs into families`\
`gsu_families`` ``<-`` `[`classify_tfs`](../reference/classify_tfs.md)`(``gsu_annotation``)`\
\
`# Take a look at the output`\
[`head`](https://rdrr.io/r/utils/head.html)`(``gsu_families``)`\
`#>         Gene     PlantTFDB_family PlantTFDB_subfamily TAPscan_family`\
`#> 1 AIG92585.1                 <NA>                <NA>            HAT`\
`#> 2 gsu04730.1             Nin-like            Nin-like         RWP-RK`\
`#> 3 gsu05000.1             Nin-like            Nin-like         RWP-RK`\
`#> 4 gsu06140.1 GARP;MYB superfamily G2-like;MYB-related            MYB`\
`#> 5 gsu06730.1                 <NA>                <NA>           bZIP`\
`#> 6 gsu07030.1                 <NA>                <NA>            PHD`\
`#>   TAPscan_subfamily TAP_class  PlantTFClass_superclass`\
`#> 1              GNAT        TR                     <NA>`\
`#> 2               RKD        TF Helix-turn-helix domains`\
`#> 3               RKD        TF Helix-turn-helix domains`\
`#> 4       MYB-related        TF Helix-turn-helix domains`\
`#> 5              bZIP        TF            Basic domains`\
`#> 6               PHD        TR                     <NA>`\
`#>                    PlantTFClass_class PlantTFClass_family`\
`#> 1                                <NA>                <NA>`\
`#> 2                              RWP-RK              RWP-RK`\
`#> 3                              RWP-RK              RWP-RK`\
`#> 4          Tryptophan cluster factors         MYB-related`\
`#> 5 Basic leucine zipper factors (bZIP)                bZIP`\
`#> 6                                <NA>                <NA>`

The output contains one row per gene, and genes that are not classified
by one of the schemes have `NA` in the respective columns. As
Plant-TFClass classifications are inferred from TAPscan classifications,
TAPscan annotation is required to obtain them. As TAPscan’s scheme also
includes other transcription-associated proteins (e.g., transcriptional
regulators), more genes are usually classified with TAPscan.

If you are interested in a single classification scheme, you can use the
argument `scheme` to get only the classifications of that scheme. For
TAPscan, the class of each family (TF, TR, or PT) is indicated in the
column `TAP_class`.

\
`# Get TAPscan classifications only`\
`tapscan_families`` ``<-`` `[`classify_tfs`](../reference/classify_tfs.md)`(``gsu_annotation``, scheme ``=`` ``"TAPscan"``)`\
[`head`](https://rdrr.io/r/utils/head.html)`(``tapscan_families``)`\
`#>         Gene TAPscan_family TAPscan_subfamily TAP_class`\
`#> 1 AIG92585.1            HAT              GNAT        TR`\
`#> 2 gsu04730.1         RWP-RK               RKD        TF`\
`#> 3 gsu05000.1         RWP-RK               RKD        TF`\
`#> 4 gsu06140.1            MYB       MYB-related        TF`\
`#> 5 gsu06730.1           bZIP              bZIP        TF`\
`#> 6 gsu07030.1            PHD               PHD        TR`\
\
`# Number of TFs, TRs, and PTs`\
[`table`](https://rdrr.io/r/base/table.html)`(``tapscan_families``$``TAP_class``)`\
`#> `\
`#>  PT  TF  TR `\
`#>   9 143  96`

TAP classes, as well as descriptions and references for each TAPscan
family, are available in the data set `tap_class`.

\
[`data`](https://rdrr.io/r/utils/data.html)`(``tap_class``)`\
[`head`](https://rdrr.io/r/utils/head.html)`(``tap_class``[``, `[`c`](https://rdrr.io/r/base/c.html)`(``"TAP_family"``, ``"TAP_class"``)``]``)`\
`#>   TAP_family TAP_class`\
`#> 1   ABI3/VP1        TF`\
`#> 2        AP2        TF`\
`#> 3        ARF        TF`\
`#> 4       ARID        TF`\
`#> 5 Alfin-like        TF`\
`#> 6  Argonaute        TR`

## Counting TFs per family in multiple species at once

If you want to get TF counts per family for multiple species, you can
use the function [`get_tf_counts()`](../reference/get_tf_counts.md).
This function takes a named list of data frames with TF classifications
for each species (as returned by
[`classify_tfs()`](../reference/classify_tfs.md)) as input, and it
returns a `SummarizedExperiment` object containing TF counts per family
in each species, as well as species metadata (optional). If you are not
familiar with the `SummarizedExperiment` class, you should consider
checking the vignettes of the
*[SummarizedExperiment](https://bioconductor.org/packages/3.24/SummarizedExperiment)*
Bioconductor package.

For real data sets, you would first identify and classify TFs in each
species. For instance, if you have a list of `AAStringSet` objects with
the proteomes of multiple species [^3], you could run:

\
`families`` ``<-`` `[`lapply`](https://rdrr.io/r/base/lapply.html)`(``proteomes``, ``function``(``x``)`` `[`classify_tfs`](../reference/classify_tfs.md)`(`[`annotate_domains`](../reference/annotate_domains.md)`(``x``)``)``)`

To demonstrate how [`get_tf_counts()`](../reference/get_tf_counts.md)
works, we will simulate TF classifications for 4 species by sampling 100
random genes from the TF classifications we obtained above
(`gsu_families`) 4 times.

\
[`set.seed`](https://rdrr.io/r/base/Random.html)`(``123``)`` ``# for reproducibility`\
\
`# Simulate 4 different species by sampling 100 random genes `\
`families`` ``<-`` `[`list`](https://rdrr.io/r/base/list.html)`(`\
`    Gsu1 ``=`` ``gsu_families``[`[`sample`](https://rdrr.io/r/base/sample.html)`(`[`nrow`](https://rdrr.io/r/base/nrow.html)`(``gsu_families``)``, ``100``)``, ``]``,`\
`    Gsu2 ``=`` ``gsu_families``[`[`sample`](https://rdrr.io/r/base/sample.html)`(`[`nrow`](https://rdrr.io/r/base/nrow.html)`(``gsu_families``)``, ``100``)``, ``]``,`\
`    Gsu3 ``=`` ``gsu_families``[`[`sample`](https://rdrr.io/r/base/sample.html)`(`[`nrow`](https://rdrr.io/r/base/nrow.html)`(``gsu_families``)``, ``100``)``, ``]``,`\
`    Gsu4 ``=`` ``gsu_families``[`[`sample`](https://rdrr.io/r/base/sample.html)`(`[`nrow`](https://rdrr.io/r/base/nrow.html)`(``gsu_families``)``, ``100``)``, ``]`\
`)`

Now, let’s also create a simulated species metadata data frame for each
“species”.

\
`# Create simulated species metadata`\
`species_metadata`` ``<-`` `[`data.frame`](https://rdrr.io/r/base/data.frame.html)`(`\
`    row.names ``=`` `[`names`](https://rdrr.io/r/base/names.html)`(``families``)``,`\
`    Division ``=`` ``"Rhodophyta"``,`\
`    Origin ``=`` `[`c`](https://rdrr.io/r/base/c.html)`(``"US"``, ``"Belgium"``, ``"China"``, ``"Brazil"``)`\
`)`\
\
`species_metadata`\
`#>        Division  Origin`\
`#> Gsu1 Rhodophyta      US`\
`#> Gsu2 Rhodophyta Belgium`\
`#> Gsu3 Rhodophyta   China`\
`#> Gsu4 Rhodophyta  Brazil`

You can add as many columns as you want to the species metadata data
frame, but make sure that **species names are in row names**, and that
`names(families)` match `rownames(species_metadata)`, otherwise
[`get_tf_counts()`](../reference/get_tf_counts.md) will return an error.

Now that we have a list of TF classifications and species metadata, we
can execute [`get_tf_counts()`](../reference/get_tf_counts.md). By
default, TFs are counted per PlantTFDB family, but you can choose other
schemes with the argument `scheme`, and other classification levels with
the argument `level`. Possible levels are ‘family’ and ‘subfamily’ for
PlantTFDB and TAPscan, and ‘superclass’, ‘class’, and ‘family’ for
Plant-TFClass.

\
`# Get TF counts per PlantTFDB family in each species`\
`tf_counts`` ``<-`` `[`get_tf_counts`](../reference/get_tf_counts.md)`(``families``, ``species_metadata``)`\
\
`# Take a look at the SummarizedExperiment object`\
`tf_counts`\
`#> class: SummarizedExperiment `\
`#> dim: 14 4 `\
`#> metadata(0):`\
`#> assays(1): counts`\
`#> rownames(14): bHLH bZIP ... NF-Y Nin-like`\
`#> rowData names(0):`\
`#> colnames(4): Gsu1 Gsu2 Gsu3 Gsu4`\
`#> colData names(2): Division Origin`\
\
`# Look at the matrix of counts: assay() function from SummarizedExperiment`\
`SummarizedExperiment``::`[`assay`](https://rdrr.io/pkg/SummarizedExperiment/man/SummarizedExperiment-class.html)`(``tf_counts``)`\
`#>                 Gsu1 Gsu2 Gsu3 Gsu4`\
`#> bHLH               1    0    2    0`\
`#> bZIP               5    7    7    5`\
`#> C2C2               5    9    8    6`\
`#> C2H2               3    0    2    4`\
`#> C3H                4    6    5    3`\
`#> CPP                2    0    2    1`\
`#> E2F/DP             2    3    1    8`\
`#> GARP               1    0    2    0`\
`#> HSF                3    2    2    2`\
`#> MADS               0    1    1    0`\
`#> MYB superfamily   16   14   15   16`\
`#> NF-X1              1    0    0    1`\
`#> NF-Y               3    4    5    5`\
`#> Nin-like           3    2    4    6`\
\
`# Look at the species metadata: colData() function from SummarizedExperiment`\
`SummarizedExperiment``::`[`colData`](https://rdrr.io/pkg/SummarizedExperiment/man/SummarizedExperiment-class.html)`(``tf_counts``)`\
`#> DataFrame with 4 rows and 2 columns`\
`#>         Division      Origin`\
`#>      <character> <character>`\
`#> Gsu1  Rhodophyta          US`\
`#> Gsu2  Rhodophyta     Belgium`\
`#> Gsu3  Rhodophyta       China`\
`#> Gsu4  Rhodophyta      Brazil`

When counting TFs at levels below the highest level of a scheme (e.g.,
subfamilies), the `rowData` of the output contains the higher levels of
each row, which you can use to aggregate counts. Here is an example with
PlantTFDB subfamilies:

\
`# Get TF counts per PlantTFDB subfamily`\
`subfam_counts`` ``<-`` `[`get_tf_counts`](../reference/get_tf_counts.md)`(`\
`    ``families``, scheme ``=`` ``"PlantTFDB"``, level ``=`` ``"subfamily"`\
`)`\
\
`# Look at the family of each subfamily`\
[`head`](https://rdrr.io/r/utils/head.html)`(``SummarizedExperiment``::`[`rowData`](https://rdrr.io/pkg/SummarizedExperiment/man/SummarizedExperiment-class.html)`(``subfam_counts``)``)`\
`#> DataFrame with 6 rows and 1 column`\
`#>         PlantTFDB_family`\
`#>              <character>`\
`#> bHLH                bHLH`\
`#> bZIP                bZIP`\
`#> C2H2                C2H2`\
`#> C3H                  C3H`\
`#> CO-like             C2C2`\
`#> CPP                  CPP`

Since [`get_tf_counts()`](../reference/get_tf_counts.md) takes
classification tables as input, you can also filter them before
counting. For example, here is how you can get counts of TAPscan TFs
(excluding TRs and PTs) per subfamily:

\
`# Keep only TFs in TAPscan classifications`\
`tapscan_tfs`` ``<-`` `[`lapply`](https://rdrr.io/r/base/lapply.html)`(``families``, ``function``(``x``)`` ``x``[``x``$``TAP_class`` `[`%in%`](https://rdrr.io/r/base/match.html)` ``"TF"``, ``]``)`\
\
`# Get TF counts per TAPscan subfamily`\
`tapscan_counts`` ``<-`` `[`get_tf_counts`](../reference/get_tf_counts.md)`(`\
`    ``tapscan_tfs``, scheme ``=`` ``"TAPscan"``, level ``=`` ``"subfamily"`\
`)`\
[`head`](https://rdrr.io/r/utils/head.html)`(``SummarizedExperiment``::`[`assay`](https://rdrr.io/pkg/SummarizedExperiment/man/SummarizedExperiment-class.html)`(``tapscan_counts``)``)`\
`#>           Gsu1 Gsu2 Gsu3 Gsu4`\
`#> ARID         0    3    2    1`\
`#> bHLH         1    0    2    0`\
`#> bZIP         5    9    9    6`\
`#> C2C2_GATA    3    5    6    3`\
`#> C2H2         3    0    1    4`\
`#> C3H          4    6    3    3`

Finally, here is how you can get TF counts per Plant-TFClass superclass:

\
`# Get TF counts per Plant-TFClass superclass`\
`superclass_counts`` ``<-`` `[`get_tf_counts`](../reference/get_tf_counts.md)`(`\
`    ``families``, scheme ``=`` ``"PlantTFClass"``, level ``=`` ``"superclass"`\
`)`\
`SummarizedExperiment``::`[`assay`](https://rdrr.io/pkg/SummarizedExperiment/man/SummarizedExperiment-class.html)`(``superclass_counts``)`\
`#>                                             Gsu1 Gsu2 Gsu3 Gsu4`\
`#> Alpha-helices exposed by beta-structures       0    1    1    0`\
`#> Basic domains                                  6    9   11    6`\
`#> Helix-turn-helix domains                      26   24   25   33`\
`#> Other all-alpha-helical DNA-binding domains    0    2    2    3`\
`#> Yet undefined DNA-binding domains              2    0    2    1`\
`#> Zinc-coordinating DNA-binding domains          6    5    7    7`

Cool, huh? In real-world analyses, once you have TF counts per family in
multiple species obtained with
[`get_tf_counts()`](../reference/get_tf_counts.md), you can try to find
associations between TF counts and eco-evolutionary aspects or traits of
each species (e.g., higher frequencies of a stress-related TF family in
a species that inhabits a stressful environment).

## Session information

This document was created under the following conditions:

\
`sessioninfo``::`[`session_info`](https://sessioninfo.r-lib.org/reference/session_info.html)`(``)`\
`#> ``─ Session info ───────────────────────────────────────────────────────────────`\
`#>  ``setting `` ``value`\
`#>  version  R version 4.6.1 (2026-06-24)`\
`#>  os       Ubuntu 24.04.4 LTS`\
`#>  system   x86_64, linux-gnu`\
`#>  ui       X11`\
`#>  language en`\
`#>  collate  en_US.UTF-8`\
`#>  ctype    en_US.UTF-8`\
`#>  tz       UTC`\
`#>  date     2026-10-06`\
`#>  pandoc   3.11 @ /usr/bin/ (via rmarkdown)`\
`#>  quarto   1.10.18 @ /usr/local/bin/quarto`\
`#> `\
`#> ``─ Packages ───────────────────────────────────────────────────────────────────`\
`#>  ``package             `` ``*`` ``version`` ``date (UTC)`` ``lib`` ``source`\
`#>  abind                  1.4-8   ``2024-09-12`` ``[1]`` ``RSPM (R 4.6.0)`\
`#>  Biobase                2.73.2  ``2026-07-29`` ``[1]`` ``Bioconductor 3.24 (R 4.6.1)`\
`#>  BiocGenerics           0.59.12 ``2026-08-11`` ``[1]`` ``Bioconductor 3.24 (R 4.6.1)`\
`#>  BiocManager            1.30.27 ``2025-11-14`` ``[1]`` ``RSPM (R 4.6.0)`\
`#>  BiocStyle            * 2.41.0  ``2026-04-28`` ``[1]`` ``Bioconductor 3.24 (R 4.6.1)`\
`#>  Biostrings             2.81.9  ``2026-09-06`` ``[1]`` ``Bioconductor 3.24 (R 4.6.1)`\
`#>  bookdown               0.48    ``2026-08-28`` ``[1]`` ``RSPM (R 4.6.0)`\
`#>  bslib                  0.12.0  ``2026-08-04`` ``[2]`` ``RSPM (R 4.6.0)`\
`#>  cachem                 1.1.0   ``2024-05-16`` ``[2]`` ``RSPM (R 4.6.0)`\
`#>  cli                    3.6.6   ``2026-04-09`` ``[2]`` ``RSPM (R 4.6.0)`\
`#>  crayon                 1.5.3   ``2024-06-20`` ``[2]`` ``RSPM (R 4.6.0)`\
`#>  DelayedArray           0.39.8  ``2026-09-30`` ``[1]`` ``Bioconductor 3.24 (R 4.6.1)`\
`#>  desc                   1.4.3   ``2023-12-10`` ``[2]`` ``RSPM (R 4.6.0)`\
`#>  digest                 0.6.39  ``2025-11-19`` ``[2]`` ``RSPM (R 4.6.0)`\
`#>  evaluate               1.0.5   ``2025-08-27`` ``[2]`` ``RSPM (R 4.6.0)`\
`#>  fastmap                1.2.0   ``2024-05-15`` ``[2]`` ``RSPM (R 4.6.0)`\
`#>  fs                     2.1.0   ``2026-04-18`` ``[2]`` ``RSPM (R 4.6.0)`\
`#>  generics               0.1.4   ``2025-05-09`` ``[1]`` ``RSPM (R 4.6.0)`\
`#>  GenomicRanges          1.65.4  ``2026-09-02`` ``[1]`` ``Bioconductor 3.24 (R 4.6.1)`\
`#>  htmltools              0.5.9   ``2025-12-04`` ``[2]`` ``RSPM (R 4.6.0)`\
`#>  htmlwidgets            1.6.4   ``2023-12-06`` ``[2]`` ``RSPM (R 4.6.0)`\
`#>  IRanges                2.47.5  ``2026-08-27`` ``[1]`` ``Bioconductor 3.24 (R 4.6.1)`\
`#>  jquerylib              0.1.4   ``2021-04-26`` ``[2]`` ``RSPM (R 4.6.0)`\
`#>  jsonlite               2.0.0   ``2025-03-27`` ``[2]`` ``RSPM (R 4.6.0)`\
`#>  knitr                  1.52    ``2026-09-06`` ``[2]`` ``RSPM (R 4.6.0)`\
`#>  lattice                0.23-1  ``2026-08-12`` ``[2]`` ``RSPM (R 4.6.0)`\
`#>  lifecycle              1.0.5   ``2026-01-08`` ``[2]`` ``RSPM (R 4.6.0)`\
`#>  Matrix                 1.7-6   ``2026-07-25`` ``[2]`` ``RSPM (R 4.6.0)`\
`#>  MatrixGenerics         1.25.0  ``2026-04-28`` ``[1]`` ``Bioconductor 3.24 (R 4.6.1)`\
`#>  matrixStats            1.5.0   ``2025-01-07`` ``[1]`` ``RSPM (R 4.6.0)`\
`#>  otel                   0.2.0   ``2025-08-29`` ``[2]`` ``RSPM (R 4.6.0)`\
`#>  pkgdown                2.2.1   ``2026-07-07`` ``[1]`` ``RSPM (R 4.6.0)`\
`#>  planttfhunter        * 1.13.1  ``2026-10-06`` ``[1]`` ``Bioconductor`\
`#>  R6                     2.6.1   ``2025-02-15`` ``[2]`` ``RSPM (R 4.6.0)`\
`#>  ragg                   1.5.2   ``2026-03-23`` ``[2]`` ``RSPM (R 4.6.0)`\
`#>  rlang                  1.3.0   ``2026-07-05`` ``[2]`` ``RSPM (R 4.6.0)`\
`#>  rmarkdown              2.32    ``2026-09-01`` ``[1]`` ``RSPM (R 4.6.0)`\
`#>  S4Arrays               1.13.2  ``2026-09-30`` ``[1]`` ``Bioconductor 3.24 (R 4.6.1)`\
`#>  S4Vectors              0.51.10 ``2026-09-16`` ``[1]`` ``Bioconductor 3.24 (R 4.6.1)`\
`#>  sass                   0.4.10  ``2025-04-11`` ``[2]`` ``RSPM (R 4.6.0)`\
`#>  Seqinfo                1.3.2   ``2026-08-27`` ``[1]`` ``Bioconductor 3.24 (R 4.6.1)`\
`#>  sessioninfo            1.2.4   ``2026-06-04`` ``[2]`` ``RSPM (R 4.6.0)`\
`#>  SparseArray            1.13.4  ``2026-09-30`` ``[1]`` ``Bioconductor 3.24 (R 4.6.1)`\
`#>  SummarizedExperiment   1.43.0  ``2026-04-28`` ``[1]`` ``Bioconductor 3.24 (R 4.6.1)`\
`#>  systemfonts            1.3.2   ``2026-03-05`` ``[2]`` ``RSPM (R 4.6.0)`\
`#>  textshaping            1.0.5   ``2026-03-06`` ``[2]`` ``RSPM (R 4.6.0)`\
`#>  xfun                   0.61    ``2026-09-16`` ``[2]`` ``RSPM (R 4.6.0)`\
`#>  XVector                0.53.0  ``2026-04-28`` ``[1]`` ``Bioconductor 3.24 (R 4.6.1)`\
`#>  yaml                   2.3.12  ``2025-12-10`` ``[2]`` ``RSPM (R 4.6.0)`\
`#> `\
`#> `` [1] /__w/_temp/Library`\
`#> `` [2] /usr/local/lib/R/site-library`\
`#> `` [3] /usr/local/lib/R/library`\
`#>  ``*`` ── Packages attached to the search path.`\
`#> `\
`#> ``──────────────────────────────────────────────────────────────────────────────`

## References

Blanc-Mathieu, Romain, Renaud Dumas, Laura Turchi, Jérémy Lucas, and
François Parcy. 2024. “Plant-TFClass: A Structural Classification for
Plant Transcription Factors.” *Trends in Plant Science* 29 (1): 40–51.
<https://doi.org/10.1016/j.tplants.2023.06.023>.

Finn, Robert D, Jody Clements, and Sean R Eddy. 2011. “HMMER Web Server:
Interactive Sequence Similarity Searching.” *Nucleic Acids Research* 39
(suppl_2): W29–37.

Jin, Jinpu, Feng Tian, De-Chang Yang, et al. 2017. “PlantTFDB 4.0:
Toward a Central Hub for Transcription Factors and Regulatory
Interactions in Plants.” *Nucleic Acids Research* 45 (D1): D1040–45.
<https://doi.org/10.1093/nar/gkw982>.

Osuna-Cruz, Cristina Maria, Gust Bilcke, Emmelien Vancaester, et al.
2020. “The Seminavis Robusta Genome Provides Insights into the
Evolutionary Adaptations of Benthic Diatoms.” *Nature Communications* 11
(1): 1–13.

Petroll, Romy, Deepti Varshney, Saskia Hiltemann, et al. 2025. “Enhanced
Sensitivity of TAPscan V4 Enables Comprehensive Analysis of Streptophyte
Transcription Factor Evolution.” *The Plant Journal* 121 (1): e17184.
<https://doi.org/10.1111/tpj.17184>.

[^1]: **Tip:** If you have protein sequences in a FASTA file, you can
    read them into an `AAStringSet` object with the function
    `readAAStringSet()` from the
    *[Biostrings](https://bioconductor.org/packages/3.24/Biostrings)*
    package.

[^2]: **Note:** in the code chunk below, the if statement is not
    required. We just added it to make sure that the function
    [`annotate_domains()`](../reference/annotate_domains.md) is only
    executed if HMMER is installed, to avoid problems when building this
    vignette in machines that do not have HMMER installed.

[^3]: **Tip:** If you have whole-genome protein sequences for multiple
    species as FASTA files in a given directory, you can read them all
    as a list of `AAStringSet` objects with the function
    `fasta2AAStringSetlist()` from the Bioconductor package
    *[syntenet](https://bioconductor.org/packages/3.24/syntenet)*.
