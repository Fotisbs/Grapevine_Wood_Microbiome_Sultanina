# Biogeography across and within viticultural zones outweighs age and grapevine trunk disease status in shaping the grapevine wood microbiome 

### By Fotios Bekris <sup>1+</sup>, Angelos Floudas <sup>2+</sup>, Nikolaos Krasagakis <sup>3,4</sup>, Stefanos K. Soultatos <sup>3,4</sup>,, Stefanos Testempasis <sup>2,5</sup>, Emmanouil Markakis <sup>3,4</sup>, Michalis Omirou <sup>6</sup>, George Karaoglanidis <sup>2*</sup>, Karpouzas D.G <sup>1*</sup>

### (\* corr. author)
### (\+ contributed equally to this work)

<sup>1</sup> University of Thessaly, Department of Biochemistry and Biotechnology, Larissa, Greece

<sup>2</sup> Aristotle University of Thessaloniki, Plant Pathology Laboratory, Faculty of Agriculture, Thessaloniki, Greece

<sup>3</sup> Hellenic Mediterranean University, Department of Agriculture, School of Agricultural Sciences, Heraklion, Greece

<sup>4</sup> Hellenic Agricultural Organization DIMITRA, Laboratory of Mycology, Department of Viticulture, Vegetable Crops, Floriculture and Plant Protection, Institute of Olive Tree, Subtropical Crops and Viticulture, Heraklion, Greece

<sup>5</sup> University of Western Macedonia, Department of Agriculture, School of Agricultural Sciences, Florina, Greece

<sup>6</sup> Department of Agrobiotechnology, Agricultural Research Institute, P.O.Box 22016, 1516, Nicosia, Cyprus

## Repository overview

This repository contains all scripts required to reproduce the microbiome analyses presented in the manuscript, including sequencing data retrieval, preprocessing, statistical analyses and figure generation. Raw sequencing data are publicly available through the NCBI Sequence Read Archive (NCBI SRA), while all downstream analyses can be reproduced using the scripts provided in this repository.

To obtain the repository, install Git (if not already installed https://github.com/git-guides/install-git), open a terminal and clone the repository:

```
$ git clone https://github.com/Fotisbs/Grapevine_Wood_Microbiome_Sultanina.git
```
Alternatively, the repository can be downloaded as a ZIP archive directly from GitHub.

Unless otherwise stated, all commands assume that the repository root directory ("Grapevine_Wood_Microbiome_Sultanina") is used as the working directory. The required sequencing datasets can be downloaded directly from the NCBI Sequence Read Archive using the scripts provided in each module.

## Repository structure
```
├── Bacteria/
│   ├── 0.DownloadData
│   ├── 1.Demultiplex
│   ├── 2.PhyloseqObjectPreparation
│   └── 3.DataAnalysis
│
├── Fungi/
   ├── 0.DownloadData
   ├── 1.Demultiplex
   ├── 2.PhyloseqObjectPreparation
   └── 3.DataAnalysis

```	

## Microbiome (Metataxonomic) analyses

The microbiome workflow is organized into four sequential steps:

0. Download raw sequencing data
1. Demultiplex sequencing reads
2. Construct phyloseq objects
3. Perform downstream statistical analyses


***Step 0. First, it is necessary to download the sequencing data***

To do so, you need to enter the "0.DownloadData" subfolder of "Fungi" and "Bacteria" folders accordingly and execute the "fetch_data.sh" bash script for batch (01), this assumes that you are located at the working directory "Grapevine_Vinifications_Vidiano_2019-".

The script downloads all raw amplicon sequencing reads deposited in the NCBI Sequence Read Archive using the corresponding SRR accession numbers listed in the batch files.Once the download is done, you need to combine all forward reads to a single file and all reverse reads to another file as well.
```
for i in {01}
do
	cd Fungi/0.DownloadData/batch${i}
	sh -x fetch_data.sh
	cat *_1.fastq | gzip > forward.fastq.gz
	cat *_2.fastq | gzip > reverse.fastq.gz
	cd ../../../
	cd Bacteria/0.DownloadData/batch${i}
	sh -x fetch_data.sh
	cat *_1.fastq | gzip > forward.fastq.gz
	cat *_2.fastq | gzip > reverse.fastq.gz
	cd ../../../
done
```

***Step 1. Then you need to demultiplex the data according to our own demultiplexing method using our in-house script***

This step requires Flexbar v3.0.3 together with the mapping file (map_file) provided in the corresponding folder. A detailed description of our in-house multiplexing approach is provided in (https://github.com/SotiriosVasileiadis/mconsort_tbz_degr#16s).
You need to enter the folder Fungi (or Bacteria)/1.Demultiplex and run the following commands (change the MY_PROCS variable to whatever number of logical processors you have available and want to devote),
the following commands are going to save the demultiplexed files in the Fungi(or Bacteria)/1.Demultiplex/demux_out folder.

```
The demultiplexing workflow follows the protocol described in: https://github.com/SotiriosVasileiadis/mconsort_tbz_degr#16s
```

***Step 2. The script `Vinification Vidiano 2019 Quality-Classification-Phyloseq Object.R` constructs the final phyloseq object used for all downstream microbiome analyses.***

PhyloseqObjectPreparation folder is run in order to prepare the final phyloseq object to be used in the data analysis described below. Before running the script make sure that the necessary reference databases are found in the same folder. The taxonomic annotations of the resulting fungal and bacterial ASVs were performed using the UNITE ITS v.8.2 (04.02.2020) (Morrison-Whittle et al., 2017) and the Silva v.138 (Yilmaz et al., 2014) databases as references respectively. The sample metadata file (samdf.txt), included in the repository, is also required for construction of the phyloseq objects.
```
cd Fungi/2.PhyloseqObjectPreparation
# fetch the databases
wget https://files.plutof.ut.ee/public/orig/1D/B9/1DB95C8AC0A80108BECAF1162D761A8D379AF43E2A4295A3EF353DD1632B645B.gz
# run the R script
Fungi Vinification Vidiano 2019 Quality-Classification-Phyloseq Object.r
cd ../../
cd Bacteria/2.PhyloseqObjectPreparation
# fetch the databases
wget https://zenodo.org/record/4587955/files/silva_nr99_v138.1_train_set.fa.gz
wget https://zenodo.org/record/4587955/files/silva_nr99_v138.1_wSpecies_train_set.fa.gz
tar vxf *.gz
# run the R script
Bacteria Vinification Vidiano 2019 Quality-Classification-Phyloseq Object.r
cd ../../
```
***Step 3. The data analysis folder includes independent R scripts reproducing all microbiome analyses and figures presented in the manuscript***

```
- Taxonomic composition
- Beta-diversity (NMDS)
- Beta-diversity statistics (PERMANOVA)
- Rarefaction curves
- Alpha-diversity metrics
- Differential abundance heatmaps
```

### Software requirements

The analyses were performed in R (v4.3.1) using the packages:

- phyloseq
- vegan
- ggplot2
- pheatmap
- dplyr
- agricolae
- apeglm
- ggrepel
- RColorBrewer


## Code Usage disclaimer<a name="disclaimer"></a>

The following is the disclaimer that applies to all scripts, functions, one-liners, etc. This disclaimer supersedes any disclaimer included in any script, function, one-liner, etc.

You running this script/function means you will not blame the author(s) if this breaks your stuff. This script/function is provided **AS IS** without warranty of any kind. Author(s) disclaim all implied warranties including, without limitation, any implied warranties of merchantability or of fitness for a particular purpose. The entire risk arising out of the use or performance of the sample scripts and documentation remains with you. In no event shall author(s) be held liable for any damages whatsoever (including, without limitation, damages for loss of business profits, business interruption, loss of business information, or other pecuniary loss) arising out of the use of or inability to use the script or documentation. Neither this script/function, nor any part of it other than those parts that are explicitly copied from others, may be republished without author(s) express written permission. Author(s) retain the right to alter this disclaimer at any time. This disclaimer was copied from a version of the disclaimer published by other authors in https://ucunleashed.com/code-disclaimer and may be amended as needed in the future.
