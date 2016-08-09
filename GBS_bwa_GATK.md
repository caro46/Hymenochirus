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
