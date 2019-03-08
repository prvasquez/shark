# Notes

## Downloading data from canada server
First step is transfer data using scp.

SCP base code - to download a file from a serve to local machine
`scp yourusername@yourserver:/home/yourusername/examplefile .`
 
Raw read files are located at `dcole@cedar.computecanada.ca:/home/dcole/project/dcole/RawData/K*`

So I will run
`scp dcole@cedar.computecanada.ca:/home/dcole/project/dcole/RawData/K* .`
This will download the data to /home/prvasque/shark/raw_data/
