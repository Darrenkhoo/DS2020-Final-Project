DS2020 Final Project Report
================
Hong Hao Khoo and Harsh Mishrikotkar
2025-04-28

# Benign or Malignant Cancer

## Introduction

Cancer is a group of diseases characterized by the uncontrolled growth
and spread of abnormal cells in the body. Cancer, also known as tumor
are often mixed up. A tumor is a lump or mass of tissue that forms when
cells grow and divide too much or don’t die when they should.
Scientifically, tumors can be categorized into benign or malignant.
Benign tumors do not invade nearby tissues or spread to other parts of
the body, whereas malignant tumors do. Malignant tumors are what we call
cancer. If it is malignant, then it is a cancer otherwise it is not. For
this project’s purpose, we are going to stick with the word ‘cancer’ in
place of ‘tumor’ as it is more familiarized, but we will specify benign
or malignant when necessary.

Benign cancer are much more common and many are not recorded. It is
estimated that 20 million malignant cancer are diagnosed each year and
100+ million for benign. When diagnosed with cancer but not knowing if
it’s benign or malignant is a confusing and frightening moment - you’re
stuck waiting, hoping for good news but scared of the worst. So, in this
project we aim to analyze a dataset containing records of patients
diagnosed with cancer. Each patient is given an unique ID along with the
type of cancer, benign or malignant. It also contains the visual
characteristics of the cancer and a value for each of these
characteristics.

Through the analysis of this dataset, we hope to find out:

1.  What are the average range of value for each characteristic for
    benign and malignant cancer?

2.  Is there a correlation between the characteristics and the type of
    cancer?

3.  Which characteristic is the most highly correlated and which is
    lowest to the type of cancer?

4.  Can we predict whether a cancer tumor is benign or malignant base on
    its characteristics?

5.  Is it possible to speed up the process of identifying whether a
    cancer is benign or malignant?

## Dataset

The link to the dataset is
<https://www.kaggle.com/datasets/erdemtaha/cancer-data>. The website
contains a downloadable csv file, which is the cancer dataset. This
dataset have 569 observations and 32 variables.

``` r
# Import dataset
data <- read_csv("Cancer_Data.csv")
```

    ## New names:
    ## • `` -> `...33`

    ## Warning: One or more parsing issues, call `problems()` on your data frame for details,
    ## e.g.:
    ##   dat <- vroom(...)
    ##   problems(dat)

    ## Rows: 568 Columns: 33
    ## ── Column specification ────────────────────────────────────────────────────────
    ## Delimiter: ","
    ## chr  (1): diagnosis
    ## dbl (31): id, radius_mean, texture_mean, perimeter_mean, area_mean, smoothne...
    ## lgl  (1): ...33
    ## 
    ## ℹ Use `spec()` to retrieve the full column specification for this data.
    ## ℹ Specify the column types or set `show_col_types = FALSE` to quiet this message.

``` r
data <- data[1:(length(data)-1)]

# View dataset
head(data, 10)
```

    ## # A tibble: 10 × 32
    ##          id diagnosis radius_mean texture_mean perimeter_mean area_mean
    ##       <dbl> <chr>           <dbl>        <dbl>          <dbl>     <dbl>
    ##  1   842302 M                18.0         10.4          123.      1001 
    ##  2   842517 M                20.6         17.8          133.      1326 
    ##  3 84300903 M                19.7         21.2          130       1203 
    ##  4 84348301 M                11.4         20.4           77.6      386.
    ##  5 84358402 M                20.3         14.3          135.      1297 
    ##  6   843786 M                12.4         15.7           82.6      477.
    ##  7   844359 M                18.2         20.0          120.      1040 
    ##  8 84458202 M                13.7         20.8           90.2      578.
    ##  9   844981 M                13           21.8           87.5      520.
    ## 10 84501001 M                12.5         24.0           84.0      476.
    ## # ℹ 26 more variables: smoothness_mean <dbl>, compactness_mean <dbl>,
    ## #   concavity_mean <dbl>, `concave points_mean` <dbl>, symmetry_mean <dbl>,
    ## #   fractal_dimension_mean <dbl>, radius_se <dbl>, texture_se <dbl>,
    ## #   perimeter_se <dbl>, area_se <dbl>, smoothness_se <dbl>,
    ## #   compactness_se <dbl>, concavity_se <dbl>, `concave points_se` <dbl>,
    ## #   symmetry_se <dbl>, fractal_dimension_se <dbl>, radius_worst <dbl>,
    ## #   texture_worst <dbl>, perimeter_worst <dbl>, area_worst <dbl>, …

## Cleaning

``` r
summary(data)
```

    ##        id             diagnosis          radius_mean      texture_mean  
    ##  Min.   :     8670   Length:568         Min.   : 6.981   Min.   : 9.71  
    ##  1st Qu.:   869222   Class :character   1st Qu.:11.707   1st Qu.:16.17  
    ##  Median :   906157   Mode  :character   Median :13.375   Median :18.84  
    ##  Mean   : 30425140                      Mean   :14.139   Mean   :19.28  
    ##  3rd Qu.:  8825022                      3rd Qu.:15.797   3rd Qu.:21.79  
    ##  Max.   :911320502                      Max.   :28.110   Max.   :39.28  
    ##  perimeter_mean     area_mean      smoothness_mean   compactness_mean 
    ##  Min.   : 43.79   Min.   : 143.5   Min.   :0.06251   Min.   :0.01938  
    ##  1st Qu.: 75.20   1st Qu.: 420.3   1st Qu.:0.08640   1st Qu.:0.06517  
    ##  Median : 86.29   Median : 551.4   Median :0.09589   Median :0.09312  
    ##  Mean   : 92.05   Mean   : 655.7   Mean   :0.09644   Mean   :0.10445  
    ##  3rd Qu.:104.15   3rd Qu.: 784.1   3rd Qu.:0.10533   3rd Qu.:0.13043  
    ##  Max.   :188.50   Max.   :2501.0   Max.   :0.16340   Max.   :0.34540  
    ##  concavity_mean    concave points_mean symmetry_mean    fractal_dimension_mean
    ##  Min.   :0.00000   Min.   :0.00000     Min.   :0.1060   Min.   :0.04996       
    ##  1st Qu.:0.02958   1st Qu.:0.02035     1st Qu.:0.1620   1st Qu.:0.05770       
    ##  Median :0.06155   Median :0.03360     Median :0.1792   Median :0.06155       
    ##  Mean   :0.08896   Mean   :0.04901     Mean   :0.1812   Mean   :0.06280       
    ##  3rd Qu.:0.13100   3rd Qu.:0.07401     3rd Qu.:0.1957   3rd Qu.:0.06613       
    ##  Max.   :0.42680   Max.   :0.20120     Max.   :0.3040   Max.   :0.09744       
    ##    radius_se        texture_se      perimeter_se       area_se       
    ##  Min.   :0.1115   Min.   :0.3602   Min.   : 0.757   Min.   :  6.802  
    ##  1st Qu.:0.2324   1st Qu.:0.8331   1st Qu.: 1.605   1st Qu.: 17.850  
    ##  Median :0.3240   Median :1.1080   Median : 2.285   Median : 24.565  
    ##  Mean   :0.4052   Mean   :1.2165   Mean   : 2.867   Mean   : 40.374  
    ##  3rd Qu.:0.4798   3rd Qu.:1.4743   3rd Qu.: 3.360   3rd Qu.: 45.237  
    ##  Max.   :2.8730   Max.   :4.8850   Max.   :21.980   Max.   :542.200  
    ##  smoothness_se      compactness_se      concavity_se     concave points_se 
    ##  Min.   :0.001713   Min.   :0.002252   Min.   :0.00000   Min.   :0.000000  
    ##  1st Qu.:0.005166   1st Qu.:0.013133   1st Qu.:0.01510   1st Qu.:0.007663  
    ##  Median :0.006374   Median :0.020460   Median :0.02592   Median :0.010950  
    ##  Mean   :0.007041   Mean   :0.025515   Mean   :0.03195   Mean   :0.011817  
    ##  3rd Qu.:0.008151   3rd Qu.:0.032455   3rd Qu.:0.04212   3rd Qu.:0.014730  
    ##  Max.   :0.031130   Max.   :0.135400   Max.   :0.39600   Max.   :0.052790  
    ##   symmetry_se       fractal_dimension_se  radius_worst   texture_worst  
    ##  Min.   :0.007882   Min.   :0.0008948    Min.   : 7.93   Min.   :12.02  
    ##  1st Qu.:0.015128   1st Qu.:0.0022445    1st Qu.:13.03   1st Qu.:21.07  
    ##  Median :0.018725   Median :0.0031955    Median :14.97   Median :25.41  
    ##  Mean   :0.020531   Mean   :0.0037967    Mean   :16.28   Mean   :25.67  
    ##  3rd Qu.:0.023398   3rd Qu.:0.0045585    3rd Qu.:18.80   3rd Qu.:29.68  
    ##  Max.   :0.078950   Max.   :0.0298400    Max.   :36.04   Max.   :49.54  
    ##  perimeter_worst    area_worst     smoothness_worst  compactness_worst
    ##  Min.   : 50.41   Min.   : 185.2   Min.   :0.07117   Min.   :0.02729  
    ##  1st Qu.: 84.15   1st Qu.: 515.7   1st Qu.:0.11660   1st Qu.:0.14758  
    ##  Median : 97.67   Median : 686.5   Median :0.13135   Median :0.21300  
    ##  Mean   :107.35   Mean   : 881.7   Mean   :0.13244   Mean   :0.25460  
    ##  3rd Qu.:125.53   3rd Qu.:1085.0   3rd Qu.:0.14602   3rd Qu.:0.33930  
    ##  Max.   :251.20   Max.   :4254.0   Max.   :0.22260   Max.   :1.05800  
    ##  concavity_worst  concave points_worst symmetry_worst   fractal_dimension_worst
    ##  Min.   :0.0000   Min.   :0.00000      Min.   :0.1565   Min.   :0.05504        
    ##  1st Qu.:0.1159   1st Qu.:0.06497      1st Qu.:0.2504   1st Qu.:0.07147        
    ##  Median :0.2275   Median :0.10002      Median :0.2821   Median :0.08005        
    ##  Mean   :0.2727   Mean   :0.11481      Mean   :0.2901   Mean   :0.08397        
    ##  3rd Qu.:0.3835   3rd Qu.:0.16168      3rd Qu.:0.3180   3rd Qu.:0.09208        
    ##  Max.   :1.2520   Max.   :0.29100      Max.   :0.6638   Max.   :0.20750

``` r
str(data)
```

    ## tibble [568 × 32] (S3: tbl_df/tbl/data.frame)
    ##  $ id                     : num [1:568] 842302 842517 84300903 84348301 84358402 ...
    ##  $ diagnosis              : chr [1:568] "M" "M" "M" "M" ...
    ##  $ radius_mean            : num [1:568] 18 20.6 19.7 11.4 20.3 ...
    ##  $ texture_mean           : num [1:568] 10.4 17.8 21.2 20.4 14.3 ...
    ##  $ perimeter_mean         : num [1:568] 122.8 132.9 130 77.6 135.1 ...
    ##  $ area_mean              : num [1:568] 1001 1326 1203 386 1297 ...
    ##  $ smoothness_mean        : num [1:568] 0.1184 0.0847 0.1096 0.1425 0.1003 ...
    ##  $ compactness_mean       : num [1:568] 0.2776 0.0786 0.1599 0.2839 0.1328 ...
    ##  $ concavity_mean         : num [1:568] 0.3001 0.0869 0.1974 0.2414 0.198 ...
    ##  $ concave points_mean    : num [1:568] 0.1471 0.0702 0.1279 0.1052 0.1043 ...
    ##  $ symmetry_mean          : num [1:568] 0.242 0.181 0.207 0.26 0.181 ...
    ##  $ fractal_dimension_mean : num [1:568] 0.0787 0.0567 0.06 0.0974 0.0588 ...
    ##  $ radius_se              : num [1:568] 1.095 0.543 0.746 0.496 0.757 ...
    ##  $ texture_se             : num [1:568] 0.905 0.734 0.787 1.156 0.781 ...
    ##  $ perimeter_se           : num [1:568] 8.59 3.4 4.58 3.44 5.44 ...
    ##  $ area_se                : num [1:568] 153.4 74.1 94 27.2 94.4 ...
    ##  $ smoothness_se          : num [1:568] 0.0064 0.00522 0.00615 0.00911 0.01149 ...
    ##  $ compactness_se         : num [1:568] 0.049 0.0131 0.0401 0.0746 0.0246 ...
    ##  $ concavity_se           : num [1:568] 0.0537 0.0186 0.0383 0.0566 0.0569 ...
    ##  $ concave points_se      : num [1:568] 0.0159 0.0134 0.0206 0.0187 0.0188 ...
    ##  $ symmetry_se            : num [1:568] 0.03 0.0139 0.0225 0.0596 0.0176 ...
    ##  $ fractal_dimension_se   : num [1:568] 0.00619 0.00353 0.00457 0.00921 0.00511 ...
    ##  $ radius_worst           : num [1:568] 25.4 25 23.6 14.9 22.5 ...
    ##  $ texture_worst          : num [1:568] 17.3 23.4 25.5 26.5 16.7 ...
    ##  $ perimeter_worst        : num [1:568] 184.6 158.8 152.5 98.9 152.2 ...
    ##  $ area_worst             : num [1:568] 2019 1956 1709 568 1575 ...
    ##  $ smoothness_worst       : num [1:568] 0.162 0.124 0.144 0.21 0.137 ...
    ##  $ compactness_worst      : num [1:568] 0.666 0.187 0.424 0.866 0.205 ...
    ##  $ concavity_worst        : num [1:568] 0.712 0.242 0.45 0.687 0.4 ...
    ##  $ concave points_worst   : num [1:568] 0.265 0.186 0.243 0.258 0.163 ...
    ##  $ symmetry_worst         : num [1:568] 0.46 0.275 0.361 0.664 0.236 ...
    ##  $ fractal_dimension_worst: num [1:568] 0.1189 0.089 0.0876 0.173 0.0768 ...

``` r
# Create columns for diagnosis as factor and numeric
dataClean <- data %>%
  mutate(diagnosis_factor = factor(diagnosis, levels = c("B", "M"), labels = c("Benign", "Malignant")),
         diagnosis_numeric = ifelse(diagnosis == "M", 1, 0))

# Drop ID column
dataClean <- dataClean %>% select(-id)

# Check for any missing values in the rest of the dataset
colSums(is.na(dataClean))
```

    ##               diagnosis             radius_mean            texture_mean 
    ##                       0                       0                       0 
    ##          perimeter_mean               area_mean         smoothness_mean 
    ##                       0                       0                       0 
    ##        compactness_mean          concavity_mean     concave points_mean 
    ##                       0                       0                       0 
    ##           symmetry_mean  fractal_dimension_mean               radius_se 
    ##                       0                       0                       0 
    ##              texture_se            perimeter_se                 area_se 
    ##                       0                       0                       0 
    ##           smoothness_se          compactness_se            concavity_se 
    ##                       0                       0                       0 
    ##       concave points_se             symmetry_se    fractal_dimension_se 
    ##                       0                       0                       0 
    ##            radius_worst           texture_worst         perimeter_worst 
    ##                       0                       0                       0 
    ##              area_worst        smoothness_worst       compactness_worst 
    ##                       0                       0                       0 
    ##         concavity_worst    concave points_worst          symmetry_worst 
    ##                       0                       0                       0 
    ## fractal_dimension_worst        diagnosis_factor       diagnosis_numeric 
    ##                       0                       0                       0

## Variables

- id :
- diagnosis :
- radius_mean :
- texture_mean :
- perimeter_mean :
- area_mean :
- smoothness_mean :
- compactness_mean :
- concavity_mean :
- concave points_mean :
- symmetry_mean :
- fractal_dimension_mean :
- radius_se :
- texture_se :
- perimeter_se :
- area_se :
- smoothness_se :
- compactness_se :
- concavity_se :
- concave points_se :
- symmetry_se :
- fractal_dimension_se :
- radius_worst :
- texture_worst :
- perimeter_worst :
- area_worst :
- smoothness_worst :
- compactness_worst :
- concavity_worst :
- concave points_worst :
- symmetry_worst :
- fractal_dimension_worst :
- diagnosis_facto :
- diagnosis_numeric :

## Results
