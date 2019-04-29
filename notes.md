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
