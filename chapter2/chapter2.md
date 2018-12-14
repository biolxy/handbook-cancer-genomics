# 第二章 基因序列比对

{:toc}



## 一、 数据质控(quality control)

直接由bcl序列转化而来的fastq文件，此时被称为原始数据。

在第一章中介绍了fastq文件的格式，其中每第四行代表这其对应read的测序质量，由于种种原因，我们获得原始获数据中包含一下低质量的reads，为了保证后续分析的准确性，我们需要将这些reads剔除。

### 1. 查看fastq文件的总体测序质量

- [从零开始完整学习全基因组测序数据分析：第3节 数据质控](https://zhuanlan.zhihu.com/p/28802083)

推荐软件

- fastq 查看数据质量
- **fastp** 查看数据质量
  - 同时实现 去接头，trim，去低质量序列等功能
  - 相当与  NGSQCToolkit + trimmomatic 等

### 

- 质量控制 quality control
- 去 adapter
- UMI

## 二、短Reads比对软件

### 	1. alignment 和 mapping

> **比对其实应该对应的单词是alignment，**但往往特指低通量的序列之间的比较。比如10条序列，进行多序列比对就是我们常说的 multiple alignment问题；如果是2条序列的比对，我们经常称其为pairwise alignment.
>
> **回贴通常对应的单词应该是mapping，**一般指高通量的数据去寻找基因组的位置。比如我们进行测序以后，有10M对read pair，要去寻找他们在基因组上的位置，这个时候就是一个典型的mapping问题。
>
> --[从双序列比对开始学起](https://zhuanlan.zhihu.com/p/35123295)

因为测序的原因，我们测得的序列(sequence)的长度，通常较短，具体长度和测序仪相关，目前比较常见的是NGS（next generation sequencing，NGS）下一代测序技术的数据，测序长度通常在150bp左右，此时我们需要使用mapping 软件，将这些短的片段回帖到参考基因组上。

### 	2. 常见的短reads mapping软件

| **Resources**     | **URL**                                         |
| ----------------- | ----------------------------------------------- |
| MAQ               | http://maq.sourceforge.net/                     |
| SOAPaligner/soap2 | <http://soap.genomics.org.cn/index.html>        |
| Bowtie            | <http://bowtie-bio.sourceforge.net/index.shtml> |
| BLAT              | <http://genome.ucsc.edu/cgi-bin/hgBlat>         |
| BWA               | http://maq.sourceforge.net/                     |
| BFAST             | http://bfast.sourceforge.net                    |
| SHRiMP            | http://compbio.cs.toronto.edu/shrimp.           |

**备注：**

- **SOAP**, Short Oligonucleotide Analysis Package;

- **MAQ**, Mapping and Assembly with Quality;

- **BLAT**, BLAST-like alignment tool;

- **BWA**, Burrows-Wheeler Alignment;

- **BFAST**, BLAT-like Fast Accurate Search Tool;

- **SHRiMP**, The Short-Read Mapping Package.



  ### 3. bam 和 sam 文件格式

- alignment 和 mapping

- bam 和 sam 文件格式

- 创建 bam index

# 引用参考



- [从双序列比对开始学起](https://zhuanlan.zhihu.com/p/35123295)
- [高通量测序中的接头（adapter）到底是什么](https://www.jianshu.com/p/3164dca8bd61)