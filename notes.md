# Notes

## Downloading data from canada server
First step is transfer data using scp.

SCP base code - to download a file from a serve to local machine
`scp yourusername@yourserver:/home/yourusername/examplefile .`
 
Raw read files are located at `dcole@cedar.computecanada.ca:/home/dcole/project/dcole/RawData/K*`

So I will run
`scp dcole@cedar.computecanada.ca:/home/dcole/project/dcole/RawData/K* .`
This will download the data to /home/prvasque/shark/raw_data/

## Run fastqc on all the samples
```
#!/bin/bash -l
#SBATCH -D /home/prvasque/projects/shark/scripts/
#SBATCH -o /home/prvasque/projects/shark/slurm-log/fastqc-stdout-%j.txt
#SBATCH -e /home/prvasque/projects/shark/slurm-log/fastqc-stderr-%j.txt
#SBATCH -J fastqc
#SBATCH --mem=6000
#SBATCH -p med
#SBATCH -t 24:00:00 

set -e
set -u

module load fastqc

DIR='/home/prvasque/projects/shark/raw_data'
OUT='/home/prvasque/projects/shark/data/fastqc'

cd $DIR

for i in `ls *.fastq.gz`; do
	fastqc $i --noextract -o $OUT
done
```
