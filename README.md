
<!-- README.md is generated from README.Rmd. Please edit that file -->
tailfindr <a href=''><img src='man/figures/tailfindr-logo.png' align="right" height="139" /></a>
================================================================================================

<!-- badges: start -->
<!-- badges: end -->
Overview
--------

tailfindr is a R package for estimating poly(A) tail lengths in Oxford Nanopore reads. It works for both RNA and DNA reads. In the case of DNA reads, it finds both poly(A)- and poly(T)-tail lengths. Currently, tailfindr can work only with 1D reads. It supports data that has been basecalled with Albacore or Guppy. Additionally, it also supports the newer multi-fast5 format.

tailfindr has been developed at [Valen Lab](http://valenlab.com/) in [Computational Biology Unit](https://www.cbu.uib.no/) at the [University of Bergen](https://www.uib.no/), Norway.

Installation
------------

#### Step 1. Installing HDF5 library

tailfindr depends on the HDF5 library for reading Fast5 files. For OS X and Linux, the HDF5 library needs to be installed via one of the (shell) commands specified below:

| System                                      | Command                            |
|:--------------------------------------------|:-----------------------------------|
| **OS X (using Homebrew)**                   | `brew install hdf5`                |
| **Debian-based systems (including Ubuntu)** | `sudo apt-get install libhdf5-dev` |
| **Systems supporting yum and RPMs**         | `sudo yum install hdf5-devel`      |

HDF5 1.8.14 has been pre-compiled for Windows and is available [here](https://github.com/mannau/h5-libwin) — thus no manual installation is required.

#### Step 2. Installing devtools

Currently, tailfindr is not listed on CRAN, so you need to install it using `devtools`. To install `devtools` use the following command:

``` r
install.packages("devtools")
```

#### Step 3. Installing tailfindr

Now you can install tailfindr using the command below:

``` r
devtools::install_github("adnaniazi/tailfindr")
```

Now you are ready to use tailfindr.

Usage
-----

`find_tails()` is the main function that you can use to find tail lengths in both RNA and DNA reads. It saves a CSV file containing all the tail-length data. Furthermore, it also returns the same data as a tibble.

Give below is a minimal use case:

``` r
library(tailfindr)
df <- find_tails(fast5_dir = '/path/to/fast5/folder/',
                 save_dir = '/path/to/a/folder/where/results/are/to/be/stored/',
                 csv_filename = 'tails.csv',
                 num_cores = 10)
```

Additionally, tailfindr allows you to save plots that show the tail location and length. You can save these plots as interactive .html files by using the `rbokeh` as `plotting_library`. You can zoom in on the tail region in the squiggle and see the exact location of the tail.

``` r
df <- find_tails(fast5_dir = '/path/to/fast5/folder/',
                 save_dir = '/path/to/a/folder/where/results/are/to/be/stored/',
                 csv_filename = 'tails.csv',
                 num_cores = 10,
                 save_plots = TRUE,
                 plotting_library = 'rbokeh)
```

However, note that using this option can slow down the performace because generating these interactive plots is a slow process. We recommend that you generat plots only for a small subset of your reads.

A relatively faster way of generating the plots is to use `ggplot2` as the `plotting_library`. In this case, plots we will saved as .png files. Becuase these plots are statics, i.e. not-interactive, therefore, it might be difficult to see the tail in a long squiggle in these plots.

``` r
df <- find_tails(fast5_dir = '/path/to/fast5/folder/',
                 save_dir = '/path/to/a/folder/where/results/are/to/be/stored/',
                 csv_filename = 'tails.csv',
                 num_cores = 10,
                 save_plots = TRUE,
                 plotting_library = 'rbokeh)
```

The devil(s) in the details
---------------------------

-   tailfindr currently works on data in the `/Analyses/Basecall_1D_000/BaseCalled_template/` path of the Fast5 file data hierarchy. It won't work on data present in, lets say, `/Analyses/Basecall_1D_001/BaseCalled_template/` path or `/Analyses/Basecall_1D_002/BaseCalled_template/` path; such paths are generated if you re-basecall already-basecalled data. To avoid this problem, use tailfindr on files that have been basecalled from the raw Fast5 files.
-   If you are using the flip flop model to basecall DNA data, please ensure that the nanopore adaptors are not trimmed off while basecalling. This can be done by turning off `enabling_trimming` option in the basecalling script. The script below shows you how we have basecalled our reads using the flip-flop model

``` bash
#!/bin/sh
INPUT=/raw/fast5/files/path/
OUTPUT=/output/folder/path/
guppy_basecaller \
    --config dna_r9.4.1_450bps_flipflop.cfg \
    --input $INPUT \
    --save_path $OUTPUT \
    --recursive \
    --fast5_out \
    --hp_correct 1 \
    --disable_pings 1 \
    --enable_trimming 0 
```

Getting help
------------

If you encounter a clear bug, please file a minimal reproducible example on [github](https://github.com/adnaniazi/tailfindr/issues). For questions and other discussion, email me at <adnan.niazi@uib.no>.
