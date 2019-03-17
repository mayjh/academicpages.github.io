---
title: "Machine Learning with R - an mlr Starter"
excerpt: "mlr is one of my favorite R machine learning packages. Here I wrote a walk-through of the package to help myself and hopefully be of some help to folks out there as well!"
collection: projects
---

[mlr](https://mlr.mlr-org.com/index.html) is a fairly comprehensive machine learning package in R. Despite the many tutorials out there, I didn't find a handy yet comprehensive one that I can refer to for my common machine learning questions, so I set out to write one.

The report was created using R Markdown and then published to [rpubs.com](https://rpubs.com), a free web publishing service sponsored by RStudio. The image below is a screenshot of the report. Please see the full report below and [here](http://rpubs.com/mayjh/mlr_starter).

SUMMARY
=======

A brief walk-through of mlr functions that are necessary for basic model tunning.

mlr vs caret
============

1.  mlr has more possibilities to create different types of tasks: Classification, Regression, Survival, Clustering, Multilabel, Cost-sensitive, Imbalanced data, Functional data, Spatial data. caret only seems to have possibilities for Classification, Regression and Cost-sensitive.

2.  mlr has 71 metrics which are easy to choose from, more complicated to change metrics in caret.

3.  mlr supports more tuning methods such as F-Racing and model-based optimization, while caret supports only random and grid search.

4.  mlr is more compatible for parallelization.

Preprocessing
=============

------------------------------------------------------------------------

**Goal:**

1.  Load data
2.  Quick preprocessing

``` r
data(BreastCancer, package = 'mlbench')
df <- BreastCancer
```

``` r
str(df)
```

    ## 'data.frame':    699 obs. of  11 variables:
    ##  $ Id             : chr  "1000025" "1002945" "1015425" "1016277" ...
    ##  $ Cl.thickness   : Ord.factor w/ 10 levels "1"<"2"<"3"<"4"<..: 5 5 3 6 4 8 1 2 2 4 ...
    ##  $ Cell.size      : Ord.factor w/ 10 levels "1"<"2"<"3"<"4"<..: 1 4 1 8 1 10 1 1 1 2 ...
    ##  $ Cell.shape     : Ord.factor w/ 10 levels "1"<"2"<"3"<"4"<..: 1 4 1 8 1 10 1 2 1 1 ...
    ##  $ Marg.adhesion  : Ord.factor w/ 10 levels "1"<"2"<"3"<"4"<..: 1 5 1 1 3 8 1 1 1 1 ...
    ##  $ Epith.c.size   : Ord.factor w/ 10 levels "1"<"2"<"3"<"4"<..: 2 7 2 3 2 7 2 2 2 2 ...
    ##  $ Bare.nuclei    : Factor w/ 10 levels "1","2","3","4",..: 1 10 2 4 1 10 10 1 1 1 ...
    ##  $ Bl.cromatin    : Factor w/ 10 levels "1","2","3","4",..: 3 3 3 3 3 9 3 3 1 2 ...
    ##  $ Normal.nucleoli: Factor w/ 10 levels "1","2","3","4",..: 1 2 1 7 1 7 1 1 1 1 ...
    ##  $ Mitoses        : Factor w/ 9 levels "1","2","3","4",..: 1 1 1 1 1 1 1 1 5 1 ...
    ##  $ Class          : Factor w/ 2 levels "benign","malignant": 1 1 1 1 1 2 1 1 1 1 ...

``` r
summary(df)
```

    ##       Id             Cl.thickness   Cell.size     Cell.shape 
    ##  Length:699         1      :145   1      :384   1      :353  
    ##  Class :character   5      :130   10     : 67   2      : 59  
    ##  Mode  :character   3      :108   3      : 52   10     : 58  
    ##                     4      : 80   2      : 45   3      : 56  
    ##                     10     : 69   4      : 40   4      : 44  
    ##                     2      : 50   5      : 30   5      : 34  
    ##                     (Other):117   (Other): 81   (Other): 95  
    ##  Marg.adhesion  Epith.c.size  Bare.nuclei   Bl.cromatin  Normal.nucleoli
    ##  1      :407   2      :386   1      :402   2      :166   1      :443    
    ##  2      : 58   3      : 72   10     :132   3      :165   10     : 61    
    ##  3      : 58   4      : 48   2      : 30   1      :152   3      : 44    
    ##  10     : 55   1      : 47   5      : 30   7      : 73   2      : 36    
    ##  4      : 33   6      : 41   3      : 28   4      : 40   8      : 24    
    ##  8      : 25   5      : 39   (Other): 61   5      : 34   6      : 22    
    ##  (Other): 63   (Other): 66   NA's   : 16   (Other): 69   (Other): 69    
    ##     Mitoses          Class    
    ##  1      :579   benign   :458  
    ##  2      : 35   malignant:241  
    ##  3      : 33                  
    ##  10     : 14                  
    ##  4      : 12                  
    ##  7      :  9                  
    ##  (Other): 17

Looks like we have an ID column, which we should remove in the prediction. We also have some NA values for the Bare.nuclei column, let's set the NAs to be a different class.

``` r
df$Id = NULL
new_col <- as.numeric(df$Bare.nuclei)
new_col[is.na(new_col)] <- 5.5
new_col <- factor(new_col, levels = c("1", "2", "3", "4", "5", "5.5", "6", "7", "8", "9", "10"), ordered = T)
table(new_col)
```

    ## new_col
    ##   1   2   3   4   5 5.5   6   7   8   9  10 
    ## 402  30  28  19  30  16   4   8  21   9 132

``` r
df$Bare.nuclei <- new_col
df$Epith.c.size <- NULL
```

Other options:

1.  createDummyFeatures
2.  normalizeFeatures (center, scale, standardize, range)
3.  mergeSmallFactorLevels
4.  capLargeValues, dropFeatures, removeConstantFeatures, summarizeLevels
5.  interface to caret preprocessing ...

Tasks
=====

------------------------------------------------------------------------

**Goal:**

1.  Set data (data)
2.  Set target (target)
3.  Set task ID (id)

``` r
bc_task = makeClassifTask(data = df, target = 'Class')
bc_task
```

    ## Supervised task: df
    ## Type: classif
    ## Target: Class
    ## Observations: 699
    ## Features:
    ##    numerics     factors     ordered functionals 
    ##           0           3           5           0 
    ## Missings: FALSE
    ## Has weights: FALSE
    ## Has blocking: FALSE
    ## Has coordinates: FALSE
    ## Classes: 2
    ##    benign malignant 
    ##       458       241 
    ## Positive class: benign

``` r
bc_task = makeClassifTask(id = 'breast cancer classification', 
                          data = df, target = 'Class',
                          positive = "malignant")
```

Notes:

1.  For classification the target column has to be a factor.
2.  makeClassifTask() by default selects the first factor level of the target variable as the positive class. When necessary, use the *positive* argument to modify.

Other tasks:

-   RegrTask() for regression problems
-   ClassifTask() for binary and multi-class classification problems with class-dependent costs can be handled as well)
-   SurvTask() for survival analysis
-   ClusterTask() for cluster analysis
-   MultilabelTask() for multilabel classification problems
-   CostSensTask() for general cost sensitive classification (with example-specific costs)

Learners
========

------------------------------------------------------------------------

**Goal:**

1.  Set package and method (class of learner, cl argument)
2.  Set prediction output type (predict.type)
3.  Set hyperparameters (par.vals)
4.  Set learner ID (id)

``` r
bc_learner = makeLearner(cl = "classif.ranger",  # class of learner
                         predict.type = "prob", # prediction output type
                         par.vals = list(), # set hyperparameters
                         predict.threshold = NULL, # threshold for prediction
                         fix.factors.prediction = TRUE,
                         id = 'bc ranger') # add a factor level for missing data in test
bc_learner
```

    ## Learner bc ranger from package ranger
    ## Type: classif
    ## Name: Random Forests; Short name: ranger
    ## Class: classif.ranger
    ## Properties: twoclass,multiclass,prob,numerics,factors,ordered,featimp,weights,oobpreds
    ## Predict-Type: prob
    ## Hyperparameters: num.threads=1,verbose=FALSE,respect.unordered.factors=order

To see available learners for the particular task:

``` r
learners <- listLearners(bc_task, properties = 'prob', check.packages = FALSE)
head(learners[,c('class', 'package')])
```

    ##                  class      package
    ## 1      classif.cforest        party
    ## 2        classif.ctree        party
    ## 3       classif.evtree       evtree
    ## 4  classif.featureless          mlr
    ## 5    classif.penalized    penalized
    ## 6 classif.randomForest randomForest

Note that somehow this function is not returning all available options for me, so I'd recommend checking out [link](https://mlr.mlr-org.com/articles/tutorial/integrated_learners.html) for all **Integrated learners:**.

A handy function to examine the available hyperparameters is to use the getParamSet function:

``` r
getParamSet(bc_learner)
```

    ##                                       Type  len    Def
    ## num.trees                          integer    -    500
    ## mtry                               integer    -      -
    ## min.node.size                      integer    -      -
    ## replace                            logical    -   TRUE
    ## sample.fraction                    numeric    -      -
    ## split.select.weights         numericvector <NA>      -
    ## always.split.variables             untyped    -      -
    ## respect.unordered.factors         discrete    - ignore
    ## importance                        discrete    -   none
    ## write.forest                       logical    -   TRUE
    ## scale.permutation.importance       logical    -  FALSE
    ## num.threads                        integer    -      -
    ## save.memory                        logical    -  FALSE
    ## verbose                            logical    -   TRUE
    ## seed                               integer    -      -
    ## splitrule                         discrete    -   gini
    ## num.random.splits                  integer    -      1
    ## keep.inbag                         logical    -  FALSE
    ##                                                 Constr Req Tunable Trafo
    ## num.trees                                     1 to Inf   -    TRUE     -
    ## mtry                                          1 to Inf   -    TRUE     -
    ## min.node.size                                 1 to Inf   -    TRUE     -
    ## replace                                              -   -    TRUE     -
    ## sample.fraction                                 0 to 1   -    TRUE     -
    ## split.select.weights                            0 to 1   -    TRUE     -
    ## always.split.variables                               -   -    TRUE     -
    ## respect.unordered.factors       ignore,order,partition   -    TRUE     -
    ## importance                   none,impurity,permutation   -   FALSE     -
    ## write.forest                                         -   -   FALSE     -
    ## scale.permutation.importance                         -   Y   FALSE     -
    ## num.threads                                   1 to Inf   -   FALSE     -
    ## save.memory                                          -   -   FALSE     -
    ## verbose                                              -   -   FALSE     -
    ## seed                                       -Inf to Inf   -   FALSE     -
    ## splitrule                              gini,extratrees   -    TRUE     -
    ## num.random.splits                             1 to Inf   Y    TRUE     -
    ## keep.inbag                                           -   -   FALSE     -

**List of properties:**

-   *numerics*: The method can cope with numerical predictors
-   *factors*: The method can cope with factor predictors
-   *ordered*: The method can cope with ordered factor predictors
-   *NAs*: The method can deal with missing values in a meaningful way (other than simply removing observations with missing values)
-   *weights*: The method support observation weights
-   *prob*: The method can predict probabilities
-   *oneclass, twoclass, multiclass*: One-class, two-class (binary) or multi-class classification problems be handled
-   *class.weights*: Class weights can be handled
-   *featimp*: The method can return feature importance
-   *oobpreds*: The method can make out-of-bag predictions

Train
=====

------------------------------------------------------------------------

**Goal:**

1.  Set learner
2.  Set task
3.  Set subset of data
4.  Set observation weights

``` r
bc_model <- train(learner = bc_learner, 
                  task = bc_task)
bc_model
```

    ## Model for learner.id=bc ranger; learner.class=classif.ranger
    ## Trained on: task.id = breast cancer classification; obs = 699; features = 8
    ## Hyperparameters: num.threads=1,verbose=FALSE,respect.unordered.factors=order

If the default setting of the learner is okay, you can skip makeLearner() and just start to train the model.

``` r
bc_model_default <- train(learner = 'classif.ranger', 
                          task = bc_task) 
bc_model_default
```

    ## Model for learner.id=classif.ranger; learner.class=classif.ranger
    ## Trained on: task.id = breast cancer classification; obs = 699; features = 8
    ## Hyperparameters: num.threads=1,verbose=FALSE,respect.unordered.factors=order

To train only a subset, use the *subset* argument.

``` r
n <- nrow(df)
in_train <- sample.int(n, round(0.8*n))
in_test <- setdiff(1:n, in_train)
bc_model <- train(learner = bc_learner, 
                  task = bc_task,
                  subset = in_train)
```

To assign weight to observations if supported by learner. Use cases:
1. reduce the influence of outliers 2. increase the influence of certain data, e.g. more recent data 3. incorporate misclassification costs 4. account for class imbalance, give both classes equal importance

``` r
target <- df$Class[in_train]
tab <- as.numeric(table(target))
w <- 1/tab[target]
bc_model <- train(learner = bc_learner, 
                  task = bc_task,
                  subset = in_train,
                  weights = w)
```

Predict
=======

------------------------------------------------------------------------

**Goal:**

1.  make predictions
2.  adjust threshold
3.  visualize the prediction

To make a prediction:

``` r
bc_pred <- predict(object = bc_model,
                   task = bc_task, 
                   subset = in_test)
bc_pred
```

    ## Prediction: 140 observations
    ## predict.type: prob
    ## threshold: benign=0.50,malignant=0.50
    ## time: 0.04
    ##    id     truth prob.benign prob.malignant  response
    ## 4   4    benign   0.1196789   0.8803211018 malignant
    ## 9   9    benign   0.9138379   0.0861621203    benign
    ## 13 13 malignant   0.3075421   0.6924579365 malignant
    ## 21 21 malignant   0.1363444   0.8636555556 malignant
    ## 24 24 malignant   0.1332519   0.8667480859 malignant
    ## 28 28    benign   0.9995886   0.0004114003    benign
    ## ... (#rows: 140, #cols: 5)

To adjust threshold:

``` r
bc_pred_2 <- setThreshold(pred = bc_pred, threshold = 0.8)
```

To visualize the prediction:

``` r
# plotLearnerPrediction(learner = bc_learner, task = bc_task,
#                       features = c('Cell.shape', 'Marg.adhesion'))
lrn = makeLearner("classif.rpart", id = "CART")
plotLearnerPrediction(lrn, task = iris.task)
```

![](Untitled_files/figure-markdown_github/unnamed-chunk-15-1.png)

Figure note: The type of symbol shows the true class labels of the data points. Symbols with white border indicate misclassified observations. The posterior probabilities (if the learner under consideration supports this) are represented by the background color where higher saturation means larger probabilities.

Handy functions:
1. getPredictionTruth( bc\_pred )
2. getPredictionResponse( bc\_pred )
3. getPredictionSE() for standard errors of regression predictions
4. getPredictionProbabilities( bc\_pred )
5. calculateConfusionMatrix()

Performance
===========

------------------------------------------------------------------------

**Goal:**

1.  access performance measures
2.  plot performance

To access performance measures:

``` r
## list measures
listMeasures("classif", properties = "classif.multi")
```

    ##  [1] "featperc"         "mmce"             "lsr"             
    ##  [4] "bac"              "qsr"              "timeboth"        
    ##  [7] "multiclass.aunp"  "timetrain"        "multiclass.aunu" 
    ## [10] "ber"              "timepredict"      "multiclass.brier"
    ## [13] "ssr"              "acc"              "logloss"         
    ## [16] "wkappa"           "multiclass.au1p"  "multiclass.au1u" 
    ## [19] "kappa"

Task-specific measures:

``` r
listMeasures(bc_task)
```

    ##  [1] "tnr"              "tpr"              "featperc"        
    ##  [4] "f1"               "mmce"             "mcc"             
    ##  [7] "brier.scaled"     "lsr"              "bac"             
    ## [10] "fn"               "fp"               "fnr"             
    ## [13] "qsr"              "fpr"              "npv"             
    ## [16] "brier"            "auc"              "timeboth"        
    ## [19] "multiclass.aunp"  "timetrain"        "multiclass.aunu" 
    ## [22] "ber"              "timepredict"      "multiclass.brier"
    ## [25] "ssr"              "ppv"              "acc"             
    ## [28] "logloss"          "wkappa"           "tn"              
    ## [31] "tp"               "multiclass.au1p"  "multiclass.au1u" 
    ## [34] "fdr"              "kappa"            "gpr"             
    ## [37] "gmean"

To access performance measures:

``` r
performance(pred = bc_pred, measures = list(f1, fpr, auc, acc))
```

    ##         f1        fpr        auc        acc 
    ## 0.96226415 0.03409091 0.99125874 0.97142857

To plot performance vs threshold:

``` r
d <- generateThreshVsPerfData(bc_pred, measures = list(fpr, fnr))
plotThreshVsPerf(d)
```

![](Untitled_files/figure-markdown_github/unnamed-chunk-19-1.png)

Resampling
==========

------------------------------------------------------------------------

**Goal:**

1.  set resampling strategy, e.g., cross-validation, holdout
2.  set resampling instance

To set resampling:

``` r
# create resampling strategy, note that cv5 is pre-defined in mlr as well
cv5 <- makeResampleDesc(method = 'CV', iters = 5, stratify = T)
bc_cv5 <- resample(learner = bc_learner, 
                   task = bc_task, 
                   resampling = cv5,
                   measures = auc,
                   models = T) # to keep the model
```

    ## Resampling: cross-validation

    ## Measures:             auc

    ## [Resample] iter 1:    0.9954212

    ## [Resample] iter 2:    0.9917582

    ## [Resample] iter 3:    0.9907156

    ## [Resample] iter 4:    0.9993207

    ## [Resample] iter 5:    0.9811446

    ## 

    ## Aggregated Result: auc.test.mean=0.9916721

    ## 

To access the resampling results:

``` r
bc_cv5$aggr
```

    ## auc.test.mean 
    ##     0.9916721

``` r
bc_cv5$measures.test
```

    ##   iter       auc
    ## 1    1 0.9954212
    ## 2    2 0.9917582
    ## 3    3 0.9907156
    ## 4    4 0.9993207
    ## 5    5 0.9811446

To set resampling instance:

``` r
bc_cv5_in <- makeResampleInstance(desc = cv5, task = bc_task)
```

Resample instances make it easy to reproduce model results and to compare models. We can run different models on the same resampling instance with the same split of the data, which is more fair than comparisons across different splits of the data.

Tuning
======

------------------------------------------------------------------------

**Goal:**

1.  tune hyperparameters

**Steps:**

First, set the search space:

``` r
bc_params <- makeParamSet(
  makeIntegerParam('num.trees', lower = 100, upper = 1000),
  makeIntegerParam('mtry', lower = 1, upper = 9)
)
```

More options:

-   makeNumericParam("u", lower=1)
-   makeIntegerParam("v", lower=1, upper=2)
-   makeDiscreteParam("w", values=1:2)
-   makeLogicalParam("x")
-   makeDiscreteVectorParam("y", len=2, values=c("a", "b"))

Second, select the optimization algorithm:

``` r
bc_control = makeTuneControlRandom(maxit = 5L)
```

More options:

-   makeTuneControlGrid()
-   makeTuneControlIrace()

Third, we select the resampling strategy and the performance measure for the tuning. In this case let's use the pre-defined resampling method cv5.

``` r
bc_tune <- tuneParams(learner = bc_learner,
                      task = bc_task, 
                      resampling = cv5,
                      par.set = bc_params, 
                      control = bc_control,
                      measures = auc)
```

    ## [Tune] Started tuning learner bc ranger for parameter set:

    ##              Type len Def       Constr Req Tunable Trafo
    ## num.trees integer   -   - 100 to 1e+03   -    TRUE     -
    ## mtry      integer   -   -       1 to 9   -    TRUE     -

    ## With control class: TuneControlRandom

    ## Imputation value: -0

    ## [Tune-x] 1: num.trees=256; mtry=4

    ## [Tune-y] 1: auc.test.mean=0.9919186; time: 0.0 min

    ## [Tune-x] 2: num.trees=334; mtry=7

    ## [Tune-y] 2: auc.test.mean=0.9895292; time: 0.0 min

    ## [Tune-x] 3: num.trees=763; mtry=7

    ## [Tune-y] 3: auc.test.mean=0.9915533; time: 0.0 min

    ## [Tune-x] 4: num.trees=350; mtry=8

    ## [Tune-y] 4: auc.test.mean=0.9896999; time: 0.0 min

    ## [Tune-x] 5: num.trees=769; mtry=5

    ## [Tune-y] 5: auc.test.mean=0.9919644; time: 0.0 min

    ## [Tune] Result: num.trees=769; mtry=5 : auc.test.mean=0.9919644

To access the tuning results:

``` r
# best-performing parameters
bc_tune$x
```

    ## $num.trees
    ## [1] 769
    ## 
    ## $mtry
    ## [1] 5

``` r
# best performance
bc_tune$y
```

    ## auc.test.mean 
    ##     0.9919644

To update the learner with the best parameters:

``` r
final_bc_learner <- setHyperPars(learner = bc_learner, 
                                 par.vals = bc_tune$x)
final_bc_model <- train(learner = final_bc_learner, 
                        task = bc_task,
                        subset = in_train)
final_bc_predict <- predict(object = final_bc_model, 
                            task = bc_task,
                            subset = in_test)
```

To view the tuning effect:

``` r
tune_data = generateHyperParsEffectData( tune.result = bc_tune )
plotHyperParsEffect(hyperpars.effect.data = tune_data, 
                    x = "iteration", y = "auc.test.mean",
                    plot.type = "line")
```

![](Untitled_files/figure-markdown_github/unnamed-chunk-28-1.png)

Benchmark Experiments
=====================

------------------------------------------------------------------------

**Goal:**

1.  compare and rank the algorithms

**Steps:**

First, build individual learners to compare and put in a list.

``` r
# xgboost learner
bc_learner_rpart <- makeLearner(cl = 'classif.rpart', 
                              predict.type = 'prob',
                              id = 'bc rpart')

# list learners together
learners <- list(bc_learner, bc_learner_rpart)
```

Second, choose the resampling strategy and benchmark. Note that we are comparing the learners on the default hyperparameters before tuning. If you want each model to pull out its A-game for the benchmarking, make sure to tune the parameters beforehand.

``` r
# choose the resampling strategy
bc_cv5_in <- makeResampleInstance(desc = cv5, task = bc_task)

bc_benchmark <- benchmark(learners = learners, 
                          task = bc_task,
                          resamplings = bc_cv5_in, 
                          measures = auc,
                          keep.pred = F) # drop predictions to conserve memeory
```

    ## Task: breast cancer classification, Learner: bc ranger

    ## Resampling: cross-validation

    ## Measures:             auc

    ## [Resample] iter 1:    0.9922161

    ## [Resample] iter 2:    0.9927536

    ## [Resample] iter 3:    0.9898098

    ## [Resample] iter 4:    0.9891304

    ## [Resample] iter 5:    0.9948419

    ## 

    ## Aggregated Result: auc.test.mean=0.9917504

    ## 

    ## Task: breast cancer classification, Learner: bc rpart

    ## Resampling: cross-validation

    ## Measures:             auc

    ## [Resample] iter 1:    0.9371566

    ## [Resample] iter 2:    0.9718071

    ## [Resample] iter 3:    0.9527853

    ## [Resample] iter 4:    0.9642210

    ## [Resample] iter 5:    0.9543620

    ## 

    ## Aggregated Result: auc.test.mean=0.9560664

    ## 

Third, understand the benchmark results.

``` r
# performance per iteration
getBMRPerformances(bc_benchmark)
```

    ## $`breast cancer classification`
    ## $`breast cancer classification`$`bc ranger`
    ##   iter       auc
    ## 1    1 0.9922161
    ## 2    2 0.9927536
    ## 3    3 0.9898098
    ## 4    4 0.9891304
    ## 5    5 0.9948419
    ## 
    ## $`breast cancer classification`$`bc rpart`
    ##   iter       auc
    ## 1    1 0.9371566
    ## 2    2 0.9718071
    ## 3    3 0.9527853
    ## 4    4 0.9642210
    ## 5    5 0.9543620

``` r
# aggregate performance
getBMRAggrPerformances(bc_benchmark, as.df = T) # as.df make the results easier to read
```

    ##                        task.id learner.id auc.test.mean
    ## 1 breast cancer classification  bc ranger     0.9917504
    ## 2 breast cancer classification   bc rpart     0.9560664

Further, to visualize the benchmark results.

``` r
plotBMRBoxplots(bmr = bc_benchmark, 
                measure = auc)
```

![](Untitled_files/figure-markdown_github/unnamed-chunk-32-1.png)

Reference
=========

------------------------------------------------------------------------

<https://mlr.mlr-org.com/index.html> [mlr vs. caret](http://philipppro.github.io/2018-11-9-mlr_vs_caret/)

<img src='/images/mlr_report.png'>
