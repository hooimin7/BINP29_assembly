Long read de novo assembly exercise

Dataset is a HiFi Pacbio reads from the yeast Saccharomyces cerevisiae.
The data can be downloaded from NCBI Sequence Read Archive (SRA)

Download the reads for the following page or use sra-tools:
SRR13577846

fastq-dump 3.0.10

Quality check before assembly
FastQC 0.12.1

de novo assembly
hifiasm 0.19.8-r603

Quast quality assessment
QUAST 5.2.0

BUSCO assessment
BUSCO 2.0.1

augustus 3.5.0

Setting up data
mkdir de_novo_genome_assembly
# download the data from SRA
fastq-dump --origfmt --split-files SRR13577846

Quality Control
mkdir fastQC_raw
fastqc --threads 6 SRR13577846_1.fastq --outdir fastQC_raw # --threads 6 to use 6 cores, --outdir to specify the output directory

De novo Assembly
mkdir hifiasm_1
hifiasm -o SRR13577846_1.asm -t32 -l0 /home/inf-51-2023/de_novo_genome_assembly/SRR13577846_1.fastq 2> SRR13577846_1.asm.log # -o to specify the output file, -t to specify the number of threads, -l to specify the minimum length of the reads

# change the gfa format to fasta, https://hifiasm.readthedocs.io/en/latest/faq.html
awk '/^S/{print ">"$2;print $3}' SRR13577846_1.asm.bp.a_ctg.gfa > SRR13577846_1.asm.bp.a_ctg.fa
# Do it for all the other gfa files

Assembly Quality Check with Quast
mkdir quast_analysis
# source of the reference genome https://www.ncbi.nlm.nih.gov/datasets/genome/GCF_000146045.2/
quast /home/inf-51-2023/de_novo_genome_assembly/hifiasm_1/SRR13577846_1.asm.bp.a_ctg.fa \
           /home/inf-51-2023/de_novo_genome_assembly/hifiasm_1/SRR13577846_1.asm.bp.p_ctg.fa \
           /home/inf-51-2023/de_novo_genome_assembly/hifiasm_1/SRR13577846_1.asm.bp.p_utg.fa \
           /home/inf-51-2023/de_novo_genome_assembly/hifiasm_1/SRR13577846_1.asm.bp.r_utg.fa \
        -r /home/inf-51-2023/de_novo_genome_assembly/ncbi_dataset/data/GCF_000146045.2/GCF_000146045.2_R64_genomic.fna \
        -o quast_analysis

Assembly Quality Check with Busco
# fungi_odb10
busco -i /home/inf-51-2023/de_novo_genome_assembly/hifiasm_1/SRR13577846_1.asm.bp.p_ctg.fa -l /home/inf-51-2023/de_novo_genome_assembly/fungi_odb10 -o Busco_out -m genome

# saccharomycetes_odb10
busco -i /home/inf-51-2023/de_novo_genome_assembly/hifiasm_1/SRR13577846_1.asm.bp.p_ctg.fa -l /home/inf-51-2023/de_novo_genome_assembly/saccharomycetes_odb10 -o saccha_out -m genome




