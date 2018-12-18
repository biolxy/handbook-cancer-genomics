# 第二章 基因序列比对

{:toc}



## 一、 数据质控(quality control)

直接由bcl序列转化而来的fastq文件，此时被称为原始数据。

在第一章中介绍了fastq文件的格式，其中每第四行代表这其对应read的测序质量，由于种种原因，我们获得原始获数据中包含一下**低质量的reads**（即不可靠的序列），为了保证后续分析的准确性，我们需要将这些reads剔除。

- [从零开始完整学习全基因组测序数据分析：第3节 数据质控](https://zhuanlan.zhihu.com/p/28802083)

- [Insight into biases and sequencing errors for amplicon sequencing with the Illumina MiSeq platform](https://www.ncbi.nlm.nih.gov/pubmed/25586220)

### 1. 为啥要做质控？

测序的目的是为了知道基因的序列，但是测序仪在实际工作中可能会给出“错误”的结果，影响数据质量的因素主要包括：

- 实验设计部分

  - 文库制备方法加上正反向引物的选择，会使特定区域出现核苷酸替换、插入和删除错误
  - pcr循环的次数越高，发生扩增错误的几率也越高
  - DNA聚合酶的效率和特异性导致在reads的尾部，测序质量通常较低
  - reads的前10bp容易发生核苷酸替换类错误
  - 样本的保存
    - 不论组织还是血液，离体后内部的DNA很容易降解，导致测序质量较低，数据量较小

- DNA链损伤

  - DNA复制中的错误

    - > 以DNA为模板按硷基配对进行DNA复制是一个严格而精确的事件，但也不是完全不发生错误的。硷基配对的错误频率约为10-1-10-2，在DNA[复制酶](https://baike.baidu.com/item/%E5%A4%8D%E5%88%B6%E9%85%B6)的作用下[碱基](https://baike.baidu.com/item/%E7%A2%B1%E5%9F%BA)错误配对频率降到约10-5-10-6，复制过程中如有错误的核苷酸参入，[DNA聚合酶](https://baike.baidu.com/item/DNA%E8%81%9A%E5%90%88%E9%85%B6)还会暂停[催化作用](https://baike.baidu.com/item/%E5%82%AC%E5%8C%96%E4%BD%9C%E7%94%A8)，以其3’-5’[外切核酸酶](https://baike.baidu.com/item/%E5%A4%96%E5%88%87%E6%A0%B8%E9%85%B8%E9%85%B6)的活性切除错误接上的核苷酸，然后再继续正确的复制，这种校正作用广泛存在于[原核](https://baike.baidu.com/item/%E5%8E%9F%E6%A0%B8)和真核的DNA聚合酶中，可以说是对DNA复制错误的修复形式，从而保证了复制的准确性。但校正后的[错配](https://baike.baidu.com/item/%E9%94%99%E9%85%8D)率仍约在10-10左右，即每复制1010个核苷酸大概会有一个硷基的错误。

  - 不可避免

- 测序错误

  -  Illumina 测序仪自身出错的几率， 0.2%
  -  不同平台，不同测序仪也各不相同

- 系统误差

  - 实验人员 + 实验设备 + 分析流程（比对错误）

去除掉这些错误信息，我们才能获得准确的分析结果，在临床检测中这一点尤为重要。

### 2. 前质控部分都包括啥？

通常包括：

>- read各个位置的碱基质量值分布
>- 碱基的总体质量值分布
>- read各个位置上碱基分布比例，目的是为了分析碱基的分离程度
>- GC含量分布
>- read各位置的N含量
>- read是否还包含测序的接头序列
>- read重复率，这个是实验的扩增过程所引入的

- adapter 是啥？
  - [高通量测序中的接头（adapter）到底是什么](https://www.jianshu.com/p/3164dca8bd61)

推荐软件

- FastQC 查看数据质量，提供html的报告（十分简陋）
  - FastQC的一个结果模板：[Online report](http://www.bioinformatics.babraham.ac.uk/projects/fastqc/Help/3%20Analysis%20Modules/)
  - 这个建议细看一下每张图的含义
- NGSQCToolkit 实现去接头和trim的功能
- Cutadapter  + Trimmomatic 从名字就看来是干啥的了

下面隆重推荐：

- **fastp** 
  - 同时实现上述所有软件的功能
  - 对paired数据，理论上不需要事先知道接头序列即可去接头
  - 而且比他们快

### 3. 前质控的标准

> 一般来说，对于二代测序，**最好是达到Q20的碱基要在95%以上（最差不低于90%），Q30要求大于85%（最差也不要低于80%）**。

### 4. 其他

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

目前

- 对于ChIP-seq, RNA-seq，多使用bowtie2，因为它快速，下游结合cufflinks等结果验证率很高。

- 对于SNP，Indels，CNV， methylation分析，使用BWA，下游结合GATK可能会好一点。

- [来自](https://www.plob.org/article/7181.html)

  ### 3. bam 和 sam 文件格式

- bam 和 sam 文件格式介绍

  - bam是二进制文件
  - sam为文本文件
  - [sam,bam格式介绍详细](http://boyun.sh.cn/bio/wp-content/uploads/2012/07/SAM1.pdf)
  - [The Sequence Alignment/Map format and SAMtools](https://www.ncbi.nlm.nih.gov/pmc/articles/PMC2723002/)

- 创建 bam index
  - samtools
  - sambamba
    - 速度快

# 引用参考



- [从双序列比对开始学起](https://zhuanlan.zhihu.com/p/35123295)
- [高通量测序中的接头（adapter）到底是什么](https://www.jianshu.com/p/3164dca8bd61)