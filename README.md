# AmpliFuse
Amplicon simulation tool with enhance chimera generation for Illumina platforms. 

AmpliFuse is a Python script that facilitates  the generation of Illumina amplion seqecning raw reads inc

+ [Requirements](#system-requirements)
+ [Installation](#installation)
+ [Usage](#usage)
+ [Output files](#output-files)
+ [Citation](#citation)   
+ [Reference](#reference)  


## Requirements

AmpliFuse was developed and tested with Python 3.13.7 on Ubuntu 22.04.5 LTS. It requires the installation and utilization of specific tools.
+ Tool or pipleine:
+ [in_silico_PCR.pl](https://github.com/egonozer/in_silico_pcr)<sup>1</sup>
+ [InSilicoSeq](https://github.com/HadrienG/InSilicoSeq)<sup>2</sup>

+ Python standard library plus:
+  [numpy](https://numpy.org/)
+  [biopython](https://biopython.org/)
    
## Installation

The easiest and most convenient way to install AmpliFuse dependencies is by using conda in an isolated environment. This method ensures a smooth and hassle-free installation process.
```bash
git clone https://github.com/comingkms/AmpliFuse.git
cd AmpliFuse
conda env create -n AmpliFuse

```
Download [in_silico_PCR.pl](https://github.com/egonozer/in_silico_pcr)<sup>1</sup> and save it into the AmpliFuse folder. 

To ensure the availability of the `AmpliFuse` command, it is essential to add the absolute path of AmpliFuse's directory to your PATH environment variable. This can be done by adding the following line to your `~/.bashrc` file:

```
export PATH=/absolute/path/to/AmpliFuse:${PATH}
```

## Usage and command line options


Activate AmpliFuse conda environment:
```
conda activate AmpliFuse
```

Running AmpliFuse:
```
$AmpliFuse -h
usage: AmpliFuse --input FASTA --outdir DIR --pcr-primers-file FILE --n-reads N --model MODEL --cpu N [other options]

Amplicon chimera pipeline

options:
  -h, --help            show this help message and exit
  --version             show program's version number and exit

Input & output:
  --input INPUT         Input FASTA of templates (default: None)
  --outdir OUTDIR       Output directory (creates pcr/, chimera/, simulated/) (default: None)
  --pick-n PICK_N       Randomly pick N templates before PCR/chimera (0 or >=total = keep all) (default: 30)
  --verbose, -v         Verbose logging (default: False)

PCR:
  --ispcr-path ISPCR_PATH
                        Path to in_silico_PCR.pl (default: script folder) (default: None)
  --pcr-primers-file PCR_PRIMERS_FILE
                        Primers file for -p (fwd<TAB>rev<TAB>output_prefix) (default: None)

Chimera generation:
  --rate RATE           Chimera rate (proportion of PCR amplicons) (default: 0.05)
  --min-segment MIN_SEGMENT
                        Minimum segment length as a FRACTION of amplicon length (0–1). Default=0.1 (default: 0.1)
  --breakpoint-model {exponential,uniform}
                        Breakpoint distribution model (default: exponential)
  --snap-kmer SNAP_KMER
                        k-mer size for snapping (used if --snap-auto is OFF) (default: 11)
  --snap-window SNAP_WINDOW
                        Search window ±bp (used if --snap-auto is OFF) (default: 50)
  --snap-auto           Automatically try multiple (k,window) combos to find the best snap; defaults: k∈{15,13,11,9,7,5}, window∈{20,40,60,80,100}. (default: False)
  --snap-kmer-candidates SNAP_KMER_CANDIDATES
                        Comma-separated k candidates used when --snap-auto is set (default: 15,13,11,9,7,5)
  --snap-window-candidates SNAP_WINDOW_CANDIDATES
                        Comma-separated window candidates used when --snap-auto is set (default: 20,40,60,80,100)
  --model2              Use Simera-style Model 2 selection (probabilistic, fragment-driven). (default: False)
  --phi PHI             Per-base extension failure probability φ for fragment formation (Model 2). (default: 0.01)
  --cycles CYCLES       Number of PCR cycles influencing fragment formation (Model 2). (default: 30)
  --mh-beta MH_BETA     Strength of microhomology preference; larger favors longer k (Model 2). (default: 0.25)
  --mh-k0 MH_K0         Neutral microhomology length k0 in exp[β·(k−k0)] (Model 2). (default: 7)
  --simera-rate         Derive effective chimera rate from (cycles, φ) like Simera; overrides --rate. (default: False)
  --simera-rate-scale SIMERA_RATE_SCALE
                        Scale factor applied to fragment probability to get effective rate. (default: 0.5)
  --simera-rate-cap SIMERA_RATE_CAP
                        Upper cap on effective rate (safety). (default: 0.6)
  --simera-calibrate    Auto-calibrate simera-rate scale to hit --target-rate (overrides --simera-rate-scale). (default: False)
  --target-rate TARGET_RATE
                        Desired effective chimera fraction when using --simera-rate (default 0.2). (default: 0.2)

Simulator (InSilicoSeq):
  --n-reads N_READS     (--n_reads) Total reads to simulate (default: None)
  --model MODEL         (--model) Sequencer model (e.g., nextseq, miseq) (default: nextseq)
  --cpu CPU             (--cpus) CPU threads for ISS (default: 4)
  --abundance, -a {uniform,halfnormal,exponential,lognormal,zero-inflated-lognormal}
                        (--abundance) Abundance distribution model (default: lognormal)

```

## Output files

The output directory of MAMR has the following structure:

Module 1: AMR 
```
OUTPUT_DIR/
├── Resistomes/
    ├── AMR_combined.class.csv
    ├── AMR_combined.gene.csv
    ├── AMR_combined.class_rel_abundance.csv
    ├── AMR_combined.gene_rel_abundance.csv
    ├──{sample_1}_sam.class.tsv 
    ├──{sample_1}_sam.gene.tsv
    ├──{sample_1}_sam.group.tsv
    ├──{sample_1}_sam.mechanism.tsv 
    ├──...
```
MAMR combined outputs are stored in the `AMR_conbined.****.csv` files. 

Module 2: BAC

```
OUTPUT_DIR/
├── {sample_1}/
├── {sample_2}/
├── {sample_3}/
├── ...
├── combined/
    ├── {sample_1}_rel-abundance.tsv 
    ├── ...
    ├── emu-combined-genus.tsv
    ├── emu-combined-genus_counts.tsv
    ├── emu-combined-species.tsv
    └── emu-combined-species_counts.tsv
```
MAMR utilizes the Emu pipeline to analyze 16S rRNA sequencing data. For more information, please review the [Emu GitHub repository](https://github.com/treangenlab/emu) 

Module 3: COR

```
OUTPUT_DIR/
├── correlation_genus.csv
├── correlation_species.csv
├── ...

```
MAMR correlation results are saved in the `corelation_xxx.csv' files. The rest of the files in this output folder are copied from the output folders of modules 1 and 2.

## Citation 



## Reference
