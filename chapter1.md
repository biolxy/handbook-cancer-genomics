# chapter1

# 第一章

　　Illumina测序仪下机的数据通常为bcl格式，是将同一个测序通道（Lane）所有样品的数据混杂在一起的，Bcl文件，本质上是一个巨大图片。使用Illumina官方出品的Bcl2FastQ软件，可将bcl文件转换为fastq文件（依旧是多个sample的混合文件），再根据Index序列（一般为6个base的序列），将其拆分为单个样品的FastQ文件。

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



## 参考

- http://bioinformatics.cvr.ac.uk/blog/tag/bcl2fastq/