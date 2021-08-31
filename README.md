# SNP CRISPR
SNP-targeted CRISPR sgRNA design pipeline. Online version available at: [https://www.flyrnai.org/tools/snp_crispr](https://www.flyrnai.org/tools/snp_crispr)  

Publication: [https://fgr.hms.harvard.edu/publications/snp-crispr-web-tool-snp-specific-genome-editing](https://fgr.hms.harvard.edu/publications/snp-crispr-web-tool-snp-specific-genome-editing)

![Workflow](img/workflow.jpg)

## Prerequisites
- Anaconda or Miniconda for environment management
- Species genome in fasta format

**Note:** To install without conda, see `environment.yml` for dependencies

## Install
- From the project directory run `conda env create -f environment.yml`
- Then `conda activate snp_crispr`
- FlyBase release 6.28 genome fasta file and blast database are included for testing. `dm.fasta.zip` must be unzipped before running. Remaining installation steps are only necessary for other species
- Download species genome fasta file and put in `fasta_files/<species>.fasta`
- Run `makeblastdb -in fasta_files/<species>.fasta -dbtype nucl -parse_seqids -title <species> -out blast_dbs/<species>`
- Species chromosome names -> fasta id mapping files included in `fasta_files/<species>_chr_ids.txt`

**Important Note:** `<species>` name/abbreviation in `fasta_files/<species>.fasta`, `fasta_files/<species>_chr_ids.txt`, and `blast_dbs/<species>` must all match

## Species Genome Fasta Download Links
Human - https://www.ncbi.nlm.nih.gov/genome/?term=txid9606  
Mouse - https://www.ncbi.nlm.nih.gov/genome/?term=txid10090  
Zebrafish - https://www.ncbi.nlm.nih.gov/genome/?term=txid7955  
Rat - https://www.ncbi.nlm.nih.gov/genome?term=txid10116  

## Usage
- Add SNP's or INDEL's of interest to input csv file, see `sample_input.csv` for format
- Input files in VCF format are also supported (file must have .vcf extension)
- From the project directory run `./snp_crispr.sh <species> <input_file> <PAM> <threads> <-all/-both> <-alt>`
- Both `-NGG` and `-NAG` PAM sequences supported
- Running with the optional `-all` argument designs guides where all SNPs are targeted within the 23-mer
- Running with the optional `-both` argument designs guides where all SNPs are targeted within the 23-mer as well as individually
- Running with the optional `-alt` argument evaluates the specificity of SNP targeted CRISPRs against a user provided non-reference genome. Before using this option the `makeblastdb` installation step must be repeated using the non-reference genome fasta file and the non-reference blast db title and output filenames must be `<species>_alt`
- **All arguments are required except `-all/-both and -alt`. Also note that the order of the arguments must match the provided example**
- Design results found in `results/designs.csv`
- Input variants without any viable designs are logged in `results/no_designs.csv`
- No viable designs may be found targeting a variant due to the lack of a neighboring PAM sequence, presence of the U6 terminator (TTTT), or the unavailability of a unique sgRNA sequence
- To test the pipeline run `./snp_crispr.sh dm sample_input.csv -NGG 2` and compare the output in `results/designs.csv` with `results/expected_results.csv`

**Note:** The pipeline can be run using any species or genome assembly by adding a new chromosome name to fasta id mapping file in the same format as the examples in `fasta_files/<species>_chr_ids.txt` and then following the same installation instructions

## Multi-Core Performance
- **2 threads recommended on personal computers with limited RAM**
- **Increasing the number of threads can significantly increase memory usage depending on the genome size**
- Setting the threads argument to 2 or more will improve performance when using inputs that span across multiple chromosomes and/or when using the -both argument
- When running on a cluster or other environment with large amounts of memory available, using as many threads as chromosomes in the input will give optimal performance

**Example:** A test run using 10 threads on an input of 10,000 human snps took \~9 minutes and used \~50 GB of RAM  
(Results will vary depending on the machine, input, species, and options used)

## Example Commands
- Fly: `./snp_crispr.sh dm dm_snps.csv -NGG 2 -all -alt`
- Human: `./snp_crispr.sh hs hs_snps.vcf -NAG 6 -both`
- Mouse: `./snp_crispr.sh mm mm_snps.csv -NGG 3`

## Design Scores
**Housden Efficiency Score**  
- These scores were computed using a position matrix. Detailed information about the input dataset and the algorithm can be found in Housden et al. Sci Signal. 2015 https://www.ncbi.nlm.nih.gov/pubmed/26350902
- Scores range from 1.47-12.32 (higher is better, > 5 recommended)

**Off Target Score**  
- Calculated based on sgRNA sequence blast results.
- Scores range from 0-5441.73 (lower is better, < 1 recommended)

## Precomputed Human Clinically Associated SNP Targeted Designs
- Precomputed CRISPR design results provided in [clinical_hs_snp_designs.csv](results/clinical_hs_snp_designs.csv)
- Clinically associated variant data from [Ensembl](https://ensembl.org/Homo_sapiens)

## Authors
**Jonathan Rodiger** - [jrodiger](https://github.com/jrodiger)  
**Verena Chung** - [vpchung](https://github.com/vpchung)

## License
This project is licensed under the MIT license - see [LICENSE](LICENSE) for details

## Acknowledgements
**Perrimon Lab** (https://perrimon.med.harvard.edu/)    
**DRSC** (https://fgr.hms.harvard.edu/)
