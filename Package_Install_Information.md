## Installing the sdm package

Please start by downloading the sdm package:

    install.packages('sdm')

Several other packages will also need to be installed (for model fitting
and evaluation). There is a function in the sdm package that will
install all of them at once for you:

    library(sdm)

    installAll()

    ## 
    ##  All required packages have been already installed!

If you are running into issues installing the package, it may be because
some of the packages require R to be updated to at least version 4.0.2.
If you get this error, update R and restart R Studio.

Please make sure that installAll() successfully installs all of the
packages without throwing any errors. If all of the associated packages
aren’t downloaded, you will only be able to get through part of the
exercise.

  
