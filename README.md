# How to cite
This sponge EMP blast database has been created for a recent publication. If you use the database, please cite this article:
Dat TTH, Steinert G, Thi Kim Cuc N, Smidt H, Sipkema D. (2018) Archaeal and bacterial diversity and community composition from 18 phylogenetically divergent sponge species in Vietnam. PeerJ 6:e4970 https://doi.org/10.7717/peerj.4970


# spongeEMP_BLASTdb 1.0
SpongeEMP deblurred subOTUs BLAST database


## 1.) Sequence data source
The sponge microbiome project (see https://academic.oup.com/gigascience/article/6/10/1/4082886) deblurred biom file "final.withtax.biom" was downloaded from https://github.com/amnona/SpongeEMP/tree/master/sponge_emp/data


## 2.) Biom to tab separeted text file transformation
The biom file was converted to a tsv file:

*biom convert -i final.withtax.biom -o final.withtax.tsv --to-tsv --header-key taxonomy*

Subsequently, the stored OTU representative sequence information was extracted from this text file and stored in a separated fasta file "final.withtax.v12.fa"


## 3.) Representative sequence classification using Silva128
Representative sequences for each deblurred OTU was classified using mothur v.1.39.5 https://www.mothur.org/wiki/Download_mothur and silva128 https://www.mothur.org/wiki/Silva_reference_files#Release_128

__a)__ First just a simple classification without chimera search and removal of Mitochondria-Chloroplast-Eukaryota-unknown sequences

*classify.seqs(fasta=final.withtax.v12.fa, template=silva.nr_v128.align, taxonomy=silva.nr_v128.tax, cutoff=80, probs=T, processors=6)*

taxlevel	rankID	taxon	daughterlevels	total
0	0	Root	4	83908
1	0.1	Archaea	10	1025
1	0.2	Bacteria	53	64429
1	0.3	Eukaryota	13	915
1	0.4	unknown	1	17539

__b)__ Chimera search
Creating name file to use EMP sequences as template for chimera search

*unique.seqs(fasta=final.withtax.v12.fa)*

Chimera search and removal

*chimera.vsearch(fasta=final.withtax.v12.fa, name=final.withtax.v12.names, dereplicate=t, processors=6)*

Using 6 processors.
Checking sequences from final.withtax.v12.fa ...
When using template=self, mothur can only use 1 processor, continuing.
vsearch v2.3.4_linux_x86_64, 15.6GB RAM, 8 cores
https://github.com/torognes/vsearch

Reading file final.withtax.v12.temp 100%  
8390800 nt in 83908 seqs, min 100, max 100, avg 100
Masking 100%  
Sorting by abundance 100%
Counting unique k-mers 100%  
Detecting chimeras 100%  
Found 0 (0.0%) chimeras, 83908 (100.0%) non-chimeras,
and 0 (0.0%) borderline sequences in 83908 unique sequences.
Taking abundance information into account, this corresponds to
0 (0.0%) chimeras, 83908 (100.0%) non-chimeras,
and 0 (0.0%) borderline sequences in 83908 total sequences.

It took 27 secs to check 83908 sequences. 0 chimeras were found.

__c)__ emoval of certain sequences
Removing Mitochondria-Chloroplast-Eukaryota-unknown sequences from seq file

*remove.lineage(fasta=final.withtax.v12.fa, taxonomy=final.withtax.v12.nr_v128.wang.taxonomy, taxon=Mitochondria-Chloroplast-Eukaryota-unknown)*

Output File Names: 
final.withtax.v12.nr_v128.wang.pick.taxonomy
final.withtax.v12.pick.fa

final.withtax.v12.fa	83908 OTUs
final.withtax.v12.pick.fa	64424 OTUS


## 4.) Creating the local spongeemp BLAST database
__a)__ Go to the BLAST download folder and download the BLAST executables for your operating system: ftp://ftp.ncbi.nlm.nih.gov/blast/executables/blast+/LATEST/ - unzip this archive.

__b)__ Copy the fasta file into the ncbi-blast-x.x.x bin folder.

__c)__ Open your terminal and go to the bin folder. Use the following command to create the spongeemp nucleotide BLAST database:

*makeblastdb -in final.withtax.v12.pick.fa -out DeblurV12PickOTUsSilva128 -dbtype 'nucl' -hash_index*

