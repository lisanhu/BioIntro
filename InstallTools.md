# Install Bio Tools

## 1. Introduction

In this section, we will go over necessary information to install the tools mentioned in previous section. 

### 1.1 Installation tips
This is a general tip to install most applications.

The priority of installation:
1. Pre-defined packages in the system default repository, using `apt-get install` for example.
2. Pre-defined packages in the framework repositories (used in the project, pipeline or workflow), such as `conda install`, `pip3 install --user`, etc.
3. Pre-compiled binaries from the official website for your platform, such as the art simulator could be directly downloaded from the website.
4. Available source code with a well-defined README, INSTALL, or other documentation that could follow easily to compile and install the packages.
5. No good documentation, but with obvious project structure for us to guess the build system, such as maven, gradle, cmake, Makefile, etc.
    Some hints here:
    1. `mvn compile`
    2. `gradle build`
    3. `cmake ../ && make`
    4. `make`
6. Not easy to guess the build system but has some scripts that seem to work as building system.
    1. `./bootstrap.xxx`
    2. `./configure`
    3. `utilities/makefile-gen.pl`
    4. `utilities/run-all.pl`
7. Nothing but the source code. You have no choice but to ask the authors and try to figure out how to compile the project yourself at the same time.

Usually, we prefer to having pre-compiled package from official locations, and we will search for choices from 1-3. 

Sometimes we may need to customize, we may not have the required privilege, there's no such pre-compiled packages, or because of some other issues, we can't possibly use those. In such cases, we will need to read documents about how we can compile and install the tools to our system. The official documentation is preferred. We can also google about how to install certain tools.

If we can't find a good documentation, we may be able to tell how to build it from the source code structure. We need to be familiar with modern building systems and try out some default options. For folders with `bootstrap.xxx`, `configure`, we usually start with using `bootstrap.xxx`, then `configure`, and finally `make`, please be aware of the `--prefix` parameter is usually availble to `bootstrap.xxx` and `configure` to allow you to customize the installation folder. Please be careful and try to find clues in the provided documentation because some code even use `make INSTALLDIR=xxx` or other variable names to specify installation folders.

## 2. Tools
### 2.1 trim_galore
This tool is used to trim the reads to remove adapters.

Information page: `https://www.bioinformatics.babraham.ac.uk/projects/trim_galore/`

Requirements:
1. [Cutadapt](https://github.com/marcelm/cutadapt)
2. [FastQC](https://www.bioinformatics.babraham.ac.uk/projects/fastqc/)

### 2.2 Sequence alignment tools
### 2.2.1 BWA
https://github.com/lh3/bwa

requirements:
1. [zlib](https://zlib.net/)

### 2.2.2 AccSeq
requirements:
1. [zlib](https://zlib.net/) 
2. [cmake](https://cmake.org/)

### 2.3 GATK4, Picard Tools, SAMTools, BAMUtil
GATK4:
https://software.broadinstitute.org/gatk/download/

Picard Tools:
https://broadinstitute.github.io/picard/

SAMTools:
http://www.htslib.org/download/

BAMUtil:
https://github.com/statgen/bamUtil

I haven't installed GATK4 and Picard Tools yet, please tell me if you have problem installing them.

## Practice

Install these tools on Caviness, Biomix, and your own laptop as a practice. Note, you are required to install all these tools inside your home folder rather than the default system folder, even on your own laptop.


[Previous](BioTools.md)
[Next](JetStream.md)