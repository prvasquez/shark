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

I have to options, only look at the peptide file alignments or take the best E value from the full database alignment. I believe I am supposed to do option #1.

To do this, I have to use the splicing python code in elvers. Here is a good link https://github.com/dib-lab/elvers/issues/131

I will try this and report the code I used here.
