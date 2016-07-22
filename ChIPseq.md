## 公開データの取得

### データの確認

```
cd /home/iu/chipseq/data
ls data
```

## QC

### fastqcの実行｜クリーニング前

```
mkdir fastqc_before
```

```
fastqc --nogroup -t 2 -o ./fastqc_before \
    data/input_1.fastq.gz \
    data/input_2.fastq.gz \
    data/sample_1.fastq.gz \
    data/sample_2.fastq.gz
```

### fastqcの結果確認｜クリーニング前

```
firefox \
    fastqc_before/input_1_fastqc/fastqc_report.html \
    fastqc_before/input_2_fastqc/fastqc_report.html \
    fastqc_before/sample_1_fastqc/fastqc_report.html \
    fastqc_before/sample_2_fastqc/fastqc_report.html
```

### Trimming

```
mkdir trimmed_data
```

```
java -jar /usr/local/bin/trimmomatic-0.36.jar PE \
    -threads 2 -phred33 \
    data/input_1.fastq.gz \
    data/input_2.fastq.gz \
    trimmed_data/input_1_paired.fastq \
    trimmed_data/input_1_unpaired.fastq \
    trimmed_data/input_2_paired.fastq \
    trimmed_data/input_2_unpaired.fastq \
    LEADING:20 TRAILING:20 SLIDINGWINDOW:4:15 MINLEN:36
```

```
java -jar /usr/local/bin/trimmomatic-0.36.jar PE \
    -threads 2 -phred33 \
    data/sample_1.fastq.gz \
    data/sample_2.fastq.gz \
    trimmed_data/sample_1_paired.fastq \
    trimmed_data/sample_1_unpaired.fastq \
    trimmed_data/sample_2_paired.fastq \
    trimmed_data/sample_2_unpaired.fastq \
    LEADING:20 TRAILING:20 SLIDINGWINDOW:4:15 MINLEN:36
```

### TIPS｜CPUコア数を確認してみる

```
cat /proc/cpuinfo
```

### fastqcの実行｜クリーニング後

```
mkdir fastqc_after
```

```
fastqc --nogroup -t 2 -o fastqc_after \
    trimmed_data/input_1_paired.fastq \
    trimmed_data/input_2_paired.fastq \
    trimmed_data/sample_1_paired.fastq \
    trimmed_data/sample_2_paired.fastq
```

### fastqcの結果確認｜クリーニング後

```
firefox \
    fastqc_after/input_1_paired_fastqc/fastqc_report.html \
    fastqc_after/input_2_paired_fastqc/fastqc_report.html \
    fastqc_after/sample_1_paired_fastqc/fastqc_report.html \
    fastqc_after/sample_2_paired_fastqc/fastqc_report.html
```

## マッピング

```
mkdir mapping
```

### inputデータ

```
bowtie2 -p 2 -x /home/iu/genome/sacCer3/Bowtie2Index/genome \
    -1 trimmed_data/input_1_paired.fastq \
    -2 trimmed_data/input_2_paired.fastq | \
        samtools view -Sb - > mapping/input.bam
```

```
samtools sort mapping/input.bam -o mapping/input.sorted.bam
```

### sampleデータ
```
bowtie2 -p 2 -x /home/iu/genome/sacCer3/Bowtie2Index/genome \
    -1 trimmed_data/sample_1_paired.fastq \
    -2 trimmed_data/sample_2_paired.fastq | \
        samtools view -Sb - > mapping/sample.bam
```

```
samtools sort mapping/sample.bam -o mapping/sample.sorted.bam
```

## ピーク検出

### MACS2によるピーク検出

```
macs2 callpeak \
    -t mapping/sample.sorted.bam \
    -c mapping/input.sorted.bam \
    --outdir macs2_res \
    -f BAMPE \
    -n handson2016 \
    -B \
    -q 0.01 \
    -g 1.2e+7
```

### 検出結果の確認

```
head -5 handson2016_peaks.narrowPeak
```

## 可視化

### BAMファイルのインデックスを作成

```
cd mapping
```

```
samtools index input.sorted.bam
samtools index sample.sorted.bam
```

### スコアの高いピークを確認

```
cd peak_detection
cat handson2016_summits.bed | sort -k 5n
```

## アノテーション

```
mkdir annotation
cd annotation
```

### アノテーション前のファイルを確認

```
cat ../macs2_res/handson2016_summits.bed
```

### SnpEffによるアノテーション

```
java -jar /usr/local/bin/snpEff.jar eff \
    -csvStats stats.txt \
    -c /usr/local/bin/snpEff.config \
    -i bed \
    -o bedAnn R64-1-1.82 \
    ../macs2_res/handson2016_summits.bed > \
        handson2016_summits.annotated.bed
```

### アノテーションしたファイルの確認

```
less handson2016_summits.annotated.bed
```

## モチーフ探索

```
mkdir ../motif
cd ../motif
```

### rGADEMを使用してモチーフ探索

```
R
```

```r
library(rGADEM)
library("BSgenome.Scerevisiae.UCSC.sacCer3")

BED <- read.table("../macs2_res/handson2016_peaks.narrowPeak", header=FALSE, sep="\t")
BED <- data.frame(chr=as.factor(BED[,1]),start=as.numeric(BED[,2]),end=as.numeric(BED[,3]))

rgBED<-IRanges(start=BED[,2],end=BED[,3])
Sequences<-RangedData(rgBED,space=BED[,1])
gadem<-GADEM(Sequences,verbose=1,genome=Scerevisiae)

pdf("motif.pdf")
plot(gadem)
dev.off()

q()
```

### PDFを確認

```
evince motif.pdf
```
