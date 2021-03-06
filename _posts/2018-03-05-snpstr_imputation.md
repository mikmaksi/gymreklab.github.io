---
layout: default
title:  'Gymrek Lab - SNPSTR Imputation'
---

# A reference haplotype panel for genome-wide imputation of short tandem repeats

## 1000 Genomes SNP-STR Haplotype Panel

### Data Description:

- 2,504 samples from the [1000 Genomes Project](http://ftp.1000genomes.ebi.ac.uk/vol1/ftp/release/20130502/) Phase 3 SNP Haplotypes
- STRs imputed from 1,916 samples that were part of the <a href="https://www.sfari.org/resource/simons-simplex-collection/">Simons Simplex Collection</a>.
- Total 27,185,239 SNP + 445,725 STR markers
- All the coordinates are based on the b37 human reference genome.

### Availability: Amazon S3 bucket s3://snp-str-imputation/1000genomes
{% for i in (1..22) %}
[[SNP-STR Panel chr{{ i }}](https://s3.amazonaws.com/snp-str-imputation/1000genomes/1kg.snp.str.chr{{ i }}.vcf.gz)] [[chr{{ i }} index](https://s3.amazonaws.com/snp-str-imputation/1000genomes/1kg.snp.str.chr{{ i }}.vcf.gz.tbi)]

{% endfor %}

### Tredparse pathogenic loci
[Huntington’s Disease](https://s3.amazonaws.com/snp-str-imputation/1000genomes/1kg.snp.str.chr4.3076604.vcf.gz) - [index](https://s3.amazonaws.com/snp-str-imputation/1000genomes/1kg.snp.str.chr4.3076604.vcf.gz.tbi)

[Spinocerebellar Ataxia 1](https://s3.amazonaws.com/snp-str-imputation/1000genomes/1kg.snp.str.chr6.16327867.vcf.gz) - [index](https://s3.amazonaws.com/snp-str-imputation/1000genomes/1kg.snp.str.chr6.16327867.vcf.gz.tbi)

[Spinocerebellar Ataxia 17](https://s3.amazonaws.com/snp-str-imputation/1000genomes/1kg.snp.str.chr6.170870996.vcf.gz) - [index](https://s3.amazonaws.com/snp-str-imputation/1000genomes/1kg.snp.str.chr6.170870996.vcf.gz.tbi)

[Dentatorubral-pallidoluysian Atrophy](https://s3.amazonaws.com/snp-str-imputation/1000genomes/1kg.snp.str.chr12.7045892.vcf.gz) - [index](https://s3.amazonaws.com/snp-str-imputation/1000genomes/1kg.snp.str.chr12.7045892.vcf.gz.tbi)

[Spinocerebellar Ataxia 2](https://s3.amazonaws.com/snp-str-imputation/1000genomes/1kg.snp.str.chr12.112036755.vcf.gz) - [index](https://s3.amazonaws.com/snp-str-imputation/1000genomes/1kg.snp.str.chr12.112036755.vcf.gz.tbi)

[Spinocerebellar Ataxia 8](https://s3.amazonaws.com/snp-str-imputation/1000genomes/1kg.snp.str.chr13.70713516.vcf.gz) - [index](https://s3.amazonaws.com/snp-str-imputation/1000genomes/1kg.snp.str.chr13.70713516.vcf.gz.tbi)

[Spinocerebellar Ataxia 3](https://s3.amazonaws.com/snp-str-imputation/1000genomes/1kg.snp.str.chr14.92537355.vcf.gz) - [index](https://s3.amazonaws.com/snp-str-imputation/1000genomes/1kg.snp.str.chr14.92537355.vcf.gz.tbi)

[Spinocerebellar Ataxia 6](https://s3.amazonaws.com/snp-str-imputation/1000genomes/1kg.snp.str.chr19.13318673.vcf.gz) - [index](https://s3.amazonaws.com/snp-str-imputation/1000genomes/1kg.snp.str.chr19.13318673.vcf.gz.tbi)

[Myotonic Dystrophy Type 1](https://s3.amazonaws.com/snp-str-imputation/1000genomes/1kg.snp.str.chr19.46273463.vcf.gz) - [index](https://s3.amazonaws.com/snp-str-imputation/1000genomes/1kg.snp.str.chr19.46273463.vcf.gz.tbi)

### Supplementary Data
Supplementary Tables 2 and 3 give imputation summary statistics for each locus:

[Saini et al. Supplementary Table 2](https://s3.amazonaws.com/snp-str-imputation/1000genomes/Saini_etal_SuppTable2.xlsx)

[Saini et al. Supplementary Table 3](https://s3.amazonaws.com/snp-str-imputation/1000genomes/Saini_etal_SuppTable3.xlsx)

### Usage:
Download [Beagle](https://faculty.washington.edu/browning/beagle/beagle.html) .jar to impute STRs from our reference panel into SNP genotype data. We suggest using the latest version 4.1. If you are working with related samples and want to use pedigree information, use [Beagle version 4.0](https://faculty.washington.edu/browning/beagle/b4_0.html)

Ensure that the alleles in the target SNP file match our reference panel. We suggest using [conform-gt](https://faculty.washington.edu/browning/conform-gt.html). Example:

{% highlight bash %}
java -jar conform-gt.jar \
gt=snp.vcf.gz \
ref=1kg.snp.str.chr1.vcf.gz \
chrom=1 match=POS \
out=snp.chr1.consistent
{% endhighlight %}

Impute STRs into your SNP file:

{% highlight bash %}
java -Xmx8g -jar  beagle.version.jar \
gt=snp.chr1.consistent.vcf.gz \
ref=1kg.snp.str.chr1.vcf.gz \
out=snp.str.chr1.vcf.gz
{% endhighlight %}

## FAQ
**How do I convert Plink BED format files to VCF format?**
Solution: Use [Plink](http://www.cog-genomics.org/plink2) to convert from plink bed to VCF format. Ensure that the reference allele matches our panel.

{% highlight bash %}
REFPANEL=1kg.snp.str.chr1.vcf.gz
zcat ${REFPANEL} | grep -v "^#" | cut -f 3 | grep -v ":" > refpanel_chr1_snps.txt
zcat ${REFPANEL} | grep -v "^#" | awk '($3!~/:/)' | cut -f 1-5 > refpanel_chr1_alleles.txt

plink \
--bfile snp_file \
--recode vcf bgz \
--out snp_file_recoded \
--extract refpanel_chr1_snps.txt \
--real-ref-alleles \
--a2-allele refpanel_chr1_alleles.txt 4 3 '#'
{% endhighlight %}

**Do I need phased SNPs as input?**
No, Beagle will phase the input SNPs during the imputation process.

**How do I measure the STR imputation accuracy?**
We measure the per locus accuracy of imputing STRs from Simon's Simplex Collection into the 1000 Genomes data for three different populations: EUR, EAS and AFR. This data is available in the Amazon S3 bucket mentioned earlier (Saini_etal_SuppTable2.xlsx). You can expect to impute STRs with similar accuracy if your SNP data is from one of these populations.
