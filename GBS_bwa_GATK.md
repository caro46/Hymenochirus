`script_bwa_GBS.pl`
```perl
#!/usr/bin/perl                                                                                                             

use warnings;
use strict;

# This script will execute alignment functions for single end reads                                                          
# using bwa mem for reference contigs                                                                                        

my $path_to_data="/net/infofile4-inside/volume1/scratch/evanslab/Hymenochirus_2016/GBS_data/process_radtags_demultiplex/";
my $path_to_genome="/net/infofile2/2/scratch/evanslab_backups/Hymenochirus_2016/abyss/";
my $path_to_output="/net/infofile4-inside/volume1/scratch/evanslab/Hymenochirus_2016/GBS_data/process_radtags_demultiplex/bwa_results/";
my $genome="SOAP_Hymeno_genome_scafSeq_supercontigs.fa";
my @individuals=("BJE3814",
"BJE3815",
"BJE3841",
"BJE3842",
"BJE3843",
"BJE3849",
"BJE3987",
"BJE3990",
"BJE3993",
"BJE3994",
"BJE3996",
"BJE3998",
"BJE3828",
"BJE3829",
"BJE3830",
"BJE3831",
"BJE3832",
"BJE3833",
"BJE3835",
"BJE3838",
"BJE3839",
"BJE3844",
"BJE3845",
"BJE3846",
"BJE3847",
"BJE3848",
"BJE3988",
"BJE3989",
"BJE3989",
"BJE3991",
"BJE3992",
"BJE3995",
"BJE3997",
"BJE3999",
"BJE3078");

#foreach my $individual (@individuals) {print $individual,"\n";}

my $commandline;
my $status;

# Index                                                                                                                      
#$commandline = "bwa index -a bwtsw ".$path_to_genome."\/".$genome;                                           

#$status = system($commandline);                                                                                             

#bwa mem 
foreach my $individual (@individuals) {

$commandline = "bwa mem -t 5 -R \'\@RG\\tID:".$individual."\\tSM:".$individual."\\tLB:library1\\tPL:illumina\' ".$path_to_genome.$genome." ".$path_to_data.$individual."\/".$individual.".fq \> ".$path_to_output.$individual.".sam";
$status = system($commandline);

# Samtools : sam to bam                                                                                                      
$commandline="samtools view -bt ".$path_to_genome.$genome." -o ".$path_to_output.$individual.".bam ".$path_to_output.$individual.".sam";
$status = system($commandline);

# Samtools : bam to _sorted.bam
$commandline="samtools sort ".$path_to_output.$individual.".bam ".$path_to_output.$individual."_sorted";
$status = system($commandline);
$commandline= "samtools index ".$path_to_output.$individual."_sorted.bam";
$status = system($commandline);

##Delete unecessary files
print "Done with individual ",$individual,"\n";
$commandline= "rm -f ".$path_to_output.$individual.".sam ".$path_to_output.$individual.".bam";
$status = system($commandline);

}
```
`script_GATK_GBS.pl`
```perl
!/usr/bin/perl

use warnings;
use strict;

# This script will run GATK on GBS data                                                         
# # producing vcf files

my $path_to_data="/net/infofile4-inside/volume1/scratch/evanslab/Hymenochirus_2016/GBS_data/process_radtags_demultiplex/";
my $path_to_genome="/net/infofile2/2/scratch/evanslab_backups/Hymenochirus_2016/abyss/";
my $path_to_output="/net/infofile4-inside/volume1/scratch/evanslab/Hymenochirus_2016/GBS_data/process_radtags_demultiplex/bwa_results/";
my $genome="SOAP_Hymeno_genome_scafSeq_supercontigs.fa";
my @individuals=("BJE3814",
"BJE3815",
"BJE3841",
"BJE3842",
"BJE3843",
"BJE3849",
"BJE3987",
"BJE3990",
"BJE3993",
"BJE3994",
"BJE3996",
"BJE3998",
"BJE3828",
"BJE3829",
"BJE3830",
"BJE3831",
"BJE3832",
"BJE3833",
"BJE3835",
"BJE3838",
"BJE3839",
"BJE3844",
"BJE3845",
"BJE3846",
"BJE3847",
"BJE3848",
"BJE3988",
"BJE3989",
"BJE3991",
"BJE3992",
"BJE3995",
"BJE3997",
"BJE3999",
"BJE3078");

my $commandline;
my $status;

#Index, make a .dict file

my $path_to_picard = "/usr/local/picard-tools-1.131/";
my $GATK_path = "/usr/local/gatk/";

#$commandline= "samtools faidx ".$path_to_genome."\/".$genome;
#$status = system($commandline);
#$commandline= "java -jar ".$path_to_picard."picard.jar CreateSequenceDictionary REFERENCE="$path_to_genome.$genome" OUTPUT="$path_to_genome."SOAP_Hymeno_genome_scafSeq_supercontigs.dict";

#INDEL realignment

$commandline = "java -Xmx1G -jar ".$GATK_path."GenomeAnalysisTK.jar -T RealignerTargetCreator ";

foreach my $individual (@individuals){
    $commandline = $commandline." -I ".$path_to_output.$individual."_sorted.bam ";
}

$commandline = $commandline."-R ".$path_to_genome.$genome." -o SOAP_Chimerical_forIndelRealigner.intervals";
print $commandline,"\n";
$status = system($commandline);

$commandline = "java -Xmx1G -jar ".$GATK_path."GenomeAnalysisTK.jar -T IndelRealigner ";

foreach my $individual (@individuals){
    $commandline = $commandline." -I ".$path_to_output.$individual."_sorted.bam ";
}

$commandline = $commandline." -R ".$path_to_genome.$genome." --targetIntervals SOAP_Chimerical_forIndelRealigner.intervals --nWayOut _realigned.bam";
print $commandline,"\n";
$status = system($commandline);

# construct a commandline for the HaplotypeCaller; output a vcf file with only variable sites

$commandline = "java -Xmx1G -jar ".$GATK_path."GenomeAnalysisTK.jar -T HaplotypeCaller -R ".$path_to_genome.$genome;
foreach my $individual (@individuals){
    $commandline = $commandline." -I ".$path_to_output.$individual."_sorted_realigned.bam ";
}
$commandline = $commandline."-out_mode EMIT_VARIANTS_ONLY -o ".$path_to_output."SOAP_Chimerical_nonrecal_varonly.vcf";
print $commandline,"\n";
# Execute this command line to call genotypes
$status = system($commandline);

# Now make a new commandline for the BaseRecalibrator, output a table for base recalibration
$commandline = "java -Xmx3G -jar ".$GATK_path."GenomeAnalysisTK.jar -T BaseRecalibrator -R ".$path_to_genome.$genome;
foreach my $individual (@individuals){
    $commandline = $commandline." -I ".$path_to_output.$individual."_sorted_realigned.bam ";
}

$commandline = $commandline."-knownSites ".$path_to_output."SOAP_Chimerical_nonrecal_varonly.vcf -o ".$path_to_output."SOAP_Chimerical_recal_data.table";
print $commandline,"\n";
# Execute this command line
$status = system($commandline);

# Now use PrintReads to output a new concatenated bam file with recalibrated quality scores
$commandline = "java -Xmx3G -jar ".$GATK_path."GenomeAnalysisTK.jar -T PrintReads -R ".$path_to_genome.$genome;

foreach my $individual (@individuals){
    $commandline = $commandline." -I ".$path_to_output.$individual."_sorted_realigned.bam ";
}

$commandline = $commandline."-BQSR ".$path_to_output."SOAP_Chimerical_recal_data.table -o ".$path_to_output."SOAP_Chimerical_concatenated_and_recalibrated_round1.bam";

print $commandline,"\n";
$status = system($commandline);

# Now use HaplotypeCaller to recall bases with recalibrated quality scores; output a vcf file 
$commandline = "java -Xmx3G -jar ".$GATK_path."GenomeAnalysisTK.jar -T HaplotypeCaller -R ".$path_to_genome.$genome;
$commandline = $commandline." -I ".$path_to_output."SOAP_Chimerical_concatenated_and_recalibrated_round1.bam";
$commandline = $commandline." -out_mode EMIT_ALL_CONFIDENT_SITES -o ".$path_to_output."SOAP_Chimerical_recalibrated_round1_allsites.vcf";

print $commandline,"\n";
$status = system($commandline);
```
###Checking quality of mapping:
```
samtools flagstat /net/infofile4-inside/volume1/scratch/evanslab/Hymenochirus_2016/GBS_data/process_radtags_demultiplex/bwa_results/SOAP_Chimerical_concatenated_and_recalibrated_round1.bam >/net/infofile4-inside/volume1/scratch/evanslab/Hymenochirus_2016/GBS_data/process_radtags_demultiplex/bwa_results/SOAP_Chimerical_concatenated_and_recalibrated_round1_flagstat.txt
```
`SOAP_Chimerical_concatenated_and_recalibrated_round1_flagstat.txt`
```
140447386 + 0 in total (QC-passed reads + QC-failed reads)
0 + 0 duplicates
130937358 + 0 mapped (93.23%:-nan%)
0 + 0 paired in sequencing
0 + 0 read1
0 + 0 read2
0 + 0 properly paired (-nan%:-nan%)
0 + 0 with itself and mate mapped
0 + 0 singletons (-nan%:-nan%)
0 + 0 with mate mapped to a different chr
0 + 0 with mate mapped to a different chr (mapQ>=5)
```
When we will have the putative sex-linked scaffolds: blast against tropicalis and each of the sex-specific assembly.
```
module load blast/2.2.28+
blastn -evalue 1e-60 -query Hymenochirus_putative_sex_linked.fa -db /work/ben/2016_Hymenochirus/xenTro9/xenTro9_genome_HARDmasked_blastable -out /work/ben/2016_Hymenochirus/putative_sex_linked_regions_blast/SOAP_chimerical_Hymenochirus_putative_sex_linked_xentrop9 -outfmt 6 -max_target_seqs 1

blastn -evalue 1e-60 -query Hymenochirus_putative_sex_linked.fa -db /work/ben/2016_Hymenochirus/BJE3815/BJE3815_genomeAbyss_blastable -out /work/ben/2016_Hymenochirus/putative_sex_linked_regions_blast/SOAP_chimerical_Hymenochirus_putative_sex_linked_BJE3815abyss -outfmt 6 -max_target_seqs 1
```
***NOTE:***
**HaplotypeCaller vs UnifiedGenotyper**

GATK developers advice to prefer `HaplotypeCaller`. It better deals with INDELs (which is good for us if something is only present in one sex). However it calls sites by "group of sites". Better to use `UnifiedGenotyper` if we set criteria based on number of calling and polymorphic sites. 

**Me:** Do both and compare results. For example: no *only female sites* called with `HaplotypeCaller` but some with `UnifiedGenotyper`.  

**Mark duplicate vs not-marking**

Apparently according to the GATK forum, GBS people regularly ask about marking or not the duplicate for RADseq data. GATK people recently (April 2016) refered to a twit to answer: 

GATK Dev Team

***‏@gatk_dev***
***GATK Dev Team Retweeted brant faircloth***
***We get lots of Qs from RADseq users re marking duplicates before #GATK. Now have something to point ppl to.***

brant faircloth twit:

***Our take on sequence capture based RADseq with duplicate removal and #GATK analysis. Low cost, high consistency***

**Me:** Do both with and without marking to make sure. We lost a lot of information when marking:
```
INFO  19:06:28,628 MicroScheduler - 127624673 reads were filtered out during the traversal out of approximately 131330679 total reads (97.18%)
INFO  19:06:28,628 MicroScheduler -   -> 0 reads (0.00% of total) failing BadCigarFilter
INFO  19:06:28,629 MicroScheduler -   -> 0 reads (0.00% of total) failing BadMateFilter
INFO  19:06:28,629 MicroScheduler -   -> 127624673 reads (97.18% of total) failing DuplicateReadFilter
INFO  19:06:28,629 MicroScheduler -   -> 0 reads (0.00% of total) failing FailsVendorQualityCheckFilter
INFO  19:06:28,630 MicroScheduler -   -> 0 reads (0.00% of total) failing MalformedReadFilter
INFO  19:06:28,630 MicroScheduler -   -> 0 reads (0.00% of total) failing MappingQualityUnavailableFilter
INFO  19:06:28,630 MicroScheduler -   -> 0 reads (0.00% of total) failing NotPrimaryAlignmentFilter
INFO  19:06:28,630 MicroScheduler -   -> 0 reads (0.00% of total) failing UnmappedReadFilter
INFO  19:06:29,968 GATKRunReport - Uploaded run statistics report to AWS S3
```
See if we still have enough data after the filtering. If yes cool (decrease time and memory), if not use not marking stuff. Probably use not marking anyway (see when done running).
