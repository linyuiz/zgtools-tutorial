# 1. 🗒Intruduction to zgtools

**zgtools** is a self-developed, highly integrated modular software suite containing over 200 functional modules, comprehensively covering various analysis tasks from conventional genomes to T2T genomes.     
Its application scenarios include but are not limited to: **Survey evaluation, assembly, Hi-C-assisted assembly, T2T complete assembly, gene annotation, evolutionary analysis, centromere prediction, and various customized analyses**. Through a unified launcher interface, zgtools achieves efficient management of full-process analysis tasks, offering greater flexibility and customizability compared to Nextflow workflows.  

🚀Leveraging practical experience from over 200 genome projects, zgtools has deeply optimized numerous standard analysis pipelines and implemented an automated update mechanism, striving to achieve the analytical goal of **"faster, more accurate, and better."**  

🚩To date, zgtools has successfully completed T2T-level genome assemblies for more than 50 species, with related findings published in high-impact journals such as *HR*, *PBJ*, *GB*, and *NC*. For the vast majority of plants, mammals, and fish (including species with ZW/XY sex chromosomes), zgtools can effortlessly achieve complete T2T assembly and accurately resolve PAR (pseudoautosomal regions).  

Additionally, zgtools now offers comprehensive support for **ultra-large genomes** and **autopolyploid T2T**, covering T2T assembly and annotation. Its assembly metrics demonstrate outstanding performance:

⭐️Telomere-to-Telomere (T2T) metrics for a certain gymnosperm (**genome size> 20Gbp**) by【zgtools】:
- Sequencing depth(ONT N50:50kbp)：**60X**；
- **Complete assembly** of telomeres with **0 gap**;
- Contig N50 reaches **1.85 Gbp**;
- Scaffold N50 reaches **2.12 Gbp**;
- Assembly BUSCO completeness is **99.87%**;
- Annotation BUSCO completeness is **99.92%**;

# 2. 🚀Available modules

## 2.1. ⭐️CPhasing-mod
[**CPhasing-mod**](https://github.com/linyuiz/CPhasing-mod) is a modified version of [**C-Phasing**](https://wangyibin.github.io/CPhasing/), featuring the following enhancements:

1️⃣ Refactor the `nextflow + shell` pipeline to improve script readability and maintainability.

2️⃣ Support simultaneous Hi-C/Pore-C clustering analysis across multiple data types, including but not limited to:
`minibwa`, `bwa-mem2`, `_chromap`, `chromap`, `pairs.gz`, and `bam` formats. The pipeline should generate multiple outputs such as `.agp`, `.hic`, and `.assembly` files accordingly.

3️⃣ Enable cross-compatibility among multiple `.assembly` files — they can share the same `.hic` file, while different `.hic` files differ only in interaction signal intensity, not in the underlying assembly structure.

4️⃣ Support both `local` execution and `slurm` cluster deployment, ensuring seamless job scheduling and resource management across environments.

5️⃣ Provide enhanced resumption capabilities for convenient continuation of interrupted runs, minimizing redundant computation and facilitating incremental updates.

⭐️If you encounter any issues, feel free to ask in the issue section. Please also support the original authors. **If you use `CPhasing-mod`, kindly cite it:**   

- **C-Phasing**
    > Yibin Wang, Ping Zhao, Xiaofei Zeng, Jiaxin Yu, Aoqian Dong, Yi Liu, Mengwei Jiang, Fang Wang, Xiao Chen, Shengcheng Zhang, Shuai Chen, Yuqing Gong, Yixing Zhang, Ruicai Long, Maojun Wang, Haibao Tang and Xingtan Zhang. Enhanced Pore-C with C-Phasing Enables Chromosomal-Scale, Haplotype-Resolved Assembly of Ultra-Complex Genomes, 05 November 2025, PREPRINT (Version 1) available at Research Square [https://doi.org/10.21203/rs.3.rs-7343323/v1]

- And HapHiC:
    > Xiaofei Zeng, Zili Yi, Xingtan Zhang, Yuhui Du, Yu Li, Zhiqing Zhou, Sijie Chen, Huijie Zhao, Sai Yang, Yibin Wang, Guoan Chen. Chromosome-level scaffolding of haplotype-resolved assemblies using Hi-C data without reference genomes. Nature Plants, 10:1184-1200. doi: https://doi.org/10.1038/s41477-024-01755-3

- And ALLHiC
    > Xingtan Zhang, Shengcheng Zhang, Qian Zhao, Ray Ming, Haibao Tang. (2019) Assembly of allele-aware, chromosomal-scale autopolyploid genomes based on Hi-C data. Nature Plants, 5:833-845. doi: https://doi.org/10.1038/s41477-019-0487-8

### 2.1.1. ⚙️Installation
To install, first download the latest distribution tarball：[zgtools_latest.tar.gz](https://github.com/linyuiz/zgtools/releases) (not one of the Source code files!) from the github release page：https://github.com/linyuiz/zgtools/releases/. 

```shell
#1. CPhasing install
##mkdir -p ~/software && cd ~/software
mamba create -n cphasing && conda activate cphasing
wget -c https://github.com/wangyibin/CPhasing/releases/download/v0.3.2/CPhasing.v0.3.2.r312.linux-64.tar.gz
tar -zxvf CPhasing.v0.3.2.r312.linux-64.tar.gz && cd CPhasing_v0.3.2
sed -i '1d' environment.yml && mamba env update -f environment.yml
mamba install fastp networkx=3.4.2 seqkit=2.13.0 seqtk mm2plus
#2. download 3D-DNA
##cd ~/software
wget -c https://github.com/aidenlab/3d-dna/archive/refs/tags/201008.tar.gz
tar -zxvf 3d-dna-201008.tar.gz 
#3. nextflow install
mamba create -n nextflow -c conda-forge -c bioconda nextflow==22.10.6
#4. zgtools CPhasing-mod install
tar -zxvf zgtools*.tar.gz && cd zgtools && chmod +x zg*
#5. add zgtools to your $PATH:
echo "export PATH=$PWD:\$PATH" >>~/.bashrc && source ~/.bashrc
zgtools CPhasing-mod
```

### 2.1.2. 📖Usage

You just need to soft link zgtools to your usual bin folder such as【~/bin】, or use an absolute path such as【/project/softawre/zgtools CPhasing-mod】.
```shell
Usage:

        zgtools CPhasing-mod Run_CPhasing.cfg

        Run_CPhasing.cfg   --Run Config

Example1:

        zgtools CPhasing-mod example_cfg

Example2:

        zgtools CPhasing-mod Run_CPhasing.cfg
       
Example3:

        zgtools CPhasing-mod help
```

### 2.1.3. 🚩Example
1️⃣ First, run `zgtools CPhasing-mod example_cfg` to generate a sample configuration file, then modify the parameters below according to your needs:
```
##Info
☆data_work_mode=local                        #local or slurm
☆data_genome_fa=00.data/genome.fa            #genome file
data_fastp_run_mode=yes                       #whether fastp filter HiC data(yes|no)
data_3ddna_run_mode=yes                       #whether create .hic file(yes|no)
☆data_parallel_task_num=2                    #parallel task num
☆data_each_group_chr_num=27                  #each group chr number
☆data_total_group_number=1                   #total group number
##Fastp
fastp_threads=16                              #fastp threads
##C-Phasing
☆cphasing_hyperpartition_mode=haploid        #hyperpartition mode(haploid|phasing)
☆cphasing_hic_aligner=_chromap               #chromap|bwa-mem2|minibwa
☆cphasing_porec_aligner=mm2plus              #minimap2|mm2plus
☆cphasing_threads=30                         #number of threads
☆cphasing_restriction_enzyme=GATC            #REs: GATC|AAGCTT
☆cphasing_input_hic_R1=00.data/hic_R1.fq.gz  #Hi-C data read1(R1.fq.gz)
☆cphasing_input_hic_R2=00.data/hic_R2.fq.gz  #Hi-C data read2(R2.fq.gz)
cphasing_input_pairs=none                     #4DN pairs file(input.pairs.gz)
cphasing_input_bam=none                       #pre-align bam file(input.align.bam)
cphasing_input_porec=none                     #Pore-C/CiFi data(FASTX[.gz]|BAM)
cphasing_hic_mapper_k=17                      #mapper's kmer size(>8G, use 27)
cphasing_hic_mapper_w=7                       #mapper's window size>8G, use 14)
cphasing_hcr_mode=yes                         #retain high confident area(yes|no)
cphasing_hyperpartition_q1=0                  #first cluster min quality(0<=x<=60)
cphasing_hyperpartition_q2=1                  #second cluster min quality(0<=x<=60)
cphasing_scaffolding_method=precision         #scaf_method(cphasing|allhic|fast)
cphasing_plot_binsize=auto                    #bin size of the heatmap(100k|500k|1m)
cphasing_plot_colormap=whitered               #colormap(redp1_r_half|whitered)
cphasing_plot_balance=yes                     #balance the matrix(yes|no)
cphasing_plot_whitered=yes                    #--scale none -cmap whitered(yes|no)
cphasing_plot_no_lines=no                     #whether use --no-lines(yes|no)
cphasing_plot_add_hap_border=yes              #whether add hap border(yes|no)
cphasing_plot_avoid_overlap_yticks=yes        #whether avoid overlap y-ticks(yes|no)
cphasing_plot_fontsize=auto                   #heatmap figure font size(auto|5|10)
cphasing_plot_dpi=300                         #plot figure dpi(150|300)
cphasing_low_memory=yes                       #reduce memory hyperpartition(yes|no)
##3D-DNA
3ddna_mapq=1                                  #build map for a specific mapq(0|1)
3ddna_java_xms=50G                            #java initial heap size
3ddna_java_xmx=750G                           #java maximum heap size
3ddna_min_resolutions=5000                    #minimum resolutions(1000|5000|10000)
3ddna_clean_run_mode=no                       #clean up when done(yes|no)
##CondaEnv
cphasing_env_name=cphasing                    #C-Phasing env name
nextflow_env_name=nextflow                    #nextflow env name
conda_path=~/miniconda3                       #conda envs path
cphasing_path=~/software/CPhasing_v0.3.2      #C-Phasing software path
3ddna_path=~/software/3d-dna-201008           #3D-DNA software path
```
2️⃣ Then, run `zgtools CPhasing-mod Run_CPhasing.cfg`. If want to stop the job, please press `Ctrl + C`, not​ `Ctrl + Z`.           
🚀 Now, `CPhasing-mod` can perform Hi-C scaffolding and Pore-C scaffolding simultaneously!!         

### 2.1.4. 💻Run log

This is the command【`zgtools CPhasing-mod Run_CPhasing.cfg`】runtime log:   
```
#######Data#######
##Info
☆data_work_mode=slurm                        #local or slurm
☆data_genome_fa=00.data/genome.fa            #genome file
data_fastp_run_mode=no                        #whether fastp filter HiC data(yes|no)
data_3ddna_run_mode=yes                       #whether create .hic file(yes|no)
☆data_parallel_task_num=4                    #parallel task num
☆data_each_group_chr_num=23                  #each group chr number
☆data_total_group_number=1                   #total group number
##Fastp
fastp_threads=60                              #fastp threads
##C-Phasing
☆cphasing_hyperpartition_mode=haploid        #hyperpartition mode(haploid|phasing)
☆cphasing_hic_aligner=minibwa,chromap        #chromap|bwa-mem2|minibwa
☆cphasing_porec_aligner=mm2plus              #minimap2|mm2plus
☆cphasing_threads=60                         #number of threads
☆cphasing_restriction_enzyme=GATC            #REs: GATC|AAGCTT
☆cphasing_input_hic_R1=00.data/hic_R1.fq.gz  #Hi-C data read2(R1.fq.gz)
☆cphasing_input_hic_R2=00.data/hic_R2.fq.gz  #Hi-C data read2(R2.fq.gz)
cphasing_input_pairs=none                     #4DN pairs file(input.pairs.gz)
cphasing_input_bam=none                       #pre-align bam file(input.align.bam)
cphasing_input_porec=00.data/pore-c.fq.gz     #disable: Pore-C data(FASTX[.gz]|BAM)
cphasing_hic_mapper_k=17                      #mapper's kmer size(>8G, use 27)
cphasing_hic_mapper_w=7                       #mapper's window size>8G, use 14)
cphasing_hcr_mode=yes                         #retain high confident area(yes|no)
cphasing_hyperpartition_q1=0                  #first cluster min quality(0<=x<=60)
cphasing_hyperpartition_q2=1                  #second cluster min quality(0<=x<=60)
cphasing_scaffolding_method=precision         #scaf_method(cphasing|allhic|fast)
cphasing_plot_binsize=auto                    #bin size of the heatmap(100k|500k|1m)
cphasing_plot_colormap=whitered               #colormap(redp1_r_half|whitered)
cphasing_plot_balance=yes                     #balance the matrix(yes|no)
cphasing_plot_whitered=yes                    #--scale none -cmap whitered(yes|no)
cphasing_plot_no_lines=no                     #whether use --no-lines(yes|no)
cphasing_plot_add_hap_border=yes              #whether add hap border(yes|no)
cphasing_plot_avoid_overlap_yticks=yes        #whether avoid overlap y-ticks(yes|no)
cphasing_plot_fontsize=auto                   #heatmap figure font size(auto|5|10)
cphasing_plot_dpi=300                         #plot figure dpi(150|300)
cphasing_low_memory=yes                       #reduce memory hyperpartition(yes|no)
##3D-DNA
3ddna_mapq=1                                  #build map for a specific mapq(0|1)
3ddna_java_xms=50G                            #java initial heap size
3ddna_java_xmx=750G                           #java maximum heap size
3ddna_min_resolutions=5000                    #minimum resolutions(1000|5000|10000)
3ddna_clean_run_mode=no                       #clean up when done(yes|no)
##CondaEnv
conda_path=~/miniconda3                       #conda envs path
nextflow_env_name=nextflow                    #nextflow env name
cphasing_env_name=cphasing                    #C-Phasing env name
cphasing_path=~/software/CPhasing_v0.3.2      #C-Phasing software path
3ddna_path=~/software/3d-dna-201008           #3D-DNA software path

#######Run#######
1.1. deal with input data ...
Pore-C Statistics:
Seqs_Num    Total_Length(bp)  N50(bp)  Q20(%)  Q30(%)
26,115,610  91,121,862,988    4,326    89.24   81.27

Hi-C Statistics:
Raw_reads      Raw_bases        Clean_reads    Clean_bases      Q20_rate  Q30_rate
1,568,492,706  235,273,905,900  1,567,058,336  234,673,429,504  97.925%   96.526%

2.1. build genome index ...
2.2. align genome to Hi-C/Pore-C data ...
[25/16a5ac] process > align (minimap2) [100%] 3 of 3 ✔
Duration    : 22h 25m 52s

2.3. retain high confidence regions to analysis ...
[88/abe74b] process > hcr (chromap) [100%] 3 of 3 ✔
Duration    : 14m 21s

2.4. convert pairs to contacts ...
[97/57acf8] process > convert_contacts (chromap) [100%] 3 of 3 ✔
Duration    : 8m 31s

3.1. running hyperpartition with【haploid】mode ...
[30/d083b6] process > hyperpartition (minibwa) [100%] 3 of 3 ✔
Duration    : 11m 36s

3.2. running scaffolding with【precision】method ...
[3c/3c16db] process > scaffolding (minimap2) [100%] 3 of 3 ✔
Duration    : 5m 21s

3.3. resort chromosomes by hic cool file ...
[d4/f250ae] process > resort_chr (minimap2) [100%] 3 of 3 ✔
Duration    : 8m 11s

4. generate heatmap figure ...
[89/75e8fc] process > heatmap_plot (chromap) [100%] 3 of 3 ✔
Duration    : 12m 16s

5. 3d-dna generate .hic file for juicebox ...
[6f/76cdfd] process > juicebox (minimap2/q1) [100%] 3 of 3 ✔
Duration    : 1h 5m 36s

#######Results#######
Output: /test/output_of_CPhasing-mod/
```
The Nextflow execution trace in the diagram has been hidden. For the specific time consumed by each process, please refer to the actual run .log file.    
⭐️The above tests were conducted on four nodes, each with 1TB of memory and 256 threads.

---

### 2.1.5. 📦Main output
The main output files are as follows:
```
.
├── cleandata
│   ├── HiC_R1.clean.fq.gz
│   ├── HiC_R2.clean.fq.gz
│   └── Reads_QC_stats.xls
├── juicebox
│   └── (haploid/phasing)
│       └── (used_pairs/used_bam/minibwa/bwa-mem2/chromap/...)
│           ├── heatmap
│           │   ├── original.heatmap.pdf(png/svg)
│           │   ├── use_hcr.sorted.heatmap.pdf(png/svg)
│           │   └── without_hcr.sorted.heatmap.pdf(png/svg)
│           ├── original.q*.hic                                 ⭐️ 1. Juicebox: File-->Open
│           ├── original.raw.assembly                           ⭐️ 2. Juicebox: Assembly-->Import Map Assembly
│           ├── use_hcr.review.assembly                         ⭐️ 3. Juicebox: Assembly-->Import Modified Assembly
│           └── without_hcr.review.assembly                     ⭐️ 3. Juicebox: Assembly-->Import Modified Assembly
└── stat
    └── Reads.stat.xls(txt)
```

## 2.2. ⭐️EDTA-mod

[**EDTA-mod**](https://github.com/linyuiz/EDTA-mod) is a modified version of [**EDTA**](https://github.com/oushujun/EDTA).

For those working on genome transposon annotation, EDTA (Extensive de novo TE Annotator) is a familiar name. It is currently recognized as one of the most accurate annotation pipelines, but the original workflow often presents several frustrating issues during execution:  

**1️⃣Efficiency bottleneck**: Serial execution leads to low CPU utilization, such as in the initial rawTE search phase.  

**2️⃣Catastrophic crashes**: After running for days, it may suddenly throw an error with no solution, even though some errors can be ignored without affecting the results.  

**3️⃣Black-box operations**: A tangled mix of lengthy Shell and Perl commands makes parameter adjustments cumbersome and the code difficult to read.  

To address these problems, we recently undertook a complete overhaul of EDTA—introducing the Nextflow workflow engine alongside Shell scripts, breathing new life into this well-established software.

⭐️If you encounter any issues, feel free to ask in the issue section. Please also support the original authors. **If you use EDTA, kindly cite it:**   

Ou S., Su W., Liao Y., Chougule K., Agda J. R. A., Hellinga A. J., Lugo C. S. B., Elliott T. A., Ware D., Peterson T., Jiang N.✉, Hirsch C. N.✉ and Hufford M. B.✉ (2019). Benchmarking Transposable Element Annotation Methods for Creation of a Streamlined, Comprehensive Pipeline. Genome Biol. 20(1): 275. 

### 2.2.1. ⚙️Installation
To install, first download the latest distribution tarball：[zgtools_latest.tar.gz](https://github.com/linyuiz/zgtools/releases) (not one of the Source code files!) from the github release page：https://github.com/linyuiz/zgtools/releases. 

```shell
#1. EDTA install
mamba create -n EDTA_2.3 && conda activate EDTA_2.3
wget https://github.com/oushujun/EDTA/blob/master/EDTA_2.3.yml && sed -i '1d' EDTA_2.3.yml
#or use [https://github.com/linyuiz/EDTA-mod/blob/master/EDTA_Apr13.yml], [https://github.com/linyuiz/EDTA-mod/blob/master/EDTA_2.3.yml]
mamba env update -f EDTA_2.3.yml
mamba install "pandas<3" tir-learner=3.0.7 repeatmodeler=2.0.5  #issue: https://github.com/oushujun/EDTA/issues/616#issuecomment-3855060533
If your mamba exit with dependency coflicts, you may check out your ~/.condarc file and make sure it use "flexible" solve:
channels:  
  - conda-forge  
  - bioconda  
channel_priority: flexible

#2. nextflow install
mamba create -n nextflow && conda activate nextflow
mamba install -c conda-forge -c bioconda nextflow==22.10.6

#3. If you do not need to run [TEtrimmer], you can skip this step.
##intasll TEtrimmer conda env 
mamba create -n TEtrimmer && conda activate TEtrimmer
mamba install -c bioconda python=3.10 samtools=1.22.1 tetrimmer=1.7.2
##git clone TEtrimmer
git clone https://github.com/qjiangzhao/TEtrimmer.git
##download Pfam data
aria2c -x 10 https://ftp.ebi.ac.uk/pub/databases/Pfam/current_release/Pfam-A.hmm.gz
aria2c -x 10 https://ftp.ebi.ac.uk/pub/databases/Pfam/current_release/Pfam-A.hmm.dat.gz
gzip -d Pfam-A.hmm.gz
gzip -d Pfam-A.hmm.dat.gz
conda activate TEtrimmer
hmmpress Pfam-A.hmm

#4. zgtools install
tar -zxvf zgtools*.tar.gz
cd zgtools && chmod +x zg*
./zgtools EDTA-mod
#You can add zgtools to your $PATH
#If zg-EDTA-mod cannot be found, please edit $ZG_BIN in zgtools
```

### 2.2.2. 📖Usage

You just need to soft link zgtools to your usual bin folder such as【~/bin】, or use an absolute path such as【/project/softawre/zgtools EDTA-mod】.
```shell
Usage:

        zgtools EDTA-mod Run_EDTA.cfg

        Run_EDTA.cfg       --Run Config

Example1:

        zgtools EDTA-mod example_cfg

Example2:

        zgtools EDTA-mod Run_EDTA.cfg
```
⭐️Note regarding genome ID format: Chromosome identifiers should follow formats like Chr1, chr1, Chr1A, or ChrA1. Unanchored sequences (contigs/scaffolds) should be named using the format scaffold1, scaffold2, etc.
🚩Note that the total Threads are threads multiplied by Parallel Task Num, for example: 60 x 3 = 180 threads.    
🚩For a multi-node Slurm cluster, the EDTA conda environment must be installed in the same path on each node to ensure functionality. Alternatively, you can package all the EDTA-mod scripts into a single image and distribute the Slurm tasks using that image.    
🚩If you need a reliable TE library, you can check out: https://github.com/simonorozcoarias/PanTEon/   
🚩Or you can download this file and unzip it：```wget https://www.dfam.org/releases/current/families/Dfam-RepeatMasker.lib.gz```.        

### 2.2.3. 🚩Example
1️⃣ First, run `zgtools EDTA-mod example_cfg` to generate a sample configuration file, then modify the parameters below according to your needs:
```
zgtools EDTA-mod example_cfg

Run_EDTA.cfg:
##Data
work_mode=local                                 #local/slurm
genome_fa=genome.fa                             #genome file
miu_rate=1.3e-8                                 #[plant]Osat:1.3e-8; Atha:7e-9
##RepeatAnno
RepeatModeler2_exist_lib=none                   #none/RM2-families.fa
EDTA_used_curated_TElib=curated.TElib.fa        #none/curated.TElib.fa
EDTA_subtask_threads=60                         #EDTA each task threads
EDTA_parallel_subtask_num=2                     #EDTA parallel subtask number
TEtrimmer_run_mode=run                          #run/skip
TEtrimmer_threads=90                            #TEtrimmer threads
TEtrimmer_path=~/software/TEtrimmer/tetrimmer/  #TEtrimmer path
TEtrimmer_pfam_db=./pfam_db                     #TEtrimmer pfam db path
whether_only_use_TEtrimmer_unknown=yes          #yes/no
RepeatMasker_threads=60                         #RepeatMasker each task threads
RepeatMasker_parallel_num=2                     #RepeatMasker parallel num
whether_count_solo_intact_LTR=run               #run/skip
##CondaEnv
EDTA_env_name=EDTA_2.3                          #EDTA env name
TEtrimmer_env_name=TEtrimmer                    #TEtrimmer env name
Nextflow_env_name=nextflow                      #Nextflow env name
conda_path=/opt/conda                           #Conda Path
```
2️⃣ Then, run `zgtools EDTA-mod Run_EDTA.cfg`. If want to stop the job, please press `Ctrl + C`, not​ `Ctrl + Z`.            
✍️Note:​ You are free to set `TEtrimmer_run_mode`. This will create either a `run_TEtrimmer` or `skip_TEtrimmer` directory under `06.anno/`, where subsequent tasks will be executed without conflicting with other files.

### 2.2.4. 💻Run log

This is the command【```zgtools EDTA-mod Run_EDTA.cfg```】runtime log:   
```
#######Data#######
##Data
☆data_work_mode=slurm                           #local or slurm
☆data_genome_fa=NIP.fa                          #genome file
☆data_miu_rate=1.3e-8                           #[plant]Osat:1.3e-8; Atha:7e-9
☆data_RepeatModeler2_exist_lib=none             #none/RM2-families.fa
☆data_TEtrimmer_run_mode=yes                    #whether run TEtrimmer(yes|no)
☆data_curated_TElib=curated.TElib.fa            #none|curated.TElib.fa
##EDTA
EDTA_subtask_threads=30                          #EDTA each task threads
EDTA_parallel_subtask_num=5                      #EDTA parallel subtask number
##TEtrimmer
TEtrimmer_threads=60                             #TEtrimmer threads
☆whether_only_use_TEtrimmer_unknown=yes         #yes/no
##RepeatMasker
RepeatMasker_threads=30                          #RepeatMasker each task threads
RepeatMasker_parallel_num=5                      #RepeatMasker parallel num
whether_count_solo_intact_LTR=no                 #whether count S/I rate(yes|no)
##CondaEnv
conda_path=~/miniconda3                          #conda path
EDTA_env_name=EDTA_2.3                           #EDTA env name
nextflow_env_name=nextflow                       #nextflow env name
TEtrimmer_env_name=TEtrimmer                     #TEtrimmer env name
TEtrimmer_path=~/software/TEtrimmer/tetrimmer/   #TEtrimmer path
TEtrimmer_pfam_db=~/software/TEtrimmer/pfam_db/  #TEtrimmer pfam db path

#######Run#######
1. transcode genome ...
Genome Size: 385,710,679 bp
2. denovo discover raw TEs ...
2.1. parallel discover TEs, each task threads: 30 ...
[ec/86fbe9] process > discoverTE (LINE) [100%] 4 of 4 ✔
Duration    : 11h 12m 2s

2.2. deal with rawTE output ...
[a0/38a834] process > deal_with (TIR) [100%] 5 of 5 ✔
Duration    : 32m 11s

2.3. check rawTE results ...
2.4. modify LTR insert time ...
LTR insert time file: output_of_EDTA-mod/LTR_insert_time.txt
3. filter raw TE candidates and the make stage 1 library ...
3.1. purify raw LTR/Helitron/TIR ...
3.2. clean other TEs ...
[fd/aacb97] process > FilterTE (purify_LTRint) [100%] 2 of 2 ✔
Duration    : 9m 37s

3.3. clean LINEs and LTRs in SINEs ...
[b5/a4e4c6] process > FilterTE (clean_SINE) [100%] 1 of 1 ✔
Duration    : 7m 16s

3.4. clean LTRs and nonLTRs in TIRs and Helitrons ...
[ca/eca345] process > FilterTE (clean_otherTE) [100%] 1 of 1 ✔
Duration    : 12m 22s

3.5. cluster TIRs and Helitrons and make stg1 raw library ...
[b2/8ff066] process > FilterTE (cleanup_nested) [100%] 1 of 1 ✔
Duration    : 1h 16s

3.6. check stg1 raw library ...
4. merge other TE library ...
4.1. identify remaining TEs in the filtered RM2 library ...
[80/2d02b9] process > MergeTE (clean_RM2) [100%] 1 of 1 ✔
Duration    : 1h 42m 49s

4.2. remove known TEs in the EDTA library ...
[6d/b24698] process > MergeTE (clean_HQlib) [100%] 1 of 1 ✔
Duration    : 11m 12s

5. skip TEtrimmer pipeline ~
6. Post-library annotate ...
6.1. split genome ...
6.2. annotate TEs using RepeatMasker with EDTA results ...
[10/19465d] process > AnnoTE (seq_1.fasta)  [100%] 12 of 12 ✔
Duration    : 1h 50m 8s

6.3. merge RepeatMasker output ...
[58/b4e22a] process > AnnoTE (merge_RMout) [100%] 1 of 1 ✔
Duration    : 1m 11s

6.4. make summary table for the non-overlapping annotation ...
TE anno statistic:
Class     Family         Count    bpMasked     %masked
LTR                      65,073   96,368,187   24.98
          Copia          13,285   14,452,400   3.75
          Gypsy          46,460   80,061,806   20.76
          TRIM           1,213    220,677      0.06
          Bel-Pao        14       6,334        0.00
          ERV            396      53,888       0.01
          unknown        2,680    910,113      0.24
DNA                      304,796  104,385,152  27.06
TIR       CACTA          28,607   18,151,556   4.71
          Mutator        70,245   25,897,029   6.71
          PIF-Harbinger  43,830   10,485,982   2.72
          Tc1-Mariner    58,214   15,184,198   3.94
          hAT            24,971   7,334,824    1.90
          unknown        11,953   2,879,052    0.75
NonTIR    Helitron       60,881   25,976,035   6.73
LINE                     15,249   7,115,770    1.84
          R2             642      57,728       0.01
          RTE            622      100,561      0.03
          L1             7,663    4,330,360    1.12
          unknown        4,555    2,417,750    0.63
SINE                     7,012    1,401,742    0.36
          tRNA           1,464    234,088      0.06
          7SL            0        0            0.00
          5S             0        0            0.00
          unknown        5,434    1,175,809    0.30
Unknown                  2,055    671,718      0.17
Total TE                 398,701  212,330,356  55.05

6.5. generate masked genome ...
6.6. skipped calculate solo/intact LTR ratio ...

#######Results#######
Output: /EDTA/output_of_EDTA-mod/
```
The Nextflow execution trace in the diagram has been hidden. For the specific time consumed by each process, please refer to the actual run .log file.    
⭐️The above tests were conducted on four nodes, each with 1TB of memory and 256 threads.

<img alt="c4c7c44d-2e63-42df-bdc8-10469442ada6" src="https://github.com/user-attachments/assets/76bc64ad-9959-4515-84da-cc414f7676cb" />

### 2.2.5. 📦Main output
The output EDTA.TElib.fa is recommended to be adjusted using ```TEtrimmer``` for better TE annotation results.
The output files are basically consistent with the EDTA output results, and the ⭐️-marked files are those commonly used by most people.
```
├── 01.EDTA.raw
│   ├── genome.fa.mod.EDTA.intact.raw.fa        ⭐️
│   ├── genome.fa.mod.EDTA.intact.raw.gff3      ⭐️
│   ├── genome.fa.mod.Helitron.intact.raw.bed
│   ├── genome.fa.mod.Helitron.intact.raw.fa
│   ├── genome.fa.mod.Helitron.intact.raw.fa.anno.list
│   ├── genome.fa.mod.Helitron.intact.raw.gff3
│   ├── genome.fa.mod.LINE.raw.fa
│   ├── genome.fa.mod.LTR.intact.raw.fa
│   ├── genome.fa.mod.LTR.intact.raw.fa.anno.list
│   ├── genome.fa.mod.LTR.intact.raw.gff3
│   ├── genome.fa.mod.LTR.raw.fa
│   ├── genome.fa.mod.RM2.fa
│   ├── genome.fa.mod.SINE.raw.fa
│   ├── genome.fa.mod.TIR.intact.raw.bed
│   ├── genome.fa.mod.TIR.intact.raw.fa
│   ├── genome.fa.mod.TIR.intact.raw.fa.anno.list
│   └── genome.fa.mod.TIR.intact.raw.gff3
├── 02.EDTA.combine
│   ├── genome.fa.mod.EDTA.fa.stg1
│   ├── genome.fa.mod.EDTA.intact.fa.cln
│   ├── genome.fa.mod.Helitron.intact.raw.fa.cln
│   ├── genome.fa.mod.Helitron.intact.raw.fa.int.cln
│   ├── genome.fa.mod.LINE.raw.fa
│   ├── genome.fa.mod.LTR.intact.raw.fa.cln
│   ├── genome.fa.mod.LTR.raw.fa.cln
│   ├── genome.fa.mod.SINE.raw.fa.cln
│   ├── genome.fa.mod.TIR.intact.raw.fa.cln
│   └── genome.fa.mod.TIR.intact.raw.fa.int.cln
├── 03.EDTA.final
│   ├── genome.fa.mod.EDTA.TElib.fa
│   ├── genome.fa.mod.EDTA.TElib.merge.fa  ⭐️ (HQlib + EDTA_denovo_lib) 
│   └── genome.fa.mod.EDTA.TElib.novel.fa  ⭐️  
├── 04.EDTA.anno
│   ├── genome.fa.mod.EDTA.TEanno.gff3  ⭐️
│   ├── genome.fa.mod.EDTA.TEanno.out   ⭐️
│   ├── genome.repeat_hard_masked.fa    ⭐️
│   └── genome.repeat_soft_masked.fa    ⭐️   
│   └── genome.fa.mod.EDTA.TEanno.sum   ⭐️
└── LTR_insert_time.txt                 ⭐️
```

## 2.3. ⭐️GINGER-mod
[**GINGER-mod**](https://github.com/linyuiz/GINGER-mod) is a modified version of [**GINGER**](https://github.com/i10labtitech/GINGER), featuring the following enhancements:

1️⃣ Refactor the `nextflow + shell` pipeline to improve script readability and maintainability.

2️⃣ Support both `local` execution and `slurm` cluster deployment, ensuring seamless job scheduling and resource management across environments.

3️⃣ The current output will provide a UTR-containing GFF file along with more detailed GFF statistical results.

4️⃣ Added comparison of elements such as gene/CDS/intron/exon between individual evidence GFFs and the final GFF.

5️⃣ Provide enhanced resumption capabilities for convenient continuation of interrupted runs, minimizing redundant computation and facilitating incremental updates.

6️⃣ Fixed some software bugs and adjusted the parallelization strategy.

⭐️If you encounter any issues, feel free to ask in the issue section. Please also support the original authors. **If you use `GINGER-mod`, kindly cite it:**   
Taniguchi T, Okuno M, Shinoda T, Kobayashi F, Takahashi K, Yuasa H, Nakamura Y, Tanaka H, Kajitani R, Itoh T. GINGER: an integrated method for high-accuracy prediction of gene structure in higher eukaryotes at the gene and exon level. DNA Res. 2023 Aug 1;30(4) <https://doi.org/10.1093/dnares/dsad017>

### 2.3.1. ⚙️Installation
To install, first download the latest distribution tarball：[zgtools_latest.tar.gz](https://github.com/linyuiz/zgtools/releases) (not one of the Source code files!) from the github release page：https://github.com/linyuiz/zgtools/releases/. 

```shell
#1. nextflow install
mamba create -n zgtools -c conda-forge -c bioconda nextflow=21.04.0=h4a94de4_0 singularity=3.8.6=hf1ae047_0
#2. zgtools GINGER-mod install
tar -zxvf zgtools*.tar.gz && cd zgtools && chmod +x zg*
#3. add zgtools to your $PATH:
echo "export PATH=$PWD:\$PATH" >>~/.bashrc && source ~/.bashrc
zgtools GINGER-mod
```

### 2.3.2. 📖Usage

You just need to soft link zgtools to your usual bin folder such as【~/bin】, or use an absolute path such as【/project/softawre/zgtools GINGER-mod】.
```shell
Usage:

        zgtools GINGER-mod Run_GINGER.cfg

        Run_GINGER.cfg     --Run Config

Example1:

        zgtools GINGER-mod example_cfg

Example2:

        zgtools GINGER-mod Run_GINGER.cfg
```

### 2.3.3. 🚩Example
1️⃣ First, run `zgtools GINGER-mod example_cfg` to generate a sample configuration file, then modify the parameters below according to your needs:
```
Run_GINGER.cfg:
##Info
☆data_work_mode=slurm                         #local/slurm
☆data_max_retries=3                           #nextflow max retries(0|3)
☆data_genome_fa=00.data/genome_unmask.fa      #unmask genome
☆data_mask_genome=00.data/genome_masked.fa    #soft-masked(A->a) genome
☆data_RM_out=00.data/genome_RM.out            #RepeatMasker output(.out|none)
☆data_rnaseq_list=00.data/used.trans.list     #RNAseq list
☆data_homopep_list=00.data/used.homo.list     #homology protein list
##TransAnno
hisat_para_n=5                                 #hisat parallel task num
hisat_each_threads=60                          #hisat each task threads(multi tasks)
stringtie_threads=60                           #stringite_threads(only 1 task)
trinity_threads=60                             #trinity threads(only 1 task)
trinity_memory=200G                            #trinity max memory
gmap_threads=60                                #gmap threads(only 1 task)
##HomoAnno
spaln_para_n=5                                 #spaln parallel task num
spaln_each_threads=30                          #spaln threads(multi tasks)
##DenovoAnno
augustus_train_threads=60                      #augustus train threads(only 1 task)
augustus_predict_threads=60                    #augustus predict threads
##Images
images_bind="-B /project"
images_path=~/images/zgtools_v0.1.6.sif
```
Note that `genome_RM.out` can be used with the annotation results from [EDTA-mod](https://github.com/linyuiz/EDTA-mod).    
2️⃣ Edit `example.used.trans.list`; currently, only second-generation transcriptomes are supported, and full-length transcriptomes are not supported.    
```
fastq   5-1     /your/absolute/path/5-1_raw_1.fq.gz,/your/absolute/path/5-1_raw_2.fq.gz
fastq   6-1     /your/absolute/path/6-1_raw_1.fq.gz,/your/absolute/path/6-1_raw_2.fq.gz
```
3️⃣ Edit `example.used.homo.list`; you can include any number of protein files from closely related species, but generally it is recommended to include 5-6 high-quality annotations.    
```
Pezizomy        Morchella_angusticeps   /your/absolute/path/Morchella_angusticeps.pep.fa
Pezizomy        Morchella_brunnea       /your/absolute/path/Morchella_brunnea.pep.fa
Pezizomy        Morchella_eximia        /your/absolute/path/Morchella_eximia.pep.fa
```
Translate the following text:
Note: The first column is mandatory for Spaln. Only modify it after checking `example.splan.check.txt`:    
| Abbreviation | English Translation |
|:---:|:---:|
| Angiosp | Angiosperms (Arabidopsis, rice, maize, etc.) |
| Magnolio | Monocots (rice, maize, sorghum, etc.) |
| Eudicoty | Eudicots (Arabidopsis, tomato, soybean, etc.) |
| Tetrapod | Tetrapods (human, mouse, chicken, frog, etc.) |
| Fish_mdl | Fish – model species (zebrafish, medaka, etc.) |
| InsectDm | Insects – *Drosophila* group (*Drosophila* and relatives) |
| NematodC | Nematodes – *Caenorhabditis* group (*C. elegans* and relatives) |
| Saccharo | Yeast – *Saccharomyces* group (*Saccharomyces* and relatives) |
| Schizosa | Yeast – *Schizosaccharomyces* group (fission yeast) |
| FungiAll | Fungi – general purpose (for non-specific fungal groups) |
| ChordatC | Urochordates (sea squirt, *Ciona*, etc.) |
| ChordatU | Cephalochordates / invertebrate chordates (amphioxus, etc.) |
| Echinode | Echinoderms (sea urchin, starfish, etc.) |
| Mollusca | Mollusks (oyster, abalone, etc.) |
| InsectAc | Insects – Hymenoptera (ants, bees, etc.) |
| InsectAg | Insects – aphids/true bugs (aphids, stink bugs, etc.) |
| InsectBm | Insects – Lepidoptera (silkworm, etc.) |
| Aconoida | Apicomplexans (Toxoplasma, Plasmodium; no trypanosomes) |
| Archamoe | Amoebozoa / amoebae (amoeba, Leishmania, etc.) |
| Conoidas | Apicomplexans – trypanosomes (Trypanosoma, etc.) |
          
4️⃣ run `zgtools GINGER-mod Run_GINGER.cfg`. If want to stop the job, please press `Ctrl + C`, not​ `Ctrl + Z`.      

### 2.3.4. 💻Run log
This example was tested using the rice T2T-NIP genome. You can download the genome and transcriptome data here:    
Genome: https://ngdc.cncb.ac.cn/gwh/ncbi_assembly/2417393/show   
RNA data: https://ngdc.cncb.ac.cn/gsa/browse/insdc/SRA1669491/SRX20950525   
```shell
#######Run#######
N E X T F L O W  ~  version 21.04.0
========== Run_GINGER.cfg ==========
##Info
☆data_work_mode=slurm                         #local/slurm
☆data_sample_name=NIP                         #sample name
☆data_max_retries=2                           #nextflow max retries
☆data_genome_fa=00.data/genome_unmask.fa      #unmask genome(must)
☆data_mask_genome=00.data/genome_masked.fa    #soft-masked(A->a) genome(must)
☆data_RM_out=00.data/genome_RM.out            #RepeatMasker output(.out|none)
☆data_rnaseq_list=00.data/used.trans.list     #RNAseq list
☆data_homopep_list=00.data/used.homo.list     #homology protein list
##TransAnno
hisat_para_n=3                                 #hisat parallel task num
hisat_each_threads=30                          #hisat each task threads(multi tasks)
stringtie_threads=30                           #stringite_threads(only 1 task)
trinity_threads=60                             #trinity threads(only 1 task)
trinity_memory=200G                            #trinity max memory
gmap_threads=60                                #gmap threads(only 1 task)
##HomoAnno
spaln_para_n=3                                 #spaln parallel task num
spaln_each_threads=30                          #spaln threads(multi tasks)
##DenovoAnno
augustus_train_threads=60                      #augustus train threads(only 1 task)
augustus_predict_threads=60                    #augustus predict threads
##Images
images_bind="-B /project"
images_path=00.data/zgtools_v0.1.6.sif

====================================
temp dir   = /project/test/NIP/tmp_of_GINGER-mod
output dir = /project/test/NIP/output_of_GINGER-mod
[d2/d7dbc3] process > hisat_index (NIP)      [100%] 1 of 1 ✔
[f3/76e731] process > hisat_align (NIP-rna)  [100%] 1 of 1 ✔
[2b/3601fd] process > bam_merge (NIP)        [100%] 1 of 1 ✔
[10/88af2a] process > stringtie (NIP)        [100%] 1 of 1 ✔
[1a/e47953] process > trans_orf (NIP)        [100%] 1 of 1 ✔
[68/e3c852] process > learn_1st (NIP)        [100%] 1 of 1 ✔
[f4/c0ae99] process > learn_2nd (NIP)        [100%] 1 of 1 ✔
[e3/3be0db] process > trinity (NIP)          [100%] 1 of 1 ✔
[ba/949e7a] process > trans_align (NIP)      [100%] 1 of 1 ✔
[72/2fc7f3] process > spaln (ZS97RS3)        [100%] 4 of 4 ✔
[58/90445c] process > homology_filter (NIP)  [100%] 1 of 1 ✔
[fe/9f6ae9] process > augustus_train (NIP)   [100%] 1 of 1 ✔
[ab/b3cfa4] process > augustus_prepare (NIP) [100%] 1 of 1 ✔
[fc/00b3dd] process > augustus_predict (NIP) [100%] 1 of 1 ✔
[16/e05cf9] process > snap (learn_2nd)       [100%] 1 of 1 ✔
[e7/9c7b72] process > prepare_evidence (NIP) [100%] 1 of 1 ✔
[6c/998f08] process > reconstruct (NIP)      [100%] 1 of 1 ✔
[01/75536c] process > finalize_genes (NIP)   [100%] 1 of 1 ✔
[1f/74a4c7] process > gff_distribution (NIP) [100%] 1 of 1 ✔
Duration    : 6h 27m 46s
CPU hours   : 616.7 

Transcriptome Alignment Rate:
ID          MapRate(%)
NIP-rna     94.73%

Final GINGER-mod Predicted Results:
Item                                     Number
the total number of gene                 34,058
the total gene length (bp)               89,709,567
the average of gene length (bp)          2,634.02
the average cds length of per gene (bp)  1,289.33
the average exon number of per gene      4.40
the average of exon length (bp)          292.89
the average of intron length (bp)        395.25
the total number of exon                 149,927
the total number of intron               115,869
the total exon length (bp)               43,912,149
the total intron length (bp)             45,797,418

Notice:
If you have any questions, you can ask in the issue section at: https://github.com/linyuiz/GINGER-mod

#######Results#######
Output: /project/test/NIP/output_of_GINGER-mod/
```

### 2.3.5. 📦Main Output
```
.
├── GINGER.gene.cds.fa               CDS fasta
├── GINGER.gene.pep.fa               protein fasta
├── GINGER.gene.UTRs.gff             longest gff3(have UTRs)
├── GINGER.gene.UTRs.gtf             longest gtf(have UTRs)
├── GINGER.gene.gff                  longest gff3(none UTRs)
├── GINGER.gene.gtf                  longest gtf(none UTRs)
├── GINGER.gene.stat.*               longest gff3 statistics
├── GINGER.gene.gff_distribution.*   longest gff3 distribution
└── GINGER.trans.map_stat.xls        transcriptome mapping rate
```
⭐️Below is a comparison of the annotation results of the GINGER-mod version with those of other rice genome versions：     
| ID | All | Longest | BUSCO-embryophyta_odb10 |
|:---:|:---:|:---:|:---:|
| Azucena | 51,365 | 34,725 | C:91.6%[S:89.3%,D:2.4%],F:3.4%,M:5.0% |
| MH63RS3 | 89,083 | 57,080 | C:86.1%[S:83.4%,D:2.7%],F:2.9%,M:11.0% |
| IRGSP1.0 | 51,210 | 26,232 | C:73.9%[S:72.2%,D:1.7%],F:4.5%,M:21.6% |
| ZS97RS3 | 88,145 | 58,871 | C:89.9%[S:87.9%,D:2.0%],F:2.0%,M:8.1% |
| Kitaake | 48,494 | 35,048 | C:99.2%[S:96.9%,D:2.3%],F:0.4%,M:0.4% |
| Y476 | 37,592 | 34,569 | C:95.3%[S:93.3%,D:2.0%],F:0.7%,M:4.0% |
| ZH11 | 67,633 | 56,948 | C:96.8%[S:94.2%,D:2.6%],F:1.4%,M:1.9% |
| T2T-NIP | 67,818 | 57,120 | C:91.4%[S:89.2%,D:2.2%],F:4.9%,M:3.7% |
| T2T-NIP(GINGER-mod) | 34,058 | 34,058 | C:98.5%[S:96.1%,D:2.4%],F:0.1%,M:1.4% |

