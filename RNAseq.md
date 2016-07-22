#RNA-seq
##公開データの取得
```
ls -l /home/iu/genome/sacCer3/

less /home/iu/genome/sacCer3/genome.fa
```
```
ls data

wc -l data/10K_SRR2048224_1.fastq

wc -l data/10K_SRR2048224_2.fastq
```
```
fastqc -v

fastqc -h
```
```
mkdir rnaseq/fastqc_res

fastqc -o fastqc_res -f fastq --nogroup data/10K_SRR2048224_1.fastq data/10K_SRR2048224_2.fastq

fastqc -o fastqc_res -f fastq --nogroup data/10K_SRR2048225_1.fastq data/10K_SRR2048225_2.fastq

fastqc -o fastqc_res -f fastq --nogroup data/10K_SRR2048228_1.fastq data/10K_SRR2048228_2.fastq

fastqc -o fastqc_res -f fastq --nogroup data/10K_SRR2048229_1.fastq data/10K_SRR2048229_2.fastq
```
```
ls fastqc_res

firefox fastqc_res/10K_SRR2048224_1_fastqc/fastqc_report.html fastqc_res/10K_SRR2048224_2_fastqc/fastqc_report.html

firefox fastqc_res/10K_SRR2048225_1_fastqc/fastqc_report.html fastqc_res/10K_SRR2048225_2_fastqc/fastqc_report.html

firefox fastqc_res/10K_SRR2048228_1_fastqc/fastqc_report.html fastqc_res/10K_SRR2048228_2_fastqc/fastqc_report.html

firefox fastqc_res/10K_SRR2048229_1_fastqc/fastqc_report.html fastqc_res/10K_SRR2048229_2_fastqc/fastqc_report.html
```
```
prinseq-lite.pl -h
```

##クオリティーコントロール
```
mkdir 1_qc

prinseq-lite.pl -fastq data/10K_SRR2048224_1.fastq -fastq2 data/10K_SRR2048224_2.fastq -out_good 1_qc/10K_SRR2048224.notail -out_bad null -out_format 3 -trim_left 5 -trim_tail_right 5 -trim_qual_right 30 -ns_max_p 20 -min_len 30

prinseq-lite.pl -fastq data/10K_SRR2048225_1.fastq -fastq2 data/10K_SRR2048225_2.fastq -out_good 1_qc/10K_SRR2048225.notail -out_bad null -out_format 3 -trim_left 5 -trim_tail_right 5 -trim_qual_right 30 -ns_max_p 20 -min_len 30

prinseq-lite.pl -fastq data/10K_SRR2048228_1.fastq -fastq2 data/10K_SRR2048228_2.fastq -out_good 1_qc/10K_SRR2048228.notail -out_bad null -out_format 3 -trim_left 5 -trim_tail_right 5 -trim_qual_right 30 -ns_max_p 20 -min_len 30

prinseq-lite.pl -fastq data/10K_SRR2048229_1.fastq -fastq2 data/10K_SRR2048229_2.fastq -out_good 1_qc/10K_SRR2048229.notail -out_bad null -out_format 3 -trim_left 5 -trim_tail_right 5 -trim_qual_right 30 -ns_max_p 20 -min_len 30
```
```
fastqc -o fastqc_res -f fastq --nogroup 1_qc/10K_SRR2048224.notail_1.fastq 1_qc/10K_SRR2048224.notail_2.fastq

fastqc -o fastqc_res -f fastq --nogroup 1_qc/10K_SRR2048225.notail_1.fastq 1_qc/10K_SRR2048225.notail_2.fastq

fastqc -o fastqc_res -f fastq --nogroup 1_qc/10K_SRR2048228.notail_1.fastq 1_qc/10K_SRR2048228.notail_2.fastq

fastqc -o fastqc_res -f fastq --nogroup 1_qc/10K_SRR2048229.notail_1.fastq 1_qc/10K_SRR2048229.notail_2.fastq
```
```
firefox fastqc_res/10K_SRR2048224.notail_1_fastqc/fastqc_report.html fastqc_res/10K_SRR2048224.notail_2_fastqc/fastqc_report.html

firefox fastqc_res/10K_SRR2048225.notail_1_fastqc/fastqc_report.html fastqc_res/10K_SRR2048225.notail_2_fastqc/fastqc_report.html

firefox fastqc_res/10K_SRR2048228.notail_1_fastqc/fastqc_report.html fastqc_res/10K_SRR2048228.notail_2_fastqc/fastqc_report.html

firefox fastqc_res/10K_SRR2048229.notail_1_fastqc/fastqc_report.html fastqc_res/10K_SRR2048229.notail_2_fastqc/fastqc_report.html
```

##マッピング
```
mkdir 2_mapping

hisat2 -x /home/iu/genome/sacCer3/Hisat2Index/genome --dta --dta-cufflinks -1 1_qc/10K_SRR2048224.notail_1.fastq -2 1_qc/10K_SRR2048224.notail_2.fastq -S 2_mapping/10K_SRR2048224.sam

hisat2 -x /home/iu/genome/sacCer3/Hisat2Index/genome --dta --dta-cufflinks -1 1_qc/10K_SRR2048225.notail_1.fastq -2 1_qc/10K_SRR2048225.notail_2.fastq -S 2_mapping/10K_SRR2048225.sam

hisat2 -x /home/iu/genome/sacCer3/Hisat2Index/genome --dta --dta-cufflinks -1 1_qc/10K_SRR2048228.notail_1.fastq -2 1_qc/10K_SRR2048228.notail_2.fastq -S 2_mapping/10K_SRR2048228.sam

hisat2 -x /home/iu/genome/sacCer3/Hisat2Index/genome --dta --dta-cufflinks -1 1_qc/10K_SRR2048229.notail_1.fastq -2 1_qc/10K_SRR2048229.notail_2.fastq -S 2_mapping/10K_SRR2048229.sam
```
```
samtools view -b 2_mapping/10K_SRR2048224.sam > 2_mapping/10K_SRR2048224.bam

samtools view -b 2_mapping/10K_SRR2048225.sam > 2_mapping/10K_SRR2048225.bam

samtools view -b 2_mapping/10K_SRR2048228.sam > 2_mapping/10K_SRR2048228.bam

samtools view -b 2_mapping/10K_SRR2048229.sam > 2_mapping/10K_SRR2048229.bam
```
```
samtools sort 2_mapping/10K_SRR2048224.bam -o 2_mapping/10K_SRR2048224.sorted.bam

samtools sort 2_mapping/10K_SRR2048225.bam -o 2_mapping/10K_SRR2048225.sorted.bam

samtools sort 2_mapping/10K_SRR2048228.bam -o 2_mapping/10K_SRR2048228.sorted.bam

samtools sort 2_mapping/10K_SRR2048229.bam -o 2_mapping/10K_SRR2048229.sorted.bam
```
```
samtools index 2_mapping/10K_SRR2048224.sorted.bam

samtools index 2_mapping/10K_SRR2048225.sorted.bam

samtools index 2_mapping/10K_SRR2048228.sorted.bam

samtools index 2_mapping/10K_SRR2048229.sorted.bam
```
##発現定量
```
cufflinks -o SRR2048224 --min-frags-per-transfrag 2 2_mapping/10K_SRR2048224.sorted.bam

cufflinks -o SRR2048225 --min-frags-per-transfrag 2 2_mapping/10K_SRR2048225.sorted.bam

cufflinks -o SRR2048228 --min-frags-per-transfrag 2 2_mapping/10K_SRR2048228.sorted.bam

cufflinks -o SRR2048229 --min-frags-per-transfrag 2 2_mapping/10K_SRR2048229.sorted.bam
```
```
cuffmerge -o COMPARE -g /home/iu/genome/sacCer3/genes.gtf -s /home/iu/genome/sacCer3/genome.fa  transcripts.gtf.txt
```
##発現比較
```
cuffdiff -o COMPARE -L Group1,Group2 COMPARE/merged.gtf 2_mapping/10K_SRR2048224.sorted.bam,2_mapping/10K_SRR2048225.sorted.bam 2_mapping/10K_SRR2048228.sorted.bam,2_mapping/10K_SRR2048229.sorted.bam
```
