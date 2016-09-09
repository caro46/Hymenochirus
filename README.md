# Hymenochirus - Sex determination system:

Sex-determination system is know to evolved quickly in the *Xenopus*. However the ancestral state of the system is not know. Adding multiple species (*Hymenochirus*, *Pipa*), outgroup of this genus will help us to infer the ancestral state.

## 1- Preparing the data:
#### -[HiSeq](https://github.com/caro46/Hymenochirus/blob/master/Starting.Rmd): Trimmomatic, Quake, FastQC - [GBS](https://github.com/caro46/Hymenochirus/blob/master/RADseq.Rmd): RADpools, STACKS.

## 2- [Assembling HiSeq reads](https://github.com/caro46/Hymenochirus/blob/master/Assembly.Rmd):
#### -Reference guided: Stampy, de novo: Abyss, SOAPdenovo
#### -[Created bigger scaffolds for GATK](https://github.com/caro46/Hymenochirus/blob/master/supercontigs.Rmd) (see )

## 3- [Transposable elements](https://github.com/caro46/Hymenochirus/blob/master/Repeat_elements.Rmd)
#### De novo: RepARK + TEclass, on assembly: repeatMasker

## 4- Genotype calling: [GATK](https://github.com/caro46/Hymenochirus/blob/master/Genotype_calls.Rmd)

## 5- [Sex-linked regions identification](https://github.com/caro46/Hymenochirus/blob/master/Sex_linked_regions_identification.Rmd)

