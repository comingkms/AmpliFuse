# AmpliFuse
Amplicon simulation tool with enhance chimera generation for Illumina platforms. 

AmpliFuse is a Python script that facilitates  the generation of Illumina amplion seqecning raw reads inc

+ [Requirements](#Requirements)
+ [Installation](#installation)
+ [Usage](#usage)
+ [Output files](#output-files)
+ [Reference](#reference)  


## Requirements

AmpliFuse was developed and tested with Python 3.13.7 on Ubuntu 22.04.5 LTS. It requires the installation and utilization of specific tools.
Tools:
+ [in_silico_PCR.pl](https://github.com/egonozer/in_silico_pcr)
+ [InSilicoSeq](https://github.com/HadrienG/InSilicoSeq)
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
Download [in_silico_PCR.pl](https://github.com/egonozer/in_silico_pcr) and save it into the AmpliFuse folder. 

To ensure the availability of the `AmpliFuse` command, it is essential to add the absolute path of AmpliFuse's directory to your PATH environment variable. This can be done by adding the following line to your `~/.bashrc` file:

```
export PATH=/absolute/path/to/AmpliFuse:${PATH}
```

## Usage

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
  --input INPUT         Input FASTA of templates
  --outdir OUTDIR       Output directory (creates pcr/, chimera/, simulated/)
  --pick-n PICK_N       Randomly pick N templates before PCR/chimera (0 or >=total = keep all) (default: 30)
  --verbose, -v         Verbose logging (default: False)

PCR:
  --ispcr-path ISPCR_PATH
                        Path to in_silico_PCR.pl (default: script folder)
  --pcr-primers-file PCR_PRIMERS_FILE
                        Primers file for -p (fwd<TAB>rev<TAB>output_prefix)

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
  --n-reads N_READS     (--n_reads) Total reads to simulate (default: 10000)
  --model MODEL         (--model) Sequencer model (e.g., nextseq, miseq) (default: nextseq)
  --cpu CPU             (--cpus) CPU threads for ISS (default: 4)
  --abundance, -a {uniform,halfnormal,exponential,lognormal,zero-inflated-lognormal}
                        (--abundance) Abundance distribution model (default: lognormal)
```

## Output files

The output directory of Amplifuse has the following structure:
```
OUTPUT_DIR/
├── pcr/
    ├── amplicons.fasta     # from in_silico_PCR.pl -f
    ├── results.txt         # stdout summary from in_silico_PCR.pl
├── chimera/
    ├── chimeras.fasta      # generated child sequences
    ├── combined.fasta      # amplicons + chimeras
    ├──chimera_meta.tsv     # summary of chimera
├──simulated/
    ├──reads_*              # InSilicoSeq output: compressed FASTQs
    ├──reads_abundance.txt  # InSilicoSeq output: table
    ├──...
```

## Reference
+ https://insilicoseq.readthedocs.io/en/latest/
+ https://github.com/egonozer/in_silico_pcr
+ 

