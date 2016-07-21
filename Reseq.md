cd /home/iu/reseq
ls

less /home/iu/genome/sacCer3/genome.fa 

less data/ERR038793_1.fastq

wc -l data/ERR038793_1.fastq

wc -l data/ERR038793_2.fastq

fastqc -v

fastqc -h

mkdir fastqc_before

fastqc -o fastqc_before -f fastq \
-c data/ERR038793_1.fastq data/ERR038793_2.fastq

ls fastqc_before

firefox \
fastqc_before/ERR038793_1_fastqc/fastqc_report.html \
fastqc_before/ERR038793_2_fastqc/fastqc_report.html

mkdir trimmed_data

java -jar /usr/local/bin/trimmomatic-0.36.jar \
PE -threads 2 -phred33 -trimlog trimmed_data/log.txt \
data/ERR038793_1.fastq \
data/ERR038793_2.fastq \
trimmed_data/paired_output_ERR038793_1.fastq \
trimmed_data/unpaired_output_ERR038793_1.fastq \
trimmed_data/paired_output_ERR038793_2.fastq \
trimmed_data/unpaired_output_ERR038793_2.fastq \
SLIDINGWINDOW:5:20 LEADING:20 TRAILING:20  MINLEN:80

mkdir fastqc_after

fastqc -o fastqc_after -f fastq -c \
trimmed_data/paired_output_ERR038793_1.fastq \
trimmed_data/paired_output_ERR038793_2.fastq

firefox \
fastqc_after/paired_output_ERR038793_1_fastqc/\
fastqc_report.html \
fastqc_after/paired_output_ERR038793_2_fastqc/\
fastqc_report.html

bwa mem -help

mkdir -p Scerevisiae/BWAIndex

cd Scerevisiae/BWAIndex

ln -s /home/iu/genome/sacCer3/genome.fa 

bwa index genome.fa

ls

cd /home/iu/reseq

mkdir mapping

bwa mem -M \
-R '@RG\tID:ERR038793_1\tSM:ERR038793\tPL:Illumina' \
Scerevisiae/BWAIndex/genome.fa \
trimmed_data/paired_output_ERR038793_1.fastq > \
mapping/ERR038793_mapped.sam

samtools view -b mapping/ERR038793_mapped.sam > \
mapping/ERR038793_mapped.bam

ls -lh mapping

samtools sort mapping/ERR038793_mapped.bam \
-o  mapping/ERR038793_sorted.bam

samtools index mapping/ERR038793_sorted.bam

ls mapping

samtools idxstats mapping/ERR038793_sorted.bam

wc -l trimmed_data/paired_output_ERR038793_1.fastq | \
awk '{print $1/4;}'

samtools idxstats mapping/ERR038793_sorted.bam > multi.txt

awk '{sum += $3} END {print sum}' multi.txt

awk '{sum2 += $4} END {print sum2}' multi.txt

samtools view -b -F 256 mapping/ERR038793_sorted.bam > \
mapping/ERR038793_unique.bam

samtools index mapping/ERR038793_unique.bam

samtools idxstats mapping/ERR038793_unique.bam > \
unique.txt

awk '{sum += $3} END {print sum}' unique.txt

awk '{sum2 += $4} END {print sum2}' unique.txt

mkdir variant_call

java -jar /usr/local/bin/GenomeAnalysisTK.jar \
-R /home/iu/genome/sacCer3/genome.fa -T HaplotypeCaller \
-I mapping/ERR038793_sorted.bam \
-variant_index_type LINEAR -variant_index_parameter 128000 \
-o variant_call/ERR038793.vcf

ls variant_call

less variant_call/ERR038793.vcf

grep -c -v '^#' variant_call/ERR038793.vcf

awk '{print $10;}' variant_call/ERR038793.vcf | \
grep '0/1' | \
cut -d ':' -f 3 | \
awk '{if($1 < 10){print $1;}}' | \
wc -l

java -jar /usr/local/bin/GenomeAnalysisTK.jar \
-R /home/iu/genome/sacCer3/genome.fa -T VariantFiltration \
-V variant_call/ERR038793.vcf \
-o variant_call/ERR038793_fil.vcf \
--clusterWindowSize 10 \
--filterExpression 'DP < 10' \
--filterName 'LowCoverage'

less variant_call/ERR038793_fil.vcf

mkdir annotation

cd annotation

java -jar /usr/local/bin/snpEff.jar eff \
-c /usr/local/bin/snpEff.config -i vcf R64-1-1.82 \
-o vcf ../variant_call/ERR038793_fil.vcf 1> \
ERR038793_eff.vcf

less ERR038793_eff.vcf

grep 'chrM' ERR038793_eff.vcf

sed -e  's/chrM/chrMito/g' \
../variant_call/ERR038793_fil.vcf > \
../variant_call/ERR038793_fil2.vcf

java -jar /usr/local/bin/snpEff.jar eff \
-c /usr/local/bin/snpEff.config -i vcf R64-1-1.82 \
-o vcf ../variant_call/ERR038793_fil2.vcf 1> \
ERR038793_eff2.vcf

grep 'chrM' ERR038793_eff2.vcf

igv.sh