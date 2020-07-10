# Exhaustive Variant Analysis (EVA) on Human Genome Sequences

The goal of this [NSF RAPID project](https://nsf.gov/awardsearch/showAward?AWD_ID=2034247) is to democratize genome sequence analysis so
that researchers can better understand how COVID-19 affects individuals based on their genetic makeup. Using CloudLab, researchers can perform
variant analysis on genomes ***at no charge***. A recent genomic-wide association study linked genes and blood type of individuals
to risk of severe COVID-19 [(NEJM '2020)](https://www.nejm.org/doi/full/10.1056/NEJMoa2020283).

## Running variant analysis on human genomes using a single CloudLab node

1. Create an account on CloudLab by signing up [here](https://cloudlab.us/signup.php).  Select "Join Existing Project" with `EVA-public` as the project name.
(If you already have a CloudLab account, then join the project `EVA-public` after logging in to CloudLab.)
<!--[(Screenshot)](images/CloudLab_signup.png?raw=true)("CloudLab Signup")-->
2. By signing up, you agree to follow the [Acceptable Use Policy of CloudLab](https://cloudlab.us/aup.php).
3. After your account is approved, you can login to your account. Read the [CloudLab manual](http://docs.cloudlab.us/) on how to start an experiment.
4. Start an experiment using the profile `EVA-single-node-profile` on CloudLab. (Or just click [here](https://www.cloudlab.us/p/8d74b0b9-bfd5-11ea-b1eb-e4434b2381fc).)
You will need to select a node/hardware type such as `xl170` (Utah), `c240g5` (Wisc), etc. Also provide your CloudLab user name. Check the box to agree to using only deidentified data.
It will take a few minutes to start the experiment; so please be patient.

5. Go to your experiment and in `Topology View` click the node icon and open a shell/terminal to connect to that node.
Alternatively, you can use `SSH` to login to the node: `$ ssh -i /path/to/CloudLab/private_key_file  CloudLab_username@CloudLab_hostname`.
(You can also run [ssh-agent](https://www.ssh.com/ssh/agent) on your local machine to add your private key.)

6. Run the following commands on the shell:

    **a.** Clone the repo.

       $ git clone https://github.com/MU-Data-Science/EVA.git

    **b.** Set up all the tools such as [bwa](https://github.com/lh3/bwa), [samtools](https://github.com/samtools/samtools), [sambamba](https://github.com/biod/sambamba), [Freebayes](https://github.com/ekg/freebayes), etc. Feel free to modify our scripts if you intend to use other tools for variant analysis.

       $ ~/EVA/scripts/setup_tools.sh

    **c.** Change directory to local block storage as we need ample space for running variant analysis.

       $ cd /mydata

    **d.** Set up and index the reference genome (e.g., hs38, hs38a, hs37). This is a one-time step and can take a hour or so depending on the node hardware type. To avoid killing the process when the SSH session terminates due to disconnection, use the `screen` command.

       $ ~/EVA/scripts/setup_reference_genome.sh hs38

    **f.** Now copy a whole genome sequence (paired-end) to the CloudLab node. It is the user's responsibility to ensure that the data are de-identified prior to storing them on the CloudLab node. Also see the [Acceptable Use Policy of CloudLab](https://cloudlab.us/aup.php). As an example, let's use whole genome sequences from [The 1000 Genomes Project](https://www.internationalgenome.org/). The FTP site is `ftp://ftp.1000genomes.ebi.ac.uk/vol1/ftp/phase3/data`.

       $ wget ftp://ftp.1000genomes.ebi.ac.uk/vol1/ftp/phase3/data/HG00096/sequence_read/SRR062635_1.filt.fastq.gz
       $ wget ftp://ftp.1000genomes.ebi.ac.uk/vol1/ftp/phase3/data/HG00096/sequence_read/SRR062635_2.filt.fastq.gz

    If you want to try a whole exome sequence (paired-end), here is an example.

       $ wget ftp://ftp.sra.ebi.ac.uk/vol1/fastq/SRR077/SRR077312/SRR077312_1.fastq.gz
       $ wget ftp://ftp.sra.ebi.ac.uk/vol1/fastq/SRR077/SRR077312/SRR077312_2.fastq.gz 

    If you have deidentified sequences on your local machine to analyze, copy to the CloudLab node using `scp`.

    **g.** Run the variant analysis script by passing the required arguments. This script will perform the alignment, sorting, marking duplicates, and variant calling.  Here is an example.

       $ ~/EVA/scripts/run_variant_analysis.sh hs38 SRR062635

    **h.** The output of variant analysis is stored in a `.output.vcf` file. Download to your local machine using `scp`.

       $ scp  -i /path/to/CloudLab/private_key_file  CloudLab_username@CloudLab_hostname:/path/to/the/output_VCF_file

    Any `.vcf` file can be further processed using readily available tools such as [scikit-allel](http://alimanfoo.github.io/2017/06/14/read-vcf.html), [VCFtools](https://vcftools.github.io/index.html), and [GATK](https://gatk.broadinstitute.org/hc/en-us/articles/360036711531-VariantsToTable). You can also use visualization tools such as [IGV](https://software.broadinstitute.org/software/igv/download).

    **i.** To perform variant analysis on more genome sequences, go to Step **f**.

### Simple steps to run the screen command

    $ screen -s my_session_name
    $ ~/EVA/scripts/run_variant_analysis.sh hs38 SRR062635

   Press "Ctrl-a" "d" (i.e., control-a followed by d) to detach from the screen session.

   To reattach, do either of the following.

    $ screen -r my_session_name            OR
    $ screen -r

   To check list of screen sessions, type the following.

    $ screen -ls

## Running variant analysis on a cluster of CloudLab nodes

***🚧 💻 Under active development 💻 🚧***

We are currently working with [Apache Spark](https://spark.apache.org), [Apache Hadoop](https://hadoop.apache.org), and [Adam/Cannoli](http://bdgenomics.org/) to enable large-scale variant analysis on CloudLab.


## Issues?

Please report them [here](https://github.com/MU-Data-Science/EVA/issues).

## Team

**Faculty:** Drs. Praveen Rao (**PI**), Deepthi Rao, Peter Tonellato, Wesley Warren, and Eduardo Simoes

**Ph.D. Students:** Arun Zachariah

# Acknowledgments
This work is supported by the National Science Foundation under [Grant No. 2034247](https://nsf.gov/awardsearch/showAward?AWD_ID=2034247).

## References
1. https://github.com/ekg/alignment-and-variant-calling-tutorial
2. https://github.com/biod/sambamba
3. https://github.com/lh3/bwa
4. https://github.com/ekg/freebayes
5. https://github.com/samtools/samtools
6. https://docs.brew.sh/Homebrew-on-Linux
7. https://spark.apache.org
8. https://hadoop.apache.org
9. http://bdgenomics.org/
10. https://cloudlab.us/

