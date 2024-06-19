# Repository for mapping RNA seq to Arabidopsis genomes and hybrds

For loop to make directories for each sample and move the files to the respective directories, using the sample name up to first underscore as the directory name.
    
```bash
for i in $(ls *.fq.gz --color=never | cut -d "_" -f 1 | sort -u); 
do echo $i; 
mkdir $i; 
mv $i* $i; done
```

# Mapping RNA seq using star, example for 

```bash
#!/bin/sh
#SBATCH --job-name=star
#SBATCH --account=nn9525k
#SBATCH --output=slurm-%j.base
##SBATCH --nodes=1
#SBATCH --cpus-per-task=16
#SBATCH --time=15:00:00
#SBATCH --mem-per-cpu=6G
##SBATCH --partition=bigmem

set -o errexit
set -o nounset

START=$(date +%s)
echo "###################################################"
echo "# Started: "
echo "#     $(date)"
echo "###################################################"
echo ""
module purge
module load STAR/2.7.11a-GCC-12.3.0

GENOME=/cluster/projects/nn9525k/ida/star_db/Arabidopsis_arenosa
OUT=arenosa_
STR=$(pwd | sed 's/.*\///')
echo "Sample is $STR"
echo "GENOME is $GENOME"

R1=$1
R2=$2
echo "R1 and R2 are: " $R1 $R2

STAR --genomeDir $GENOME \
     --readFilesIn $1 $2 \
     --runThreadN $SLURM_CPUS_PER_TASK \
     --outFileNamePrefix $STR.$OUT \
     --outSAMtype BAM SortedByCoordinate \
     --quantMode GeneCounts \
     --readFilesCommand zcat


# Computing runtime
secs=$(($(date +%s)-$START))
echo ""
echo "######################"
echo "Script finished: "
echo "    $(date)"
echo "Running time:"
#echo "#     $(($(date +%si)-$START)) seconds"
printf '%dd:%dh:%02dm:%02ds\n' $(($secs/86400)) $(($secs%86400/3600)) $(($secs%3600/60)) $(($secs%60))
echo "######################"
```