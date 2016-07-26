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
---
```
fastqc -v

fastqc -h
```
---
```
mkdir rnaseq/fastqc_res

cd rnaseq

fastqc -o fastqc_res -f fastq --nogroup data/10K_SRR2048224_1.fastq data/10K_SRR2048224_2.fastq

fastqc -o fastqc_res -f fastq --nogroup data/10K_SRR2048225_1.fastq data/10K_SRR2048225_2.fastq

fastqc -o fastqc_res -f fastq --nogroup data/10K_SRR2048228_1.fastq data/10K_SRR2048228_2.fastq

fastqc -o fastqc_res -f fastq --nogroup data/10K_SRR2048229_1.fastq data/10K_SRR2048229_2.fastq
```
---
```
ls fastqc_res

firefox fastqc_res/10K_SRR2048224_1_fastqc/fastqc_report.html fastqc_res/10K_SRR2048224_2_fastqc/fastqc_report.html

firefox fastqc_res/10K_SRR2048225_1_fastqc/fastqc_report.html fastqc_res/10K_SRR2048225_2_fastqc/fastqc_report.html

firefox fastqc_res/10K_SRR2048228_1_fastqc/fastqc_report.html fastqc_res/10K_SRR2048228_2_fastqc/fastqc_report.html

firefox fastqc_res/10K_SRR2048229_1_fastqc/fastqc_report.html fastqc_res/10K_SRR2048229_2_fastqc/fastqc_report.html
```
---
##クオリティーコントロール
```
prinseq-lite.pl -h
```
---
```
mkdir 1_qc

prinseq-lite.pl -fastq data/10K_SRR2048224_1.fastq -fastq2 data/10K_SRR2048224_2.fastq -out_good 1_qc/10K_SRR2048224.notail -out_bad null -out_format 3 -trim_left 5 -trim_tail_right 5 -trim_qual_right 30 -ns_max_p 20 -min_len 30

prinseq-lite.pl -fastq data/10K_SRR2048225_1.fastq -fastq2 data/10K_SRR2048225_2.fastq -out_good 1_qc/10K_SRR2048225.notail -out_bad null -out_format 3 -trim_left 5 -trim_tail_right 5 -trim_qual_right 30 -ns_max_p 20 -min_len 30

prinseq-lite.pl -fastq data/10K_SRR2048228_1.fastq -fastq2 data/10K_SRR2048228_2.fastq -out_good 1_qc/10K_SRR2048228.notail -out_bad null -out_format 3 -trim_left 5 -trim_tail_right 5 -trim_qual_right 30 -ns_max_p 20 -min_len 30

prinseq-lite.pl -fastq data/10K_SRR2048229_1.fastq -fastq2 data/10K_SRR2048229_2.fastq -out_good 1_qc/10K_SRR2048229.notail -out_bad null -out_format 3 -trim_left 5 -trim_tail_right 5 -trim_qual_right 30 -ns_max_p 20 -min_len 30
```
---
```
fastqc -o fastqc_res -f fastq --nogroup 1_qc/10K_SRR2048224.notail_1.fastq 1_qc/10K_SRR2048224.notail_2.fastq

fastqc -o fastqc_res -f fastq --nogroup 1_qc/10K_SRR2048225.notail_1.fastq 1_qc/10K_SRR2048225.notail_2.fastq

fastqc -o fastqc_res -f fastq --nogroup 1_qc/10K_SRR2048228.notail_1.fastq 1_qc/10K_SRR2048228.notail_2.fastq

fastqc -o fastqc_res -f fastq --nogroup 1_qc/10K_SRR2048229.notail_1.fastq 1_qc/10K_SRR2048229.notail_2.fastq
```
---
```
firefox fastqc_res/10K_SRR2048224.notail_1_fastqc/fastqc_report.html fastqc_res/10K_SRR2048224.notail_2_fastqc/fastqc_report.html

firefox fastqc_res/10K_SRR2048225.notail_1_fastqc/fastqc_report.html fastqc_res/10K_SRR2048225.notail_2_fastqc/fastqc_report.html

firefox fastqc_res/10K_SRR2048228.notail_1_fastqc/fastqc_report.html fastqc_res/10K_SRR2048228.notail_2_fastqc/fastqc_report.html

firefox fastqc_res/10K_SRR2048229.notail_1_fastqc/fastqc_report.html fastqc_res/10K_SRR2048229.notail_2_fastqc/fastqc_report.html
```
---
##マッピング
```
mkdir 2_mapping

hisat2 -x /home/iu/genome/sacCer3/Hisat2Index/genome --dta --dta-cufflinks -1 1_qc/10K_SRR2048224.notail_1.fastq -2 1_qc/10K_SRR2048224.notail_2.fastq -S 2_mapping/10K_SRR2048224.sam

hisat2 -x /home/iu/genome/sacCer3/Hisat2Index/genome --dta --dta-cufflinks -1 1_qc/10K_SRR2048225.notail_1.fastq -2 1_qc/10K_SRR2048225.notail_2.fastq -S 2_mapping/10K_SRR2048225.sam

hisat2 -x /home/iu/genome/sacCer3/Hisat2Index/genome --dta --dta-cufflinks -1 1_qc/10K_SRR2048228.notail_1.fastq -2 1_qc/10K_SRR2048228.notail_2.fastq -S 2_mapping/10K_SRR2048228.sam

hisat2 -x /home/iu/genome/sacCer3/Hisat2Index/genome --dta --dta-cufflinks -1 1_qc/10K_SRR2048229.notail_1.fastq -2 1_qc/10K_SRR2048229.notail_2.fastq -S 2_mapping/10K_SRR2048229.sam
```
---
```
samtools view -b 2_mapping/10K_SRR2048224.sam > 2_mapping/10K_SRR2048224.bam

samtools view -b 2_mapping/10K_SRR2048225.sam > 2_mapping/10K_SRR2048225.bam

samtools view -b 2_mapping/10K_SRR2048228.sam > 2_mapping/10K_SRR2048228.bam

samtools view -b 2_mapping/10K_SRR2048229.sam > 2_mapping/10K_SRR2048229.bam
```
---
```
samtools sort 2_mapping/10K_SRR2048224.bam -o 2_mapping/10K_SRR2048224.sorted.bam

samtools sort 2_mapping/10K_SRR2048225.bam -o 2_mapping/10K_SRR2048225.sorted.bam

samtools sort 2_mapping/10K_SRR2048228.bam -o 2_mapping/10K_SRR2048228.sorted.bam

samtools sort 2_mapping/10K_SRR2048229.bam -o 2_mapping/10K_SRR2048229.sorted.bam
```
---
```
samtools index 2_mapping/10K_SRR2048224.sorted.bam

samtools index 2_mapping/10K_SRR2048225.sorted.bam

samtools index 2_mapping/10K_SRR2048228.sorted.bam

samtools index 2_mapping/10K_SRR2048229.sorted.bam
```
---
### マッピング結果の可視化

```
igv.sh
```

IGV上でのクリップボードからのペーストは<kbd>ctrl</kbd> + <kbd>v</kbd>で可能です

`chrX:139,767-139,933`
---
##発現定量
```
cufflinks -o SRR2048224 --min-frags-per-transfrag 2 2_mapping/10K_SRR2048224.sorted.bam

cufflinks -o SRR2048225 --min-frags-per-transfrag 2 2_mapping/10K_SRR2048225.sorted.bam

cufflinks -o SRR2048228 --min-frags-per-transfrag 2 2_mapping/10K_SRR2048228.sorted.bam

cufflinks -o SRR2048229 --min-frags-per-transfrag 2 2_mapping/10K_SRR2048229.sorted.bam
```
---
transcripts.gtfの確認
```
ls SRR2048224
less SRR2048224/transcripts.gtf
```

---
transcripts.gtf.txtの作成

```
vim transcripts.gtf.txt
```

Vimにはいくつかのモードが有り、起動した際はノーマルモードです  
<kbd>i</kbd>で挿入モードになると文字の入力ができます

```
./SRR2048224/transcripts.gtf
./SRR2048225/transcripts.gtf
./SRR2048228/transcripts.gtf
./SRR2048229/transcripts.gtf
```
---
入力が完了したら<kbd>ESC</kbd>でノーマルモードに戻ります

保存してVimを終了します  
<kbd>:</kbd>でコマンド入力状態になりますので続けて `wq` を入力して、<kbd>Enter</kbd>


```
:wq
```

w: 保存
q: 終了

Vimについてもっと知りたい方は `vimtutor` を実行してみてください  
充実したチュートリアルを受けることができます
---
##発現比較

```
cuffmerge -o COMPARE -g /home/iu/genome/sacCer3/genes.gtf -s /home/iu/genome/sacCer3/genome.fa  transcripts.gtf.txt
```

```
cuffdiff -o COMPARE -L Group1,Group2 COMPARE/merged.gtf 2_mapping/10K_SRR2048224.sorted.bam,2_mapping/10K_SRR2048225.sorted.bam 2_mapping/10K_SRR2048228.sorted.bam,2_mapping/10K_SRR2048229.sorted.bam
```
---
##可視化
cummeRbundのインストール  
Rを起動

```
source("https://bioconductor.org/biocLite.R")
biocLite("cummeRbund")
library(cummeRbund)
```
---
サマリーの表示

```
cuff <- readCufflinks()
cuff
```
---
Scatter plot

```
s <- csScatter(genes(cuff), "Group1", "Group2", smooth=T)
s
```
---
Density plot

```
dens <- csDensity(genes(cuff))
dens
densRep <- csDensity(genes(cuff),replicates=T)
densRep
```
---
