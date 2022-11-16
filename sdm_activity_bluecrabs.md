Hopefully you installed the sdm package successfully! Load the
package(s):

    library(sdm)
    library(raster)
    library(rgdal)
    library(sf)

Read in the file

    bc.PA.shp <-  read_sf("data_folder/Points/Crab_sim.shp")

Convert the blue crab data to sf object

    bc.PA.shp <- st_as_sf(bc.PA.shp)

Pull out the Blue crab data only:

    bc.PA.shp$BC_Occurre[bc.PA.shp$BC_Occurre >0] <- 1

    bc.PA.shp <- subset(bc.PA.shp, select = c(BC_Occurre, geometry))

    bc.PA.shp

    ## Simple feature collection with 231 features and 1 field
    ## Geometry type: POINT
    ## Dimension:     XYZ
    ## Bounding box:  xmin: -73.3215 ymin: 40.6296 xmax: -72.8842 ymax: 40.75559
    ## z_range:       zmin: 0 zmax: 1
    ## Geodetic CRS:  WGS 84
    ## # A tibble: 231 × 2
    ##    BC_Occurre                 geometry
    ##         <dbl>              <POINT [°]>
    ##  1          0  Z (-73.2244 40.66525 0)
    ##  2          0 Z (-73.20134 40.69725 0)
    ##  3          0 Z (-73.19212 40.66784 0)
    ##  4          0  Z (-73.15126 40.6636 0)
    ##  5          0 Z (-73.12622 40.68128 0)
    ##  6          0 Z (-73.10766 40.67509 0)
    ##  7          1   Z (-73.085 40.71424 1)
    ##  8          0 Z (-73.06582 40.69299 0)
    ##  9          1 Z (-73.03204 40.69015 1)
    ## 10          1    Z (-73.025 40.7424 1)
    ## # … with 221 more rows

Convert bc.PA.shp to a SpatialPointsDataFrame called Blue\_crabs. Check
by plotting the Blue\_crabs data. Then plot the presence and
pseudo-absence data as two different colors

    Blue_crabs <- as(bc.PA.shp, "Spatial")

    plot(Blue_crabs)

![](sdm_activity_bluecrabs_files/figure-markdown_strict/unnamed-chunk-5-1.png)

    plot(Blue_crabs[Blue_crabs$BC_Occurre == 1,], col='blue', pch=16)
    points(Blue_crabs[Blue_crabs$BC_Occurre == 0,], col = 'red', pch=16)

![](sdm_activity_bluecrabs_files/figure-markdown_strict/unnamed-chunk-5-2.png)

Now we can load in all of the data for our predictor variables. This
includes: temperature, pH, salinity, and dissolved oxygen. The raster()
function from the sf package allows you to read these .tif files in as
rasters.

Once you load these in, it is always helpful to check and make sure that
they’re correct by plotting them one at a time.

    temperature <- raster("data_folder/Envs variables/temp_mean.tif")
    DO <- raster("data_folder/Envs variables/DO_mean.tif")
    pH <- raster("data_folder/Envs variables/ph_mean.tif")
    salinity <- raster("data_folder/Envs variables/salinity_mean.tif")

    plot(temperature)

![](sdm_activity_bluecrabs_files/figure-markdown_strict/unnamed-chunk-6-1.png)

    plot(DO)

![](sdm_activity_bluecrabs_files/figure-markdown_strict/unnamed-chunk-6-2.png)

    plot(pH)

![](sdm_activity_bluecrabs_files/figure-markdown_strict/unnamed-chunk-6-3.png)

    plot(salinity)

![](sdm_activity_bluecrabs_files/figure-markdown_strict/unnamed-chunk-6-4.png)

Great! Now we want to stack them into a multi-layered RasterStack. We
can do this using the stack() function with the list() function nested
in. This stacks the list of raster objects.

Then you should check to make sure this worked by plotting your new
RasterStack. You can also pull out one raster and take a closer look.

    preds <- stack(list(temperature,DO,pH,salinity))

    plot(preds)

![](sdm_activity_bluecrabs_files/figure-markdown_strict/unnamed-chunk-7-1.png)

    plot(preds[[4]])

![](sdm_activity_bluecrabs_files/figure-markdown_strict/unnamed-chunk-7-2.png)

Create object with specified train dataset and specify predictors you
care about. This is considered a data “pre-processing” step. Let’s
follow what the sdm package vignette tells us. When you put a . or don’t
specify a formula, the sdmData function will decide what to use as
presence/absence data and your predictor variables.

    data_2 <- sdmData(formula = BC_Occurre~., train = Blue_crabs, predictors = preds)

    ## Loading required package: dismo

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

Seems like it worked… let’s take a closer look.

    data_2

    ## class                                 : sdmdata 
    ## =========================================================== 
    ## number of species                     :  1 
    ## species names                         :  BC_Occurre 
    ## number of features                    :  5 
    ## feature names                         :  coords.x3, temp_mean, DO_mean, ... 
    ## type                                  :  Presence-Absence 
    ## has independet test data?             :  FALSE 
    ## number of records                     :  230 
    ## has Coordinates?                      :  TRUE

Checkpoint: How many predictors did the function detect? Does this seem
right? What about the feature names?

Now let’s make sure we help the function out by specifying our formula.
We can also choose to use only a few predictor variables if we want.

    data <- sdmData(formula = BC_Occurre~temp_mean+DO_mean+ph_mean+salinity_mean, train = Blue_crabs, predictors = preds)

# Fitting our models

This package supports many different methods or classification
algorithms. This is why you need to make sure that all of the associated
packages downloaded properly via the InstallAll() function. It allows
you to fit multiple models at once and store the results as one object.
Let’s try to fit Occurence ~ temp + DO + pH + salinity using random
forest, support vector machine, and gams

    model_1 <- sdm(BC_Occurre~temp_mean+DO_mean+ph_mean+salinity_mean, data=data, methods=c('rf', 'svm', 'gam'))

    ## Loading required package: parallel

Random forest uses bootstrapping (sampling with replacement from
original dataset, both predictor and response variables), then each
bootstrapped sample is used to create a tree. Once you have a tree, it’s
then broken up by creating two subsets with the minimum possible total
intrasubset variance. This then happens again to “grow the decision
tree”. The trees then get to “vote” on the probability of presence or
absence based on the predictor data at each spatial location.

The support vector machine algorithm, finds a hyperplane in an
N-dimensional space (n=the number of features, so in our case, 4) that
distinctly classifies the data points. Of course, there are lots of
different hyperplanes to choose from, so the algorithm wants to find a
plane that has the maximum distance between data points of both classes.

Generalized additive models builds off of GLMs by replacing linear
predictors with additive predictors. GAMs allow for more flexibility
than GLMs to account for variability in the data.

# Model evaluation

Now let’s specify the number of replications for each algorithm and how
much of the original dataset should be saved as a testing set.

    model_2 <- sdm(BC_Occurre~temp_mean+DO_mean+ph_mean+salinity_mean, data=data, methods=c('rf', 'svm', 'gam'), replication='sub', test.percent=20, n=2)

    getModelInfo(model_2)

    ##   modelID    species method replication replicationID success training test.dep
    ## 1       1 BC_Occurre     rf subsampling             1    TRUE     TRUE     TRUE
    ## 2       2 BC_Occurre     rf subsampling             2    TRUE     TRUE     TRUE
    ## 3       3 BC_Occurre    svm subsampling             1    TRUE     TRUE     TRUE
    ## 4       4 BC_Occurre    svm subsampling             2    TRUE     TRUE     TRUE
    ## 5       5 BC_Occurre    gam subsampling             1    TRUE     TRUE     TRUE
    ## 6       6 BC_Occurre    gam subsampling             2    TRUE     TRUE     TRUE
    ##   test.indep
    ## 1      FALSE
    ## 2      FALSE
    ## 3      FALSE
    ## 4      FALSE
    ## 5      FALSE
    ## 6      FALSE

Generate receiver operating characteristic curves for each of your
classifying methods. These show the trade-offs between the sensitivity
(TPR, true positive rate) and specificity (1-FPR, false positive rate).

    roc(model_2)

![](sdm_activity_bluecrabs_files/figure-markdown_strict/unnamed-chunk-13-1.png)

# Let’s predict!

    predict_2 <- predict(model_2, newdata=preds)

    ## Warning in .rasterFromRasterFile(grdfile, band = band, objecttype, ...): size of
    ## values file does not match the number of cells (given the data type)

    ## Warning in .rasterFromRasterFile(grdfile, band = band, objecttype, ...): size of
    ## values file does not match the number of cells (given the data type)

    nlayers(predict_2)

    ## [1] 6

    # There are 6
    plot(predict_2[[1:6]])

![](sdm_activity_bluecrabs_files/figure-markdown_strict/unnamed-chunk-14-1.png)

Take the mean of raster over different runs for each method and species.
Then plot the predictions. It should produce three figures.

    predict_2_mean <- predict(model_2, newdata=preds, mean=T)

    ## Warning in .rasterFromRasterFile(grdfile, band = band, objecttype, ...): size of
    ## values file does not match the number of cells (given the data type)

    ## Warning in .rasterFromRasterFile(grdfile, band = band, objecttype, ...): size of
    ## values file does not match the number of cells (given the data type)

    plot(predict_2_mean)

![](sdm_activity_bluecrabs_files/figure-markdown_strict/unnamed-chunk-15-1.png)

If you want to pull out the results of one classifying algorithm and
take a closer look, you can. Let’s take a look and see how the predicted
results compare to the orginal presence data points.

    plot(predict_2_mean[["sp_1.m_rf.re_subs"]])
    points(Blue_crabs[Blue_crabs$BC_Occurre == 1,], col='blue', pch=16)

![](sdm_activity_bluecrabs_files/figure-markdown_strict/unnamed-chunk-16-1.png)
