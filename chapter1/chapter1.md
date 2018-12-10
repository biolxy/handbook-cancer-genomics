# chapter1

# 第一章

{:toc}

　　Illumina测序仪下机的数据通常为bcl格式，是将同一个测序通道（Lane）所有样品的数据混杂在一起的，Bcl文件，本质上是一个巨大图片。使用Illumina官方出品的Bcl2FastQ软件，可将bcl文件转换为fastq文件（依旧是多个sample的混合文件），再根据Index序列（一般为6个base的序列），将其拆分为单个样品的FastQ文件。

##  一、Illumina下机数据格式转化

拆分的过程需要如下几个文件：

- 原始数据文件夹： `180612_NB502066_0002_AH2CC7AFXY`

  ```shell
  $ le 180612_NB502066_0002_AH2CC7AFXY
        1 总用量 96
        2 drwxrwxr-x  9 lixy bioinf_group  4096 6月  13 17:40 ./
        3 drwxrwxr-x 58 lixy bioinf_group  4096 12月  5 10:37 ../
        4 drwxr-xr-x  2 lixy bioinf_group  4096 6月  13 09:10 Config/
        5 drwxr-xr-x  3 lixy bioinf_group  4096 6月  13 09:10 Data/
        6 drwxr-xr-x  3 lixy bioinf_group  4096 6月  13 09:15 Images/
        7 drwxr-xr-x  2 lixy bioinf_group  4096 6月  13 09:53 InterOp/
        8 drwxr-xr-x  2 lixy bioinf_group  4096 6月  13 09:15 Logs/
        9 drwxr-xr-x  2 lixy bioinf_group  4096 6月  13 09:15 Recipe/
       10 -rwxr--r--  1 lixy bioinf_group    46 6月  13 02:57 RTAComplete.txt*
       11 -rwxr--r--  1 lixy bioinf_group  5712 6月  13 02:57 RTAConfiguration.xml*
       12 drwxr-xr-x  2 lixy bioinf_group  4096 6月  13 09:15 RTALogs/
       13 -rwxr--r--  1 lixy bioinf_group    36 6月  13 02:57 RTARead1Complete.txt*
       14 -rwxr--r--  1 lixy bioinf_group    36 6月  13 02:57 RTARead2Complete.txt*
       15 -rwxr--r--  1 lixy bioinf_group    36 6月  13 02:57 RTARead3Complete.txt*
       16 -rwxr--r--  1 lixy bioinf_group    36 6月  13 02:57 RTARead4Complete.txt*
       17 -rwxr--r--  1 lixy bioinf_group   928 6月  13 04:57 RunCompletionStatus.xml*
       18 -rwxr--r--  1 lixy bioinf_group 10195 6月  13 02:57 RunInfo.xml*
       19 -rwxr--r--  1 lixy bioinf_group 11386 6月  13 02:57 RunParameters.xml*
       20 -rwxrw-r--  1 root   root           872 6月  13 17:40 SampleSheet.csv*
  
  ```

未完……

## 二、Fastq格式介绍

![fastq](./chapter1/fastq.jpg)

**维基百科的介绍：**

- 中文： https://zh.wikipedia.org/wiki/FASTQ%E6%A0%BC%E5%BC%8F
- 英文： https://en.wikipedia.org/wiki/FASTQ_format

fastq文件中，每隔4行可以看作一个单位，其中第二行表示测到的碱基(A,T,C,G,N 其中N表示任意核酸Nucleic acid)，第四行表示对应碱基位置测序的信号强弱，也就是碱基质量，总体上是越高越好。



## 三、不同质量编码标示之间的换算

![1544426946819](./chapter1/quality.png)

不同测序平台给定的质量编码表示不同，各平台间测序质量换算如下：

```shell
Command line conversions
FASTQ to FASTA format:

zcat input_file.fastq.gz | awk 'NR%4==1{printf ">%s\n", substr($0,2)}NR%4==2{print}' > output_file.fa
Illumina FASTQ 1.8 to 1.3

sed -e '4~4y/!"#$%&'\''()*+,-.\/0123456789:;<=>?@ABCDEFGHIJ/@ABCDEFGHIJKLMNOPQRSTUVWXYZ[\\]^_`abcdefghi/' myfile.fastq   # add -i to save the result to the same input file
Illumina FASTQ 1.3 to 1.8

sed -e '4~4y/@ABCDEFGHIJKLMNOPQRSTUVWXYZ[\\]^_`abcdefghi/!"#$%&'\''()*+,-.\/0123456789:;<=>?@ABCDEFGHIJ/' myfile.fastq   # add -i to save the result to the same input file
Illumina FASTQ 1.8 raw quality to binned quality (HiSeq Qtable 2.10.1, HiSeq 4000 )

sed -e '4~4y/!"#$%&'\''()*+,-.\/0123456789:;<=>?@ABCDEFGHIJKL/))))))))))----------77777<<<<<AAAAAFFFFFJJJJ/' myfile.fastq   # add -i to save the result to the same input file
Illumina FASTQ 1.8 raw quality to clinto format (a visual block representation)

sed -e 'n;n;n;y/!"#$%&'\''()*+,-.\/0123456789:;<=>?@ABCDEFGHIJKL/▁▁▁▁▁▁▁▁▂▂▂▂▂▃▃▃▃▃▄▄▄▄▄▅▅▅▅▅▆▆▆▆▆▇▇▇▇▇██████/' myfile.fastq   # add -i to save the result to the same input file
```

## 四、 fastq文件的拆分

从bcl转换而来的fastq是一个混合了多个样本的fastq文件，为了区分各样本，在建库的时候引入了index，为不同样本加上不同的，拆分是可通过 index 将其区分，最后生成各样本单独的index。

- index 

- adapter

# 参考

- http://bioinformatics.cvr.ac.uk/blog/tag/bcl2fastq/