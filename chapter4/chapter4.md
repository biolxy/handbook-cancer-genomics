# [第四章 变异过滤](https://github.com/biolxy/handbook-cancer/blob/master/chapter4/chapter4.md)

{:toc}



### GATK的过滤

```shell
$ MEANQUAL=$(awk '{ if ($1 !~ /#/) { total += $6; count++  }  } END { print total/count  }' sample.concordance.raw.vcf)
$ java  -jar GenomeAnalysisTK.jar \
        -R ref.fa \
        -T VariantFiltration \
        --filterExpression "QD < 20.0 || ReadPosRankSum < -8.0 || FS > 10.0 || QUAL < $MEANQUAL" --filterName LowQualFilter \
        --missingValuesInExpressionsShouldEvaluateAsFailing \
        --variant sample.concordance.raw.vcf \
        --logging_level ERROR -o sample.concordance.flt.vcf \
        -log ./log/sample.concordance.flt.vcf.log
$ grep -v "Filter" sample.concordance.flt.vcf > sample.concordance.flt.knowsites.vcf
```

> [变异检测](http://starsyi.github.io/2016/05/25/%E5%8F%98%E5%BC%82%E6%A3%80%E6%B5%8B%EF%BC%88BWA-SAMtools-picard-GATK%EF%BC%89/)



关于SNP的过滤（1）：如何使用vcftools进行SNP过滤

 

来自 <<https://www.jianshu.com/p/e05ff3cace56?utm_campaign=maleskine&utm_content=note&utm_medium=seo_notes&utm_source=recommendation>> 



关于SNP的过滤（2）：如何使用vcftools进行SNP过滤

 

来自 <<https://cdn2.jianshu.io/p/52b2dcb601d2>> 