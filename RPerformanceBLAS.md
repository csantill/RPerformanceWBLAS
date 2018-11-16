Improving R Perfomance by installing optimized BLAS/LAPACK libraries
====================================================================

July 25th, 2018

Carlos Santillan

Principal Software Engineer

Bentley Systems Inc.

<https://www.linkedin.com/in/carlos-santillan/>

<csantill@gmail.com>

<https://github.com/csantill/RPerformanceWBLAS>

------------------------------------------------------------------------

We frequently hear that R is slow, if your code makes heavy usage of
vector/matrix operations you will see significant performance
improvements.

The precompiled R distribution that is downloaded from CRAN makes use of
the reference BLAS/LAPACK implementation for linear algebra operations,
These implementations are built to be stable and cross platform
compatible but are not optimized for performance. Most R programmers use
these default libraries and are unaware that highly optimized libraries
are available and switching to these can have a significant perfomance
improvement. We will review the steps to install highly optimized
libraries and benchmark their performance.

------------------------------------------------------------------------

Base Libraries
==============

BLAS
----

The BLAS (Basic Linear Algebra Subprograms) are routines that provide
standard building blocks for performing basic vector and matrix
operations. The Level 1 BLAS perform scalar, vector and vector-vector
operations, the Level 2 BLAS perform matrix-vector operations, and the
Level 3 BLAS perform matrix-matrix operations. Because the BLAS are
efficient, portable, and widely available, they are commonly used in the
development of high quality linear algebra software

LAPACK
------

LAPACK is written in Fortran 90 and provides routines for solving
systems of simultaneous linear equations, least-squares solutions of
linear systems of equations, eigenvalue problems, and singular value
problems. The associated matrix factorizations (LU, Cholesky, QR, SVD,
Schur, generalized Schur) are also provided, as are related computations
such as reordering of the Schur factorizations and estimating condition
numbers. Dense and banded matrices are handled, but not general sparse
matrices. In all areas, similar functionality is provided for real and
complex matrices, in both single and double precision.

------------------------------------------------------------------------

You can use sessionInfo() in R to see which version of BLAS and LAPACK
your R session is using

    sessionInfo()

Here is the output when using the default BLAS/LAPACK libraries on
linux.

    ## BLAS: /usr/lib/x86_64-linux-gnu/blas/libblas.so.3.7.1
    ## LAPACK: /usr/lib/x86_64-linux-gnu/lapack/liblapack.so.3.7.1

    ## R version 3.4.4 (2018-03-15)
    ## Platform: x86_64-pc-linux-gnu (64-bit)
    ## Running under: Ubuntu 18.04 LTS
    ## 
    ## Matrix products: default
    ## BLAS: /usr/lib/x86_64-linux-gnu/blas/libblas.so.3.7.1
    ## LAPACK: /usr/lib/x86_64-linux-gnu/lapack/liblapack.so.3.7.1
    ## 
    ## locale:
    ##  [1] LC_CTYPE=en_US.UTF-8       LC_NUMERIC=C              
    ##  [3] LC_TIME=en_US.UTF-8        LC_COLLATE=en_US.UTF-8    
    ##  [5] LC_MONETARY=en_US.UTF-8    LC_MESSAGES=en_US.UTF-8   
    ##  [7] LC_PAPER=en_US.UTF-8       LC_NAME=C                 
    ##  [9] LC_ADDRESS=C               LC_TELEPHONE=C            
    ## [11] LC_MEASUREMENT=en_US.UTF-8 LC_IDENTIFICATION=C       
    ## 
    ## attached base packages:
    ## [1] stats     graphics  grDevices utils     datasets  methods   base     
    ## 
    ## loaded via a namespace (and not attached):
    ##  [1] compiler_3.4.4  backports_1.1.2 magrittr_1.5    rprojroot_1.3-2
    ##  [5] tools_3.4.4     htmltools_0.3.6 yaml_2.1.19     Rcpp_0.12.17   
    ##  [9] stringi_1.2.3   rmarkdown_1.10  knitr_1.20      stringr_1.3.1  
    ## [13] digest_0.6.15   evaluate_0.10.1

------------------------------------------------------------------------

Alternative libraries
=====================

There are several highly optimized libraries that can be used instead of
the default base libraries. These libraries are optimized to take
advantage of the hardware they are run on, and can be significatantly
faster than the base implementation (operations such as Matrix
multiplications may be over 40 times faster)

OpenBLAS
--------

OpenBLAS is an optimized BLAS library based on GotoBLAS2 1.13 BSD
version

AtlasBLAS
---------

The ATLAS (Automatically Tuned Linear Algebra Software) project is an
ongoing research effort focusing on applying empirical techniques in
order to provide portable performance.

GotoBLAS
========

GotoBLAS and GotoBLAS2 are open source implementations of the BLAS
(Basic Linear Algebra Subprograms) API with many hand-crafted
optimizations for specific processor types. GotoBLAS was developed by
Kazushige Goto at the Texas Advanced Computing Center (UT). As of 2003,
it was used in seven of the world's ten fastest supercomputers.

Intel MKL
---------

Intel Math Kernel Library

Features highly optimized, threaded, and vectorized math functions that
maximize performance on each processor family Uses industry-standard C
and Fortran APIs for compatibility with popular BLAS, LAPACK, and FFTW
functions—no code changes required

The Intel MKL used to a commercial product, free for student use, paid
license for other uses, it looks like it is now free

*Free for all, no royalties, no restrictions on company or project size,
access to current & older versions of libraries (only current version
for Intel® MPI), Forum Support.*

The above mentioned libraries are optimized for perfomance and are used
in production.

GotoBLAS at one time ran on 7 of the top 10 supercomputers in the world,
ATLASBLAS is used for Matlab (for some platforms), Matlab uses Veclib on
Mac.

------------------------------------------------------------------------

Installing the libraries
------------------------

The following steps have been tested on Ubuntu 18.04 "Bionic Beaver",
but they should work on debian distributions. We can use apt-cache
search to see what libblas debian packages are available to install

    apt-cache search libblas

    csantill@gawain:/usr/bin$ apt-cache search libblas
    libblas-dev - Basic Linear Algebra Subroutines 3, static library
    libblas3 - Basic Linear Algebra Reference implementations, shared library
    libatlas-base-dev - Automatically Tuned Linear Algebra Software, generic static
    libatlas3-base - Automatically Tuned Linear Algebra Software, generic shared
    libblas-test - Basic Linear Algebra Subroutines 3, testing programs
    libblasr - tools for aligning PacBio reads to target sequences
    libblasr-dev - tools for aligning PacBio reads to target sequences (development files)
    libopenblas-base - Optimized BLAS (linear algebra) library (shared library)
    libopenblas-dev - Optimized BLAS (linear algebra) library (development files)

Install OpenBLAS and AtlasBLAS wit the following command

    $ sudo apt-get install libopenblas-base libatlas3-base

Installing the Intel MKL
------------------------

The following script form ["Adding Intel MKL easily via a simple
script"](http://dirk.eddelbuettel.com/blog/2018/04/15/) provides a
simple way to install the Intel MKL

    sudo script.sh

    #!/bin/bash
    ##
    ## cf https://software.intel.com/en-us/articles/installing-intel-free-libs-and-python-apt-repo

    cd /tmp
    wget https://apt.repos.intel.com/intel-gpg-keys/GPG-PUB-KEY-INTEL-SW-PRODUCTS-2019.PUB
    apt-key add GPG-PUB-KEY-INTEL-SW-PRODUCTS-2019.PUB

    ## all products:
    #sudo wget https://apt.repos.intel.com/setup/intelproducts.list -O /etc/apt/sources.list.d/intelproducts.list
    ## just MKL
    sh -c 'echo deb https://apt.repos.intel.com/mkl all main > /etc/apt/sources.list.d/intel-mkl.list'
    ## other (TBB, DAAL, MPI, ...) listed on page

    apt-get update
    apt-get install intel-mkl-64bit-2018.2-046   ## wants 500+mb :-/  installs to 1.8 gb :-/

    ## update alternatives
    update-alternatives --install /usr/lib/x86_64-linux-gnu/libblas.so     libblas.so-x86_64-linux-gnu      /opt/intel/mkl/lib/intel64/libmkl_rt.so 50
    update-alternatives --install /usr/lib/x86_64-linux-gnu/libblas.so.3   libblas.so.3-x86_64-linux-gnu    /opt/intel/mkl/lib/intel64/libmkl_rt.so 50
    update-alternatives --install /usr/lib/x86_64-linux-gnu/liblapack.so   liblapack.so-x86_64-linux-gnu    /opt/intel/mkl/lib/intel64/libmkl_rt.so 50
    update-alternatives --install /usr/lib/x86_64-linux-gnu/liblapack.so.3 liblapack.so.3-x86_64-linux-gnu  /opt/intel/mkl/lib/intel64/libmkl_rt.so 50

    echo "/opt/intel/lib/intel64"     >  /etc/ld.so.conf.d/mkl.conf
    echo "/opt/intel/mkl/lib/intel64" >> /etc/ld.so.conf.d/mkl.conf
    ldconfig

    echo "MKL_THREADING_LAYER=GNU" >> /etc/environment

Microsoft R Open
================

R Open (formerly Revolution R Open) is an enhanced distribution of R.

The current version, Microsoft R Open 3.5.0, is based on (and 100%
compatible with) R-3.5.0, has full compatibility with all packages,
scripts and applications that work with R. It includes additional
capabilities for improved performance, reproducibility, and support for
Windows, Linux-based and OS X platforms.

(Windows and Linux version use Intel MKL libraries, OS X uses Accelerate
ML)

<https://mran.microsoft.com/open>

------------------------------------------------------------------------

Selecting the default BLAS/LAPACK
=================================

Once you have installed the different BLAS/LAPACK libraries you can use
update alternatives to select the default library

Note: You will have to to restart R/R Studio for the change to take
effect, you can use sessionInfo() to confirm that you are using the
library you want

Change default BLAS
-------------------

Run update-alternatives to select one of the installed Alternatives

    sudo update-alternatives --config libblas.so.3-x86_64-linux-gnu

    csantill@gawain:~$ sudo update-alternatives --config libblas.so.3-x86_64-linux-gnu
    There are 4 choices for the alternative libblas.so.3-x86_64-linux-gnu (providing /usr/lib/x86_64-linux-gnu/libblas.so.3).

      Selection    Path                                             Priority   Status
    ------------------------------------------------------------
      0            /opt/intel/mkl/lib/intel64/libmkl_rt.so           50        auto mode
    * 1            /opt/intel/mkl/lib/intel64/libmkl_rt.so           50        manual mode
      2            /usr/lib/x86_64-linux-gnu/atlas/libblas.so.3      35        manual mode
      3            /usr/lib/x86_64-linux-gnu/blas/libblas.so.3       10        manual mode
      4            /usr/lib/x86_64-linux-gnu/openblas/libblas.so.3   40        manual mode

    Press <enter> to keep the current choice[*], or type selection number: 

Change default LAPACK
---------------------

    sudo update-alternatives --config liblapack.so.3-x86_64-linux-gnu

    csantill@gawain:~$ sudo update-alternatives --config liblapack.so.3-x86_64-linux-gnu
    There are 4 choices for the alternative liblapack.so.3-x86_64-linux-gnu (providing /usr/lib/x86_64-linux-gnu/liblapack.so.3).

      Selection    Path                                               Priority   Status
    ------------------------------------------------------------
      0            /opt/intel/mkl/lib/intel64/libmkl_rt.so             50        auto mode
    * 1            /opt/intel/mkl/lib/intel64/libmkl_rt.so             50        manual mode
      2            /usr/lib/x86_64-linux-gnu/atlas/liblapack.so.3      35        manual mode
      3            /usr/lib/x86_64-linux-gnu/lapack/liblapack.so.3     10        manual mode
      4            /usr/lib/x86_64-linux-gnu/openblas/liblapack.so.3   40        manual mode

    Press <enter> to keep the current choice[*], or type selection number: 

------------------------------------------------------------------------

BenchMarks
==========

I ran the R Benchmark 2.5 scripts under each of the configurations. The
accompanying article [Why is R slow? some explanations and MKL/OpenBLAS
setup to try to fix
this](https://www.r-bloggers.com/why-is-r-slow-some-explanations-and-mklopenblas-setup-to-try-to-fix-this/)
was one of the references I used.

The script I used can be found

<https://github.com/pachamaltese/r-with-intel-mkl/blob/master/00-benchmark-scripts/1-r-benchmark-25.R>

And can be run from

    source("1-r-benchmark-25.R")

As can be seen from the Benchmark results OpenBLAS is about 2.85 times
faster than the default BLAS and MKL is about 3.25 times faster, also
AtlasBLAS is about twice as fast as the default. With some operations
such as Matrix Cross Product being over 20 times faster.

R Benchmark 2.5

Number of times each test is run: 3

<table style="width:100%;">
<colgroup>
<col width="47%" />
<col width="17%" />
<col width="19%" />
<col width="8%" />
<col width="7%" />
</colgroup>
<thead>
<tr class="header">
<th></th>
<th>BLAS</th>
<th>OpenBLAS</th>
<th>MKL</th>
<th>ATLASBLAS</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td>A) Creation, transp., deformation of 2.5kx2.5k matrix</td>
<td>1.424</td>
<td>1.411</td>
<td>1.611</td>
<td><strong>1.371</strong></td>
</tr>
<tr class="even">
<td>B) 2.4Kx2.4K normal distributed random matrix ^1k</td>
<td>1.028</td>
<td>1.030</td>
<td>1.030</td>
<td><strong>1.024</strong></td>
</tr>
<tr class="odd">
<td>C) Sorting of 7M random values</td>
<td>1.423</td>
<td>1.433</td>
<td><strong>1.346</strong></td>
<td>1.441</td>
</tr>
<tr class="even">
<td>D) 2.8Kx2.8K cross-product matrix (b = a' * a)</td>
<td>20.687</td>
<td>0.789</td>
<td><strong>0.577</strong></td>
<td>3.756</td>
</tr>
<tr class="odd">
<td>E) Linear regr. over a 3Kx3K matrix (c = a  b')</td>
<td>9.898</td>
<td>0.561</td>
<td><strong>0.322</strong></td>
<td>1.939</td>
</tr>
<tr class="even">
<td></td>
<td></td>
<td></td>
<td></td>
<td></td>
</tr>
<tr class="odd">
<td>Trimmed geom. mean (2 extremes eliminated)</td>
<td>2.717</td>
<td>1.047</td>
<td><strong>0.928</strong></td>
<td>1.565</td>
</tr>
</tbody>
</table>

![](RPerformanceBLAS_files/figure-markdown_strict/unnamed-chunk-2-1.png)

<table>
<thead>
<tr class="header">
<th></th>
<th>BLAS</th>
<th>OpenBLAS</th>
<th>MKL</th>
<th>ATLASBLAS</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td>F) FFT over 2,400,000 random values</td>
<td>0.575</td>
<td>0.535</td>
<td>0.570</td>
<td><strong>0.534</strong></td>
</tr>
<tr class="even">
<td>G) Eigenvalues of a 640x640 random matrix</td>
<td>1.470</td>
<td>1.028</td>
<td><strong>0.480</strong></td>
<td>0.921</td>
</tr>
<tr class="odd">
<td>H) Determinant of a 2500x2500 random matrix</td>
<td>6.727</td>
<td>0.456</td>
<td><strong>0.322</strong></td>
<td>1.873</td>
</tr>
<tr class="even">
<td>I) Cholesky decomposition of a 3000x3000 matrix</td>
<td>8.314</td>
<td>0.444</td>
<td><strong>0.321</strong></td>
<td>1.617</td>
</tr>
<tr class="odd">
<td>J) Inverse of a 1600x1600 random matrix</td>
<td>6.069</td>
<td>0.493</td>
<td><strong>0.296</strong></td>
<td>1.603</td>
</tr>
<tr class="even">
<td></td>
<td></td>
<td></td>
<td></td>
<td></td>
</tr>
<tr class="odd">
<td>Trimmed geom. mean (2 extremes eliminated)</td>
<td>3.915</td>
<td>0.494</td>
<td><strong>0.368</strong></td>
<td>1.336</td>
</tr>
</tbody>
</table>

![](RPerformanceBLAS_files/figure-markdown_strict/unnamed-chunk-3-1.png)

<table style="width:100%;">
<colgroup>
<col width="78%" />
<col width="5%" />
<col width="5%" />
<col width="5%" />
<col width="5%" />
</colgroup>
<thead>
<tr class="header">
<th></th>
<th>BLAS</th>
<th>OpenBLAS</th>
<th>MKL</th>
<th>ATLASBLAS</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td>K) 3,500,000 Fibonacci numbers calculation (vector calc)</td>
<td>1.069</td>
<td>1.035</td>
<td>1.006</td>
<td><strong>1.029</strong></td>
</tr>
<tr class="even">
<td>L) Creation of a 3000x3000 Hilbert matrix (matrix calc)</td>
<td>0.530</td>
<td>0.497</td>
<td>0.510</td>
<td><strong>0.493</strong></td>
</tr>
<tr class="odd">
<td>N) Grand common divisors of 400,000 pairs (recursion)</td>
<td>0.502</td>
<td>0.500</td>
<td>0.579</td>
<td><strong>0.478</strong></td>
</tr>
<tr class="even">
<td>M) Creation of a 500x500 Toeplitz matrix (loops)</td>
<td>0.580</td>
<td>0.540</td>
<td><strong>0.086</strong></td>
<td>0.542</td>
</tr>
<tr class="odd">
<td>O) Escoufier's method on a 45x45 matrix (mixed)</td>
<td>0.636</td>
<td><strong>0.469</strong></td>
<td>0.507</td>
<td>0.538</td>
</tr>
<tr class="even">
<td></td>
<td></td>
<td></td>
<td></td>
<td></td>
</tr>
<tr class="odd">
<td>Trimmed geom. mean (2 extremes eliminated)</td>
<td>0.580</td>
<td><strong>0.512</strong></td>
<td>0.531</td>
<td>0.524</td>
</tr>
</tbody>
</table>

![](RPerformanceBLAS_files/figure-markdown_strict/unnamed-chunk-4-1.png)

<table>
<thead>
<tr class="header">
<th></th>
<th>BLAS</th>
<th>OpenBLAS</th>
<th>MKL</th>
<th>ATLASBLAS</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td>Total time for all 15 tests</td>
<td>60.932</td>
<td>11.222</td>
<td><strong>9.563</strong></td>
<td>19.158</td>
</tr>
<tr class="even">
<td>Overall mean (sum of I, II and III trimmed means/3)</td>
<td>1.834</td>
<td>0.642</td>
<td><strong>0.566</strong></td>
<td>1.031</td>
</tr>
<tr class="odd">
<td></td>
<td></td>
<td></td>
<td></td>
<td></td>
</tr>
</tbody>
</table>

![](RPerformanceBLAS_files/figure-markdown_strict/unnamed-chunk-5-1.png)

Results
=======

The Intel MKL (Microsoft R open)) are the clear winners for the used
configuration. They are faster in most operations than OpenBLAS or
ATLAS, It should be noted that while the MKL performs faster when
running on Intel CPUs, OpenBLAS is typically faster on AMD CPUs.

Mac
===

Apple provides the Accelarate Framework which includes optimized BLAS
libraries (veclib). The R installation downloaded from CRAN ships with
both the vecLib and reference BLAS libraries (The reference BLAS is used
by default)

The Mac OSX R FAQ [10.5 Which BLAS is used and how can it be
changed?](https://cran.r-project.org/bin/macosx/RMacOSX-FAQ.html#Which-BLAS-is-used-and-how-can-it-be-changed_003f)
describes the steps to change the default BLAS to use the vecLib.

The following article [R: Use faster
vecLib](https://gist.github.com/nicebread/6920c8287d7bffb03007)


    ## 
    # use faster vecLib library
    cd /Library/Frameworks/R.framework/Resources/lib
    ln -sf  /System/Library/Frameworks/Accelerate.framework/Frameworks/vecLib.framework/Versions/Current/libBLAS.dylib libRblas.dylib

    # return to default settings
    cd /Library/Frameworks/R.framework/Resources/lib
    ln -sf libRblas.0.dylib libRblas.dylib

sessionInfo() should return something similar to below if you are using
the Accelerate Framework

    Matrix products: default
    BLAS: /System/Library/Frameworks/Accelerate.framework/Versions/A/Frameworks/vecLib.framework/Versions/A/libBLAS.dylib
    LAPACK: /System/Library/Frameworks/Accelerate.framework/Versions/A/Frameworks/vecLib.framework/Versions/A/libLAPACK.dylib

Benchmarks Mac OSX
------------------

Benchmark 2.5

Number of times each test is run: 3

Matrix calculation

<table style="width:100%;">
<colgroup>
<col width="90%" />
<col width="4%" />
<col width="4%" />
</colgroup>
<thead>
<tr class="header">
<th></th>
<th>BLAS</th>
<th>vecLib</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td>Creation, transp., deformation of a 2500x2500 matrix</td>
<td>0.911666666666668</td>
<td><strong>0.907666666666665</strong></td>
</tr>
<tr class="even">
<td>2400x2400 normal distributed random matrix ^1000</td>
<td>0.173000000000002</td>
<td><strong>0.172666666666668</strong></td>
</tr>
<tr class="odd">
<td>Sorting of 7,000,000 random values</td>
<td>0.717000000000004</td>
<td><strong>0.715333333333334</strong></td>
</tr>
<tr class="even">
<td>2800x2800 cross-product matrix (b = a' * a)</td>
<td>13.115</td>
<td><strong>0.472000000000001</strong></td>
</tr>
<tr class="odd">
<td>Linear regr. over a 3000x3000 matrix (c = a  b')</td>
<td>6.31833333333333</td>
<td><strong>0.429</strong></td>
</tr>
<tr class="even">
<td>Trimmed geom. mean (2 extremes eliminated)</td>
<td>1.60442438940899</td>
<td><strong>0.525173235045746</strong></td>
</tr>
</tbody>
</table>

1.  Matrix functions

<table style="width:100%;">
<colgroup>
<col width="90%" />
<col width="4%" />
<col width="4%" />
</colgroup>
<thead>
<tr class="header">
<th></th>
<th>BLAS</th>
<th>vecLib</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td>FFT over 2,400,000 random values</td>
<td><strong>0.424000000000007</strong></td>
<td>0.469000000000001</td>
</tr>
<tr class="even">
<td>Eigenvalues of a 640x640 random matrix</td>
<td>0.904666666666671</td>
<td><strong>0.479333333333332</strong></td>
</tr>
<tr class="odd">
<td>Determinant of a 2500x2500 random matrix</td>
<td>4.507</td>
<td><strong>0.379333333333335</strong></td>
</tr>
<tr class="even">
<td>Cholesky decomposition of a 3000x3000 matrix</td>
<td>5.22133333333333</td>
<td><strong>0.406666666666666</strong></td>
</tr>
<tr class="odd">
<td>Inverse of a 1600x1600 random matrix</td>
<td>4.00333333333333</td>
<td><strong>1.433</strong></td>
</tr>
<tr class="even">
<td>Trimmed geom. mean (2 extremes eliminated)</td>
<td>2.53668164510219</td>
<td><strong>0.45048778200099</strong></td>
</tr>
</tbody>
</table>

1.  Programmation

<table style="width:100%;">
<colgroup>
<col width="90%" />
<col width="4%" />
<col width="4%" />
</colgroup>
<thead>
<tr class="header">
<th></th>
<th>BLAS</th>
<th>vecLib</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td>3,500,000 Fibonacci numbers calculation (vector calc)</td>
<td><strong>0.320000000000003</strong></td>
<td>0.322666666666665</td>
</tr>
<tr class="even">
<td>Creation of a 3000x3000 Hilbert matrix (matrix calc)</td>
<td><strong>0.336999999999989</strong></td>
<td>0.349000000000004</td>
</tr>
<tr class="odd">
<td>Grand common divisors of 400,000 pairs (recursion)</td>
<td>0.284666666666662</td>
<td><strong>0.280000000000001</strong></td>
</tr>
<tr class="even">
<td>Creation of a 500x500 Toeplitz matrix (loops)</td>
<td>0.36099999999999</td>
<td><strong>0.359666666666665</strong></td>
</tr>
<tr class="odd">
<td>Escoufier's method on a 45x45 matrix (mixed)</td>
<td>0.401999999999987</td>
<td><strong>0.278000000000006</strong></td>
</tr>
<tr class="even">
<td>Trimmed geom. mean (2 extremes eliminated)</td>
<td>0.33891882626425</td>
<td><strong>0.315921503050365</strong></td>
</tr>
</tbody>
</table>

<table style="width:100%;">
<colgroup>
<col width="90%" />
<col width="4%" />
<col width="4%" />
</colgroup>
<thead>
<tr class="header">
<th></th>
<th>BLAS</th>
<th>vecLib</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td>Total time for all 15 tests</td>
<td>38</td>
<td><strong>7.45333333333334</strong></td>
</tr>
<tr class="even">
<td>Overall mean (sum of I, II and III trimmed means/3)</td>
<td>1.11316695385059</td>
<td><strong>0.421232232758123</strong></td>
</tr>
</tbody>
</table>

------------------------------------------------------------------------

Windows
=======

While it is possible to use alternative BLAS and LAPACK libraries on
Windows, I could not found a source of updated precompiled binaries I
found it easier just to use Microsoft R Open that uses the Intel MKL

------------------------------------------------------------------------

------------------------------------------------------------------------

Paralell Development
--------------------

OpenBLAS and the Intel MKL (and R Open) take advantage of multiple cores
and multithreading, For some operations they will use multiple threads.
This is great if your operation is serial in nature but it could
interfer if you are trying to perform parallel operations (such as
parallel foreach, parapply, etc.)

You should disable multi threading to avoid this. Fortunatley most of
the performance from using these libraries comes from vectorized math
not multi threading (see the following article for more info [Edge cases
in using the Intel MKL and parallel
programming](http://blog.revolutionanalytics.com/2015/10/edge-cases-in-using-the-intel-mkl-and-parallel-programming.html),
while this is about MKL the same applies to openBLAS )

with openBLAS you can disable multihreading by using

    openblas_set_num_threads(1) 

on R open

    setMKLthreads(1)

if you are using MKL then you should set environment variable
MKL\_NUM\_THREADS ( you can set this in your .profile )

    export MKL_NUM_THREADS=1

------------------------------------------------------------------------

<!--
***

## Amdahl's law
Amdahl's law is a formula which gives the theoretical speedup in latency of the execution of a task at fixed workload that can be expected of a system whose resources are improved. 

$$S_{latency} = \frac{1}{(1-p)+(\frac{p}{s})}$$
Where
s is the speedup
p is the portion that benefits from the speedup.



 $$ Percentage Improvement = 100 ( 1- (\frac{1}{S_{latency}})) $$
For example:

If we improve the performance of a function by 200 % and this function takes 40% of our calculation , then our overall performance improvement will be of only 20%

p=0.40

s=2

$$S_{latency} = \frac{1}{(1-.40)+(\frac{.40}{2})} = 1.25$$
The percentageImprovent will be 

 $$ Percentage Improvement = 100 ( 1- (\frac{1}{1.25})) = 20 \%$$


# Final words 

”*We should forget about small efficiencies, say about 97% of the time: premature optimization is the root of all evil.*” - **Donald Knuth**

"*Make it work, Make it right, Make it Fast*" **Kent Beck**

-->
Resources
=========

-   BLAS <http://www.netlib.org/blas/>

-   LAPACK <http://www.netlib.org/lapack/>

-   OpenBLAS <https://www.openblas.net/>

-   ATLAS <http://math-atlas.sourceforge.net/>

-   Intel MKL
    <https://software.intel.com/en-us/articles/free-ipsxe-tools-and-libraries>

-   Microsoft Open R (Formerly Revolution R Open)
    <https://mran.microsoft.com/open>

-   GotoBLAS
    <https://www.tacc.utexas.edu/research-development/tacc-software/gotoblas2>

-   Apple Accelerate
    <https://developer.apple.com/documentation/accelerate>

-   [Amadahl's
    Law](https://en.wikipedia.org/wiki/Amdahl%27s_law#Serial_programs)

-   [gcbd: 'GPU'/CPU Benchmarking in Debian-Based
    Systems](https://cran.r-project.org/web/packages/gcbd/index.html)

-   [Faster BLAS in
    R](http://brettklamer.com/diversions/statistical/faster-blas-in-r/)
