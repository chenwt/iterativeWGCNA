# iterativeWGCNA: a WGCNA extension

## Synopsis

iterativeWGCNA provides an extension and Python wrapper for the R program [Weighted Gene Co-expression Network Analysis](https://github.com/cran/WGCNA), or WGCNA, that improves the robustness of network-based classifications (modules) inferred from whole-transcriptome gene expression datasets.

## Contents


### Setup

* [Dependencies](#dependencies)
* [Installation](#installation)
  
### Usage
  * [Run iterativeWGCNA](#running-iterativewgcna)
  * [Summarize results](#summarize-results)
  
### [Troubleshooting](#troubleshooting-1)


## Setup and Installation

### Dependencies

iterativeWGCNA has the following dependencies:

#### R language for statistical computing

[R](https://cran.r-project.org/) version 3.* must be available on the system and the binary executable in the system PATH.

> NOTE: the most recent version of R that supports WGCNA is 3.2.0.

iterativeWGCNA requires that the following R packages be installed:

1. [WGCNA](https://labs.genetics.ucla.edu/horvath/CoexpressionNetwork/Rpackages/WGCNA/#cranInstall): Weighted Gene Co-expression Network Analysis package and Bioconductor dependencies
1. [pheatmap](https://cran.r-project.org/web/packages/pheatmap/index.html): Pretty Heatmaps

#### Python

iterativeWGCNA requires Python version 2.7 or higher.  It is designed to be future compatible with Python 3+.  iterativeWGCNA requires the following Python packages:

1. [rpy2](https://pypi.python.org/pypi/rpy2): a Python interface for R (v. 2.7.9+)
1. [matplotlib](http://matplotlib.org/): a python 2D plotting library

If missing, these will be installed by the iterativeWGCNA installer.  See below.

### Installation

iterativeWGCNA can be run without installing the package as long as the requisite Python dependencies (rpy2 and matplotlib) are already present on the system.  Installing the package will install any missing *Python* dependencies.

To install the iterativeWGCNA package run the following command:

```
git clone https://github.com/cstoeckert/iterativeWGCNA.git
cd iterativeWGCNA
python setup.py install
```

> NOTE: depending on your system this may require administrative (e.g., sudo) permissions.


As a work around, specify the `--user` switch to install iterativeWGCNA and its dependencies to a local (user) library (e.g., `.local/bin` on a Linux system) as follows:

```
git clone https://github.com/cstoeckert/iterativeWGCNA.git
cd iterativeWGCNA
python setup.py install --user
```

## Usage

### Running iterativeWGCNA

1. [Quick Start](#quick-start)
1. [Command Line Options](#command-line-options)
1. [WGCNA Parameters](#wgcna-parameters)
1. [Input File Format](#input-file-format)
1. [Output Files](#output-files)

#### Quick Start

iterativeWGCNA can be run without installing the iterativeWGCNA package by excuting the wrapper script `run_iterative_wgcna.py` in the iterativeWGCNA directory. At a minimum, the `-i` option (`--inputFile`) denoting the full path to the input file must be specified.

```
python run_iterative_wgcna.py -i <input_file_path> 
```

if the iterativeWGCNA package was installed, iterativeWGCNA can also be run at the package level using the `-m` switch:

```
python -m iterativeWGCNA -h
```

#### Command Line Options

Execute `run_iterative_wgcna.py` with the `-h` (`--help`) switch to see all command line options and additional usage information, including details on file formats.

```
python run_iterative_wgcna.py -h
```

```diff
-h, --help
   show help message and exit
    
-i <gene expression file>, --inputFile <gene expression file>
   full path to input gene expression file; if full path is not provided,
   assumes the file is in the working (output) directory
+ required
   
-o <output dir>, --workingDir <output dir>
   R working directory; where output will be saved
   default: current directory
   
-v, --verbose
   print status messages

-p <param list>, --wgcnaParameters <param list>
   comma separated list of parameters to be passed to WGCNA's blockwiseModules function
   e.g., power=6,randomSeed=1234875
   see 'blockwiseModules' section of the WGCNA manual for more information
   
--enableWGCNAThreads
    enable WGCNA to use threads
    
--saveBlocks
    save WGNCA blocks during each iteration
    
```

#### WGCNA Parameters

iterativeWGCNA can accept any parameter valid for the WGCNA blockwiseModules function.  See http://www.inside-r.org/packages/cran/wgcna/docs/blockwiseModules for full details

> To specify these parameters use the `--wgcnaParameters` flag followed by a comma separated list of parameter=value pairs.

For example:

`--wgcnaParameters maxBlockSize=5000,corType=bicor,power=10`

sets the maximum block size to 5000 genes, the correlation type to the biweight correlation, and the power-law scaling factor (beta) to 10

> If WGCNA parameters are not specified, iterativeWGCNA uses the default WGCNA settings for the blockwiseModules function, except for the following:

```python
minModuleSize = 20 # minimum number of genes in a detected module
saveTOMs = TRUE # save the topological overlap matrices for each block in the block data structure
minKMEtoStay = 0.8 # minimum eigengene connectivity (kME) required for a gene to be retained in its assigned module
minCoreKME = 0.8 # if the module does not have minModuleSize genes with eigengene connectivity at least minCoreKME, the module is disbanded
reassignThreshold = 0.05 # per-set p-value ratio threshold for reassigning genes between modules
networkType = 'signed' # character string specifying network type. Allowed values are "unsigned" and "signed"
numericLabels = TRUE # label modules by numbers (e.g., 0,1,2) instead of colors
```

#### Input File Format

iterativeWGCNA expects a `tab-delimited` text file containing gene expression data arranged such that there is one row per gene and one column per sample.  The first column should contain `unique` gene identifiers.  For example:

| Gene | Sample1 | Sample2 | Sample3 |
| --- | --- | --- | --- |
| Gata1 | 500 | 715 | 1000 |
| Phtf2 | 60 | 1000 | 1600 |


> iterativeWGCNA will accept `gzipped` input files.


#### Output Files

### Summarize Results




## Troubleshooting

Access to the `readline` library in the context of the `rpy2` library can be problematic and has been [reported elsewhere](https://github.com/ContinuumIO/anaconda-issues/issues/152). In trying to run iterativeWGCNA, an error like the following would be observed:
```
Traceback (most recent call last):
  File "/home/_USER_/iterativeWGCNA-master/run_iterative_wgcna.py", line 7, in <module>
    from iterativeWGCNA.iterativeWGCNA import IterativeWGCNA
  File "/home/_USER_/iterativeWGCNA-master/iterativeWGCNA/iterativeWGCNA.py", line 17, in <module>
    import rpy2.robjects as ro
  File "/home/_USER_/bin/anaconda2/lib/python2.7/site-packages/rpy2/robjects/__init__.py", line 15, in <module>
    import rpy2.rinterface as rinterface
  File "/home/_USER_/bin/anaconda2/lib/python2.7/site-packages/rpy2/rinterface/__init__.py", line 100, in <module>
    from rpy2.rinterface._rinterface import *
ImportError: /home/_USER_/bin/anaconda2/lib/python2.7/site-packages/rpy2/rinterface/../../../../libreadline.so.6: undefined symbol: PC
```

The workaround is to uncomment the following line in `run_iterative_wgcna.py`:
```
# import readline
```
