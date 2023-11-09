## Setup

Run on **terra**.

```plain
export PATH=/opt/common/bic/miniconda3/bin:$PATH
source activate /home/wilson/.conda/envs/snakemakev5 ## Manda already set up snakemake env successfully
module load singularity/3.7.1
module load perl/perl-5.22.0  ## added 10/30/2023

#conda deactivate
```

Make a copy of `run.sh` and modify your copy to point to your project directory, config file, cluster job submission command, target file and log file. 
```plain
cp run.sh run_KasuminonDAC_test.sh

#open and modify variables in run_KasuminonDAC_test.sh

WD=/juno/work/bic/byrne/pg2/20231023_new_cmd                                    
CONFIG=${WD}/2023-Oct-12-KasuminonDAC.yaml                                      
TARGET=out/experiment/novel_analysis/proteome_blast.outfmt6                     
LOG=${WD}/2023-Oct-12-KasuminonDAC.snakemake.out                                
CLUSTER="bsub -J {params.J} -n {params.n} -R 'span[hosts=1] rusage[mem={params.mem_per_cpu}]' -W 144:00 -o {params.o} -eo {params.eo}"

...
```

For our test run, the final command was:
```plain
ProteomeGenerator2> snakemake --snakefile ProteomeGenerator2.py --cluster "bsub -J {params.J} -n {params.n} -R 'span[hosts=1] rusage[mem={params.mem_per_cpu}]' -W 144:00 -o {params.o} -eo {params.eo}" -j 100 -k --ri --latency-wait 30 --configfile /juno/work/bic/byrne/pg2/20231023_new_cmd/2023-Oct-12-KasuminonDAC.yaml  --use-conda --use-singularity --singularity-args "--bind /juno:/juno,/work:/work,/home:/home,/scratch:/scratch" out/experiment/novel_analysis/proteome_blast.outfmt6 > /juno/work/bic/byrne/pg2/20231023_new_cmd/2023-Oct-12-KasuminonDAC.snakemake.out 2>&1
```

## Run 
```plain
# run in background as entire pipeline will take a few days
nohup ./run.sh &
```

### Changes made from original instructions
- reordered channels in envs/crossmap.yaml
- replaced channel 'bioconda' with 'bioconda/label/cf201901' in envs/transdecoder.yaml
- load perl prior to running (`module load perl/perl-5.22.0`)
- added /work to singularity bind paths
- put snakemake command in shell script: run.sh; variables will need to be configured in this script but then pipeline can be started and restarted as necessary with simple command `./run.sh`
  
