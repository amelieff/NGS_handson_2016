## 公開データの取得

### データの確認

```
cd /home/iu/chipseq/data
ls data
```

fastqc --nogroup -t 8 -o ./ input_1.fastq input_2.fastq sample_1.fastq sample_2.fastq
ls

firefox input_1_fastqc/fastqc_report.html input_2_fastqc/fastqc_report.html sample_1_fastqc/fastqc_report.html sample_2_fastqc/fastqc_report.html

java -jar /usr/local/bin/trimmomatic-0.36.jar PE -threads 8 -phred33 input_1.fastq input_2.fastq input_1_paired.fastq input_1_unpaired.fastq input_2_paired.fastq input_2_unpaired.fastq LEADING:20 TRAILING:20 SLIDINGWINDOW:4:15 MINLEN:36
java -jar /usr/local/bin/trimmomatic-0.36.jar PE -threads 8 -phred33 sample_1.fastq sample_2.fastq sample_1_paired.fastq sample_1_unpaired.fastq sample_2_paired.fastq sample_2_unpaired.fastq LEADING:20 TRAILING:20 SLIDINGWINDOW:4:15 MINLEN:36

fastqc --nogroup -t 8 -o ./ input_1_paired.fastq input_2_paired.fastq  sample_1_paired.fastq sample_2_paired.fastq
ls

firefox input_1_paired_fastqc/fastqc_report.html input_2_paired_fastqc/fastqc_report.html sample_1_paired_fastqc/fastqc_report.html sample_2_paired_fastqc/fastqc_report.html


cd ../
bowtie2 -p 8 -x /home/iu/genome/sacCer3/Bowtie2Index/genome -1 data/input_1_paired.fastq -2 data/input_2_paired.fastq | samtools view -Sb - > input.bam
samtools sort input.bam -o input.sorted.bam

bowtie2 -p 8 -x /home/iu/genome/sacCer3/Bowtie2Index/genome -1 data/sample_1_paired.fastq -2 data/sample_2_paired.fastq | samtools view -Sb - > sample.bam
samtools sort sample.bam -o sample.sorted.bam

macs2 callpeak -t sample.sorted.bam -c input.sorted.bam -f BAMPE -n handson2016 -B -q 0.01 -g 1.2e+7

java -jar /usr/local/bin/snpEff.jar eff -csvStats stats.txt -c /usr/local/bin/snpEff.config -i bed -o bedAnn R64-1-1.82 handson2016_summits.bed > handson2016_summits.annotated.bed

## モチーフ探索

### rGADEM
```r
R
library(rGADEM)
library("BSgenome.Scerevisiae.UCSC.sacCer3")

BED <- read.table("handson2016_peaks.narrowPeak", header=FALSE, sep="\t")
BED <- data.frame(chr=as.factor(BED[,1]),start=as.numeric(BED[,2]),end=as.numeric(BED[,3]))

rgBED<-IRanges(start=BED[,2],end=BED[,3])
Sequences<-RangedData(rgBED,space=BED[,1])
gadem<-GADEM(Sequences,verbose=1,genome=Scerevisiae)

pdf("motif.pdf")
plot(gadem)
dev.off()

q()
```


evince motif.pdf