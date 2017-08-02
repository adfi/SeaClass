
The SeaClass R Package
==================

OTAAG has created an R based package named SeaClass which is available for any Seagate data analyst to download. SeaClass provides tools for analyzing classification problems. In particular, specialized tools are available for addressing the problem of imbalanced data sets. Imbalanced data, such as Pass/Fail variables, is a very common problem at Seagate. The SeaClass application provides an easy to use interface which requires no R programming knowledge. The application allows the user to explore numerous methods by simply clicking on the available options and interacting with the generated results. The user can choose to download the codes for any procedures they wish to explore further. SeaClass was designed to jump start the analysis process for both novice and advanced R users. The application easily transforms an analyst's job from days to minutes. The application is already being used by data scientists within OTAAG and other Seagate organizations.

### Install Instructions
The SeaClass application depends on numerous R packages. The most common problem using SeaClass is caused by not fully installing all its dependencies. Begin by running:
```r
install.packages(pkgs = c("abind","mcmc","shiny", "shinyjs", "miniUI", "V8", "rstudioapi", "class", "DMwR", "DT", "e1071", "ggplot2", "glmnet", "htmltools", "MASS", "PRROC", "randomForest", "robustbase", "rpart", "xgboost", "Zelig"))
```
Examine the R console and re-install any packages that failed during install. Next download the version of <a href="https://cran.r-project.org/bin/windows/Rtools/" target="_blank">Rtools</a> corresponding to your instance of R. Note that SeaClass was built under R 3.4.1 and future versions may require repairs. 

Download the zipped SeaClass package by clicking <a href="http://datascience.seagate.com/files/SeaClass_1.0.0.tar.gz" target="_blank">here</a>. To install, replace the path variable and run:
```r
install.packages("path/SeaClass_1.0.0.tar.gz", repos = NULL, type="source")
```

### Usage Instructions
Begin by loading and preparing your data in R. Some general advice:
  * Your data set must be saved as an R data frame object.
  * The data set must contain a binary response variable (0/1, PASS/FAIL, A/B, etc.)
  * All other variables must be predictor variables. 
  * Predictor variables can be numeric, categorical, or factors.
  * Including too many predictors may slow down the application and weaken performance.
  * Categorical predictors are often ignored when the number of levels exceeds 10 since they tend to have improper influences.
  * Missing values are not allowed and will throw a flag. Please remove or impute NAs prior to starting the app.
  * Keep the number of observations (rows) to a medium or small size. 
  * Data sets with many rows (>10,000) or many columns (>30) may slow down the app's interactive responses. 

After data preparation, start the application by either loading SeaClass from the RStudio Addins dropdown menu or by loading the the SeaClass function from the command line. For example:
```r
library(SeaClass)

### Make some fake data:
X <- matrix(rnorm(10000,0,1),ncol=10,nrow=1000)
X[1:100,1:2] <- X[1:100,1:2] + 3
Y <- c(rep(1,100), rep(0,900))
Fake_Data <- data.frame(Y = Y , X)

### Load the SeaClass rare failure data:
data("rareFailData")

### Start the interactive GUI: 
SeaClass()
```
If the application fails to load, you may need to first specify your favorite browser path. For example:
```r
options(browser = "C:/Program Files (x86)/Google/Chrome/Application/chrome.exe")
```
The user has various options for configuring their analysis within the GUI. Once the analysis runs, the user can view the results, interact with the results (module dependent), save the underlying R script, or start over. Additional help is provided within the application. 

Besides the SeaClass function, several other functions are contained within the library. For example:
```r
### List available functions:
ls("package:SeaClass")
### Note this is a sample data set:
# data(rareFailData)
### Note code_output is a support function for SeaClass, not good for general use. 

### View help:
?accuracy_threshold

### Run example from help file:
### General Use: ###
set.seed(123)
x <- c(rnorm(100,0,1),rnorm(100,2,1))
group <- c(rep(0,100),rep(2,100))
accuracy_threshold(x=x, group=group, pos_class=2)
accuracy_threshold(x=x, group=group, pos_class=0)
### Bagged Example ###
set.seed(123)
replicate_function = function(index){accuracy_threshold(x=x[index], group=group[index], pos_class=2)[[2]]}
sample_cuts = replicate(100, {
  sample_index = sample.int(n=length(x),replace=TRUE)
  replicate_function(index=sample_index)
})
bagged_scores = sapply(x, function(x) mean(x > sample_cuts))
unbagged_cut    = accuracy_threshold(x=x, group=group, pos_class=2)[[2]]
unbagged_scores = ifelse(x > unbagged_cut, 1, 0)
# Compare AUC:
PRROC::roc.curve(scores.class0 = bagged_scores,weights.class0 = ifelse(group==2,1,0))[[2]]
PRROC::roc.curve(scores.class0 = unbagged_scores,weights.class0 = ifelse(group==2,1,0))[[2]]
bagged_prediction = ifelse(bagged_scores > 0.50, 2, 0)
unbagged_prediction = ifelse(x > unbagged_cut, 2, 0)
# Compare Confusion Matrix:
table(bagged_prediction, group)
table(unbagged_prediction, group)
```
