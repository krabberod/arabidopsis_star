# Repository for mapping RNA seq to Arabidopsis genomes and hybrds

For loop to make directories for each sample and move the files to the respective directories, using the sample name up to first underscore as the directory name.
    
```bash
for i in $(ls *.fq.gz --color=never | cut -d "_" -f 1 | sort -u); 
do echo $i; 
mkdir $i; 
mv $i* $i; done
```
