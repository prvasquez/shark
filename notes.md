# Notes

## Downloading data from canada server
First step is transfer data using scp.

SCP base code - to download a file from a serve to local machine
`scp yourusername@yourserver:/home/yourusername/examplefile .`
 
Raw read files are located at `dcole@cedar.computecanada.ca:/home/dcole/project/dcole/RawData/K*`

So I will run
`scp dcole@cedar.computecanada.ca:/home/dcole/project/dcole/RawData/K* .`
This will download the data to /home/prvasque/shark/raw_data/

## Elvers

https://github.com/dib-lab/elvers

```
source activate /home/prvasque/miniconda3/envs/elvers-env
```

### Running Elvers
This will run whatever workflow is in the shark.yaml file.
* Use `-n` as a prerun to make sure everything works
```
elvers shark.yaml --slurm_config slurm_cluster.yml --cluster_cmd --forceall
```



Made config files on farm.

Output of `wc -l` on my pretrimmed files

```
    8552647 K03_a_1.fq.gz
    9261749 K03_a_2.fq.gz
    5000971 K04_a_1.fq.gz
    5438762 K04_a_2.fq.gz
    6257258 K07_a_1.fq.gz
    6996715 K07_a_2.fq.gz
   10179272 K08_a_1.fq.gz
   10952201 K08_a_2.fq.gz
   12470348 K11_a_1.fq.gz
   13543030 K11_a_2.fq.gz
   10351820 K12_a_1.fq.gz
   11459496 K12_a_2.fq.gz
    8428069 K15_a_1.fq.gz
    9209859 K15_a_2.fq.gz
    6734789 K16_a_1.fq.gz
    7407953 K16_a_2.fq.gz
    8609392 K17_a_1.fq.gz
    9440984 K17_a_2.fq.gz
   10414546 K18_a_1.fq.gz
   11609902 K18_a_2.fq.gz
   10813979 K19_a_1.fq.gz
   11766865 K19_a_2.fq.gz
    7770536 K20_a_1.fq.gz
    8643745 K20_a_2.fq.gz
    9981701 K21_a_1.fq.gz
   11094878 K21_a_2.fq.gz
    6921859 K23_a_1.fq.gz
    7562829 K23_a_2.fq.gz
    7864412 K24_a_1.fq.gz
    8641573 K24_a_2.fq.gz
  273382140 total
```

Need to figure out which samples are biological reps.


### Elvers default script

```
#!/bin/bash -l
#SBATCH -D /pylon5/mc5phkp/pvasquez/projects/shark/
#SBATCH -J shark_default
#SBATCH -o /pylon5/mc5phkp/pvasquez/slurm/out/shark_default.txt
#SBATCH -e /pylon5/mc5phkp/pvasquez/slurm/err/shark_default.txt
#SBATCH -t 240:00:00
#SBATCH -p LM
#SBATCH -N 1
#SBATCH -n 1
#SBATCH --mem=1000GB
#SBATCH -c 32

conda activate elvers-env

cd /pylon5/mc5phkp/pvasquez/projects/shark

elvers shark.yaml
```

### Elvers annotate script
```
#!/bin/bash -l
#SBATCH -D /pylon5/mc5phkp/pvasquez/projects/shark/
#SBATCH -J shark_annotate
#SBATCH -o /pylon5/mc5phkp/pvasquez/slurm/out/shark_annotate.txt
#SBATCH -e /pylon5/mc5phkp/pvasquez/slurm/err/shark_annotate.txt
#SBATCH -t 240:00:00
#SBATCH -p LM
#SBATCH -N 1
#SBATCH --ntasks-per-node=8
#SBATCH --mem=1000GB
#SBATCH -c 32
#SBATCH -C EGRESS

conda activate elvers-env

cd /pylon5/mc5phkp/pvasquez/projects/shark

elvers shark.yaml assemble annotate
```

# 11/12/19

Hello World.

I have rerun the annotation with the elephant shark peptide file. This gives me the intermediate file output of 

```
(base) [pvasquez@login018 shark_trinity.fasta.dammit]$ ls
annotate.doit.db                               shark_trinity.fasta.transdecoder_dir
dammit.log                                     shark_trinity.fasta.transdecoder.gff3
run_shark_trinity.fasta.metazoa.busco.results  shark_trinity.fasta.transdecoder.pep
shark_trinity.fasta                            shark_trinity.fasta.x.Callorhinchus_milii.Callorhinchus_milii-6.1.3.pep.all.fa.crbl.csv
shark_trinity.fasta.dammit.gff3                shark_trinity.fasta.x.Callorhinchus_milii.Callorhinchus_milii-6.1.3.pep.all.fa.crbl.gff3
shark_trinity.fasta.dammit.stats.json          shark_trinity.fasta.x.Callorhinchus_milii.Callorhinchus_milii-6.1.3.pep.all.fa.crbl.model.csv
shark_trinity.fasta.transdecoder.bed           shark_trinity.fasta.x.Callorhinchus_milii.Callorhinchus_milii-6.1.3.pep.all.fa.crbl.model.plot.pdf
shark_trinity.fasta.transdecoder.cds           tmp
```

My understanding is that `shark_trinity.fasta.x.Callorhinchus_milii.Callorhinchus_milii-6.1.3.pep.all.fa.crbl.gff3` is the file that has the recriprical best blasts of my transcripts to the peptide file.

`shark_trinity.fasta.dammit.gff3` is the recriptical best blasts of the transcripts to all the data bases offered by dammit.

I have two options, only look at the peptide file alignments or take the best E value from the full database alignment. I believe I am supposed to do option #1.

To do this, I have to use the splicing python code in elvers. Here is a good link https://github.com/dib-lab/elvers/issues/131 and here https://dib-lab.github.io/elvers/dammit/

I will try this and report the code I used here. First I must activate the enviornment that has all the python libraries loaded. To do this `source activate dammit`. **Is this the right one????** I believe so, but might be wrong. After, I have to activate the python code enviornment. `pyton`

```
(base) [pvasquez@login018 shark_trinity.fasta.dammit]$ source activate dammit
(dammit) [pvasquez@login018 shark_trinity.fasta.dammit]$ python
Python 3.7.3 | packaged by conda-forge | (default, Mar 27 2019, 23:01:00) 
[GCC 7.3.0] :: Anaconda, Inc. on linux
Type "help", "copyright", "credits" or "license" for more information.
>>> import pandas as pd

>>> from dammit.fileio.gff3 import GFF3Parser
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
ModuleNotFoundError: No module named 'dammit'
```

So I believe it is the wrong conda enviornment. Time to choose a new one.

11/24/19

Activate Conda env
```
(base) [pvasquez@login005 pvasquez]$ source activate /pylon5/mc5phkp/pvasquez/miniconda3/envs/elvers-env

python
>>> import pandas as pd
>>> from dammit.fileio.gff3 import GFF3Parser
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
ModuleNotFoundError: No module named 'dammit'
```
Same issue as last time, I emailed Tessa for help.

1/24/20

Tessa said pandas should be updated. I need to reinstall the dammit enviornment and then install pandas.

1/27/20

Met with Tessa, she helped me setup my dammit conda enviornment on my lcoal computer as well as gave insight into next step (splicing my annotation file).

1/28/20

### What I need to do
* Research for Lisa's scripts on github on how she did her splicing.
* Start learning how to use jupyter notebook to write and save scripts
* Make sure all my files are in the correct place

It seems that bridges is having trouble as I am unable to connect by ssh and my globus app cannot connect. Confirmed: It is down for maintenence today.

#### Transrate

In the future I should use transrate on my transcriptome. http://hibberdlab.com/transrate/getting_started.html

#### Lisa's github scripts

This may be a good starting point, although I am having trouble opening the files. https://github.com/WhiteheadLab/osmotic/tree/master/notebooks


2/4/20

I downloaded the 3 gff3 files from bridges to my computer. They consisted of
1. The dammit gff3 file
2. The transrate gff3 file
3. The great white blast gff3 file

Based on andrew's suggustion I am going to collapse the reads by the gff3 file. I will follow the same steps lisa took in this thread, https://github.com/dib-lab/elvers/issues/131.

Limiting step right now is the GFF3Parser.read step.

2/11/20

The GFF3Parser.read step sucks and always causes my jupyter notebook to crash. I do not know why, I have emailed Tessa to see if she knows. For now, I will try to do the parsing in Rstudio using some notes from BIS180L lab last year. https://jnmaloof.github.io/BIS180L_web/2019/04/23/R-Practice-Blast/

I also talked to Andrew last week and i realized I annotated my files using the wrong protein fasta. The correct one I should be using is `proteins_from_10kbp_scaffolds.fasta`. I am re-reunning the script now. With that running, I will use the incorrect annotation file to start preparing a script so once I get the correct file it should be easy to collapse. 

***JUST KIDDING*** I already have the `shark_trinity.fasta.x.proteins_from_10kbp_scaffolds.fasta.crbl.gff3` file on my local computer.

2/17/20

The splicing RMD is coming along well. I need to figure out numbers to keep tract of everything.

* `shark_trinity.fasta` - This is my transcriptome that is on bridges currently. It has 1,097,683 sequences. Sequence example 
```
>TRINITY_DN323845_c0_g1_i1 len=431 path=[0:0-430]
GGAATTTGATACATGTGTGCTAAGTGTTTGTATGATAACATTGCTGACAGTCATTTCTACCCTCATGTCCTTTAAATTGTGTCTTTCTCCAGCTACTTACCTCTGGAACTCATTTATATTATCTGCTTCAGTGACCTAGTCTGATGACTTATTCCATATGTTTGATACTCCTTGTGTGAAGTGGTTCTGCCTGAATTCCCTATTAACTCCTAATTTTCAGATTTATATCTCCTAATTTTTGAATCCTTTATCAGTGTAAACAAATTATCTAGATCAGCTTTAGCAAATCCATTCATGGTGAAAGCAAGTATGGCTTCTTCAATAATTCCTTATAGCTTAGATTTCCGAGACCTGTTATCAGCTTTGTTGATCTATACTATGGGTTCTCAAGTGCAATAATATAGCCCTGATATTCCTTTCTCTCTTTCCCT
```
* `proteins_from_10kbp_scaffolds.fasta` - Great white shark proteome. Has 24,520 protein sequences. This is what I used for my annotation. Sequence example
```
>augustus_masked-ScRDnwc_10016_342179-processed-gene-2.1-mRNA-1 protein AED:0.13 eAED:0.13 QI:0|0|0|0.5|0|0|2|0|124
MSPGFRCEGLYGNVVHSGTFPVGRVHRLLRKGNYAERVGAGAPVYLAAVLEYLTAEILELAGNAARDNKKTRIIPRHLQLAVRNDEELNKLLGGVTIAQGGVLPNIQAVLLPKKTSAAAGSKTK
```

* `shark_trinity.fasta.x.proteins_from_10kbp_scaffolds.fasta.crbl.gff3` - My transcriptome annotated with the Great white shark proteome. Has 159,728 matches. Example
```
TRINITY_DN133975_c0_g1_i10 len=4352 path=[0:0-585 2:586-586 4:587-988 6:989-1336 9:1337-1337 10:1338-1655 11:1656-1657 12:1658-1658 13:1659-3580 14:3581-3976 16:3977-3977 17:3978-3978 19:3979-4097 20:4098-4351]	shmlast.LAST	conditional_reciprocal_best_LAST	125	245	2.700000e-79	+	.	ID=homology:0;Name=maker-ScRDnwc_11311_1269623-snap-gene-4.2-mRNA-1 protein AED:0.09 eAED:0.09 QI:0|0|0|0.75|0|0|4|0|144;Target=maker-ScRDnwc_11311_1269623-snap-gene-4.2-mRNA-1 protein AED:0.09 eAED:0.09 QI:0|0|0|0.75|0|0|4|0|144 23 144 +;database=proteins_from_10kbp_scaffolds.fasta
```


* `full_maker_annotation_file.gff` - Great white shark annotation file. I can use this to link my annotations to GO terms. Example
```
ScRDnwc_29106_354205	.	contig	1	354205	.	.	.	ID=ScRDnwc_29106_354205;Name=ScRDnwc_29106_354205
###
ScRDnwc_29106_354205	maker	gene	67388	139107	.	+	.	ID=maker-ScRDnwc_29106_354205-snap-gene-2.6;Name=maker-ScRDnwc_29106_354205-snap-gene-2.6;Dbxref=InterPro:IPR005821,Pfam:PF00520;Ontology_term=GO:0005216,GO:0006811,GO:0016020,GO:0055085
ScRDnwc_29106_354205	maker	mRNA	67388	139107	.	+	.	ID=maker-ScRDnwc_29106_354205-snap-gene-2.6-mRNA-1;Parent=maker-ScRDnwc_29106_354205-snap-gene-2.6;Name=maker-ScRDnwc_29106_354205-snap-gene-2.6-mRNA-1;_AED=0.37;_eAED=0.37;_QI=0|0|0|0.4|0|0|5|0|377;Dbxref=InterPro:IPR005821,Pfam:PF00520;Ontology_term=GO:0005216,GO:0006811,GO:0016020,GO:0055085;Alias=maker-ScRDnwc_29106_354205-snap-gene-2.6-mRNA-1
```

2/20/20

Went to D-rug to get advice from taylor. She showed me how to use gsub to remove all the excess stuff.

2/24/20

Tessa suggested I cluster my trinity assembly using CD-hit so that I reduce the number of transcripts.

I created an environment in bridges for cdhit `conda create -n cdhit -c conda-forge -c bioconda cd-hit`. Then `conda activate cdhit` to get into my enviornment. Here is the url for cdhit user guide https://github.com/weizhongli/cdhit/wiki/3.-User's-Guide. I am using the cd-hit-est function because my transcriptome is a DNA fasta.

```
cd-hit-est -i shark_trinity.fasta -o shark_trinity95.fasta -c 0.95 -d 0 -M 2000`
```

Using `-c 0.95` means I want 95% similarity sequences to cluster
Using `-d 0` reduces the length of the description in the final file. I believe this will make the isoform name in trinity go from `TRINITY_DN323882_c0_g1_i1 len=514 path=[0:0-513]` to `TRINITY_DN323882_c0_g1_i1`.
Using `-M 2000` set my memory limit to 2000MBs.

This is taking a while, I will have to write a script and run it through bash.


