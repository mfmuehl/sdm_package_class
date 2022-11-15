## Installing the sdm package

Please start by downloading the sdm package:

install.packages(‘sdm’)

Several other packages will also need to be installed (for model fitting
and evaluation). There is a function in the sdm package that will
install all of them at once for you:

    library(sdm)

    ## Loading required package: sp

    ## sdm 1.1-8 (2021-11-11)

    installAll()

    ## 
    ##  All required packages have been already installed!

    ## Loading required package: dismo

    ## Loading required package: raster

    ## Loading required package: gbm

    ## Loaded gbm 2.1.8.1

    ## Loading required package: tree

    ## Loading required package: mda

    ## Loading required package: class

    ## Loaded mda 0.5-3

    ## Loading required package: mgcv

    ## Loading required package: nlme

    ## 
    ## Attaching package: 'nlme'

    ## The following object is masked from 'package:raster':
    ## 
    ##     getData

    ## This is mgcv 1.8-41. For overview type 'help("mgcv-package")'.

    ## Loading required package: glmnet

    ## Loading required package: Matrix

    ## Loaded glmnet 4.1-4

    ## Loading required package: earth

    ## Loading required package: Formula

    ## Loading required package: plotmo

    ## Loading required package: plotrix

    ## Loading required package: TeachingDemos

    ## Loading required package: rJava

    ## Loading required package: RSNNS

    ## Loading required package: Rcpp

    ## Loading required package: ranger

    ## Loading required package: randomForest

    ## randomForest 4.7-1.1

    ## Type rfNews() to see new features/changes/bug fixes.

    ## 
    ## Attaching package: 'randomForest'

    ## The following object is masked from 'package:ranger':
    ## 
    ##     importance

    ## Loading required package: rpart

    ## Loading required package: kernlab

    ## 
    ## Attaching package: 'kernlab'

    ## The following objects are masked from 'package:raster':
    ## 
    ##     buffer, rotated

If you are running into issues installing the package, it may be because
some of the packages require R to be updated to at least version 4.0.2.
If you get this error, update R and restart R Studio.

Please make sure that installAll() successfully installs all of the
packages without throwing any errors. If all of the associated packages
aren’t downloaded, you will only be able to get through part of the
exercise.
