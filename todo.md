# Plan:

- *X. gilli* / *X. laevis* 
- [x] sequence submission - 16S + nfil1 still need to be corrected and submitted (issue with codon stop for 16S, nfil1 some warnings - can be submitted like that but better to try resolving them)
  
- HiSeqX from *Pipa* dad, mom and *Rhyno*: 
- [x] Copy 
- [x] Check quality 
- [x] Trimmo + Scythe + Jellifish/Quake for *Pipa* (info)
- [x] Trimmo + Scythe + Skewer + bbduk + Jellifish/Quake for *Rhyno* **- still failure for the k-mer but the other criteria are OK**
- [x] Assemblies (need to start for *Pipa* on iqaluk) - using 43mers for the following steps
- [x] Assemblies for *Rhyno*: started on Iqaluk (`Segmentation fault` for the 1st try on Aug 2nd, another one running) but issue with Sharcnet on Aug 3rd. - *abyss 64mers finished on Sept.13 but the low coverage seemed to make difficult to resolve the assembly*

- RADseq from *Pipa* dad, mom
- [x] Copy 
- [x] Check quality - some over-represented sequences
- [x] Demultiplex (installing the newest version of stacks that requires a newer version of gcc) 

- Hymenochirus Sex-linked regions
- [x] Extraction
- [ ] PCR - need to try the new primer for Sall1
- [ ] Sequencing 

- Pipa Sex-linked regions
- [ ] Extraction needed?
- [ ] PCR
- [ ] Sequencing

*To do also as soon as possible (in case we need Mark's help)*
- *Mellotropicalis* 
- [x] Installing DBG2OLC on sharcnet (Ben's account)
- [x] Assembling with DBG2OLC using pacbio sequences and the assembly (contigs, not scaffolds) previously made with Allpaths-LG (in theory Allpaths should have better assembly in term of confidence in the real contigs) **- Not able to run the last step because require to create too many files (more than the limit allowed on Sharcnet)**

*26/04: An on-going run on Iqaluk with all the >1000bp pacbio reads*

*30/05: DBG2OLC runs without issue on fasta files until the run sleeps (started sleeping on Friday) on wobbie.*

*31/05: DBG2OLC run finished*

*2/06: DBG2OLC last step starting*

