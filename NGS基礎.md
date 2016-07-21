# NGS��b�f�B���N�g���Ɉړ�
```
cd /home/iu/ngsbasics
ls
```

# IGV�̋N��
```
igv.sh
```

# BAM�t�@�C���̃C���f�b�N�X�쐬
```
ls
samtools sort SRR504515.bam -o SRR504515_sorted.bam
ls
samtools index SRR504515_sorted.bam
ls
```

# FastQC
```
fastqc -h
ls
fastqc  SRR504515_R1.fastq
cd  SRR504515_R1_fastqc/
firefox fastqc_report.html
cd�@../
```

# �}�b�s���O���̊m�F
```
samtools view -b -F 256 SRR504515.bam > SRR504515_uniq.bam
samtools index SRR504515_uniq.bam
samtools idxstats SRR504515_uniq.bam > SRR504515_idxstats.txt
```

# �V�����\�t�g�E�F�A�̓���
```
ls Trimmomatic-0.36.zip
unzip Trimmomatic-0.36.zip
cd Trimmomatic-0.36
ls -l
cd ../
java -jar Trimmomatic-0.36/trimmomatic-0.36.jar
```
