---
title: "Project 1"
author: "Maya Szymborski"
format: 
  html:
    toc: true
    toc-depth: 3
    toc-location: right
editor: source
execute:
  error: true
  warning: false
  message: false
---

# Introduction

yada yada yada

# Part 1: Data Processing

yada yada yada


::: {.cell}

```{.r .cell-code}
# attach packages 
library(httr)
library(jsonlite)
library(tidyverse)
library(lubridate)
```
:::


## Explore PUMS Census API

yada yada yada


::: {.cell}

```{.r .cell-code}
# establish API key

api_key <- "9565ad3d32410ea2b7caa601ff9c5c4467f789d7"

# try calling to API using example

url1 <- paste0(
  "https://api.census.gov/data/2024/acs/acs1/pums?",
  "get=SEX,PWGTP,MAR&",
  "for=state:*&",
  "SCHL=24&",
  "key=",
  api_key
)

response <- GET(url1)

response
```

::: {.cell-output .cell-output-stdout}

```
Response [https://api.census.gov/data/2024/acs/acs1/pums?get=SEX,PWGTP,MAR&for=state:*&SCHL=24&key=9565ad3d32410ea2b7caa601ff9c5c4467f789d7]
  Date: 2026-10-04 20:09
  Status: 200
  Content-Type: application/json;charset=utf-8
  Size: 1.25 MB
[["SEX","PWGTP","MAR","SCHL","state"],
["2","17","1","24","06"],
["1","58","3","24","39"],
["2","72","2","24","51"],
["2","13","1","24","12"],
["2","37","5","24","27"],
["2","47","5","24","34"],
["1","10","3","24","48"],
["1","24","5","24","17"],
["2","153","4","24","42"],
...
```


:::

```{.r .cell-code}
# convert response to text/character string & 
# parse the JSON text into an R object
data <- fromJSON(rawToChar(response$content))

# look at returned data
head(data)
```

::: {.cell-output .cell-output-stdout}

```
     [,1]  [,2]    [,3]  [,4]   [,5]   
[1,] "SEX" "PWGTP" "MAR" "SCHL" "state"
[2,] "2"   "17"    "1"   "24"   "06"   
[3,] "1"   "58"    "3"   "24"   "39"   
[4,] "2"   "72"    "2"   "24"   "51"   
[5,] "2"   "13"    "1"   "24"   "12"   
[6,] "2"   "37"    "5"   "24"   "27"   
```


:::

```{.r .cell-code}
# make the first row the column names, 
# then remove the first row
column_names <- data[1, ]
data <- data[-1, ]

# convert to a tibble
data <- as_tibble(data)

# make sure tibble has the correct column names
names(data) <- column_names

# check
head(data)
```

::: {.cell-output .cell-output-stdout}

```
# A tibble: 6 × 5
  SEX   PWGTP MAR   SCHL  state
  <chr> <chr> <chr> <chr> <chr>
1 2     17    1     24    06   
2 1     58    3     24    39   
3 2     72    2     24    51   
4 2     13    1     24    12   
5 2     37    5     24    27   
6 2     47    5     24    34   
```


:::

```{.r .cell-code}
# use process to create a helper function & then test it

query_api <- function(url) {
  
  response <- GET(url)
  
  data <- fromJSON(rawToChar(response$content))
  
  column_names <- data[1, ]
  data <- data[-1, ]
  
  data <- as_tibble(data)
  
  names(data) <- column_names
  
  return(data)
}

test <- query_api(url1)

test
```

::: {.cell-output .cell-output-stdout}

```
# A tibble: 47,486 × 5
   SEX   PWGTP MAR   SCHL  state
   <chr> <chr> <chr> <chr> <chr>
 1 2     17    1     24    06   
 2 1     58    3     24    39   
 3 2     72    2     24    51   
 4 2     13    1     24    12   
 5 2     37    5     24    27   
 6 2     47    5     24    34   
 7 1     10    3     24    48   
 8 1     24    5     24    17   
 9 2     153   4     24    42   
10 2     46    1     24    25   
# ℹ 47,476 more rows
```


:::
:::


yada yada yada

## Parsing Variable Values

yada yada yada


::: {.cell}

```{.r .cell-code}
# check 2024 PUMS variable data

url_2024 <- "https://api.census.gov/data/2024/acs/acs1/pums/variables.json"

info_2024 <- GET(url_2024)

data_2024 <- fromJSON(rawToChar(info_2024$content))

# inspect structure & variables 

str(data_2024, max.level = 2)
```

::: {.cell-output .cell-output-stdout}

```
List of 1
 $ variables:List of 524
  ..$ for        :List of 6
  ..$ in         :List of 6
  ..$ ucgid      :List of 6
  ..$ HHLANP     :List of 6
  ..$ FBATHP     :List of 6
  ..$ DRIVESP    :List of 6
  ..$ WGTP23     :List of 6
  ..$ WGTP22     :List of 6
  ..$ WGTP25     :List of 6
  ..$ WGTP24     :List of 6
  ..$ RACNH      :List of 6
  ..$ WGTP21     :List of 6
  ..$ FWATP      :List of 6
  ..$ WGTP20     :List of 6
  ..$ WGTP27     :List of 6
  ..$ WGTP26     :List of 6
  ..$ WGTP29     :List of 6
  ..$ WGTP28     :List of 6
  ..$ FBROADBNDP :List of 6
  ..$ FDRATXP    :List of 6
  ..$ FWKWNP     :List of 6
  ..$ HOTWAT     :List of 6
  ..$ FWKHP      :List of 6
  ..$ FFULP      :List of 6
  ..$ WORKSTAT   :List of 6
  ..$ FRACP      :List of 6
  ..$ FJWDP      :List of 6
  ..$ WGTP34     :List of 6
  ..$ WGTP33     :List of 6
  ..$ WGTP36     :List of 6
  ..$ PINCP      :List of 6
  ..$ WGTP35     :List of 6
  ..$ FPOBP      :List of 6
  ..$ WGTP30     :List of 6
  ..$ WGTP32     :List of 6
  ..$ STOV       :List of 6
  ..$ FMHP       :List of 6
  ..$ WGTP31     :List of 6
  ..$ RACAIAN    :List of 6
  ..$ WGTP38     :List of 6
  ..$ WGTP37     :List of 6
  ..$ WGTP39     :List of 6
  ..$ PUBCOV     :List of 6
  ..$ SRNT       :List of 6
  ..$ SEX        :List of 6
  ..$ WGTP45     :List of 6
  ..$ WGTP44     :List of 6
  ..$ WGTP47     :List of 6
  ..$ DOUT       :List of 6
  ..$ FACCESSP   :List of 6
  ..$ WGTP46     :List of 6
  ..$ WGTP41     :List of 6
  ..$ WGTP40     :List of 6
  ..$ OTHSVCEX   :List of 6
  ..$ WGTP43     :List of 6
  ..$ WGTP42     :List of 6
  ..$ RACPI      :List of 6
  ..$ INDP       :List of 6
  ..$ WGTP49     :List of 6
  ..$ WGTP48     :List of 6
  ..$ PRIVCOV    :List of 6
  ..$ SFN        :List of 6
  ..$ FINTP      :List of 6
  ..$ HUPAC      :List of 6
  ..$ SFR        :List of 6
  ..$ WGTP50     :List of 6
  ..$ FBLDP      :List of 6
  ..$ WGTP56     :List of 6
  ..$ WGTP55     :List of 6
  ..$ WGTP58     :List of 6
  ..$ WGTP57     :List of 6
  ..$ WGTP52     :List of 6
  ..$ WGTP51     :List of 6
  ..$ DEAR       :List of 6
  ..$ WGTP54     :List of 6
  ..$ DIS        :List of 6
  ..$ WGTP53     :List of 6
  ..$ ACR        :List of 6
  ..$ VACS       :List of 6
  ..$ FINSP      :List of 6
  ..$ WGTP59     :List of 6
  ..$ FMILPP     :List of 6
  ..$ ADJHSG     :List of 6
  ..$ MARHYP     :List of 6
  ..$ PAP        :List of 6
  ..$ WGTP7      :List of 6
  ..$ PWGTP30    :List of 6
  ..$ WGTP6      :List of 6
  ..$ PWGTP31    :List of 6
  ..$ WGTP5      :List of 6
  ..$ HINCP      :List of 6
  ..$ PWGTP32    :List of 6
  ..$ WKWN       :List of 6
  ..$ WGTP4      :List of 6
  ..$ PWGTP33    :List of 6
  ..$ PWGTP34    :List of 6
  ..$ PWGTP35    :List of 6
  ..$ WGTP9      :List of 6
  ..$ PWGTP36    :List of 6
  .. [list output truncated]
```


:::

```{.r .cell-code}
names(data_2024)
```

::: {.cell-output .cell-output-stdout}

```
[1] "variables"
```


:::

```{.r .cell-code}
names(data_2024$variables) [1:10]
```

::: {.cell-output .cell-output-stdout}

```
 [1] "for"     "in"      "ucgid"   "HHLANP"  "FBATHP"  "DRIVESP" "WGTP23" 
 [8] "WGTP22"  "WGTP25"  "WGTP24" 
```


:::

```{.r .cell-code}
# create function & test it

PUMS_var <- c("AGEP", "GASP", "GRPIP", "JWAP", "JWDP", "JWMNP", "PWGTP",
  "FER", "HHL", "SCH", "SCHL", "SEX",
  "REGION", "DIVISION")

get_var <- function(year) {
  
  url1 <- paste0(
    "https://api.census.gov/data/",
    year,
    "/acs/acs1/pums/variables.json"
  )
  
  response <- GET(url1)
  
  data <- fromJSON(rawToChar(response$content))
  
  # keep requested variables
  var <- PUMS_var
  if (year == 2021 | year == 2022) {
    var <- c(var, "ST")
  } else {
    var <- c(var, "STATE")
  }
  
  variables <- data$variables[var]
  
  return(variables)
}

test_2024 <- get_var(2024)

names(test_2024)
```

::: {.cell-output .cell-output-stdout}

```
 [1] "AGEP"     "GASP"     "GRPIP"    "JWAP"     "JWDP"     "JWMNP"   
 [7] "PWGTP"    "FER"      "HHL"      "SCH"      "SCHL"     "SEX"     
[13] "REGION"   "DIVISION" "STATE"   
```


:::
:::


works! so do on 2021-2023 data as well + yada yada


::: {.cell}

```{.r .cell-code}
data_2024 <- get_var(2024)
names(data_2024)
```

::: {.cell-output .cell-output-stdout}

```
 [1] "AGEP"     "GASP"     "GRPIP"    "JWAP"     "JWDP"     "JWMNP"   
 [7] "PWGTP"    "FER"      "HHL"      "SCH"      "SCHL"     "SEX"     
[13] "REGION"   "DIVISION" "STATE"   
```


:::

```{.r .cell-code}
data_2023 <- get_var(2023)
names(data_2023)
```

::: {.cell-output .cell-output-stdout}

```
 [1] "AGEP"     "GASP"     "GRPIP"    "JWAP"     "JWDP"     "JWMNP"   
 [7] "PWGTP"    "FER"      "HHL"      "SCH"      "SCHL"     "SEX"     
[13] "REGION"   "DIVISION" "STATE"   
```


:::

```{.r .cell-code}
data_2022 <- get_var(2022)
names(data_2022)
```

::: {.cell-output .cell-output-stdout}

```
 [1] "AGEP"     "GASP"     "GRPIP"    "JWAP"     "JWDP"     "JWMNP"   
 [7] "PWGTP"    "FER"      "HHL"      "SCH"      "SCHL"     "SEX"     
[13] "REGION"   "DIVISION" "ST"      
```


:::

```{.r .cell-code}
data_2021 <- get_var(2021)
names(data_2021)
```

::: {.cell-output .cell-output-stdout}

```
 [1] "AGEP"     "GASP"     "GRPIP"    "JWAP"     "JWDP"     "JWMNP"   
 [7] "PWGTP"    "FER"      "HHL"      "SCH"      "SCHL"     "SEX"     
[13] "REGION"   "DIVISION" "ST"      
```


:::

```{.r .cell-code}
# save as .rds in data folder

saveRDS(data_2024, "data/data_2024.rds")
saveRDS(data_2023, "data/data_2023.rds")
saveRDS(data_2022, "data/data_2022.rds")
saveRDS(data_2021, "data/data_2021.rds")
```
:::


## Parsing API Data

### Helper Functions


::: {.cell}

```{.r .cell-code}
# make helper function to return available geography 

# inspect for specific_geography
data_2024$STATE$values
```

::: {.cell-output .cell-output-stdout}

```
$item
$item$`47`
[1] "Tennessee/TN"

$item$`04`
[1] "Arizona/AZ"

$item$`05`
[1] "Arkansas/AR"

$item$`49`
[1] "Utah/UT"

$item$`09`
[1] "Connecticut/CT"

$item$`50`
[1] "Vermont/VT"

$item$`28`
[1] "Mississippi/MS"

$item$`37`
[1] "North Carolina/NC"

$item$`42`
[1] "Pennsylvania/PA"

$item$`45`
[1] "South Carolina/SC"

$item$`51`
[1] "Virginia/VA"

$item$`15`
[1] "Hawaii/HI"

$item$`25`
[1] "Massachusetts/MA"

$item$`72`
[1] "Puerto Rico/PR"

$item$`30`
[1] "Montana/MT"

$item$`33`
[1] "New Hampshire/NH"

$item$`35`
[1] "New Mexico/NM"

$item$`12`
[1] "Florida/FL"

$item$`48`
[1] "Texas/TX"

$item$`53`
[1] "Washington/WA"

$item$`13`
[1] "Georgia/GA"

$item$`18`
[1] "Indiana/IN"

$item$`20`
[1] "Kansas/KS"

$item$`26`
[1] "Michigan/MI"

$item$`32`
[1] "Nevada/NV"

$item$`54`
[1] "West Virginia/WV"

$item$`55`
[1] "Wisconsin/WI"

$item$`27`
[1] "Minnesota/MN"

$item$`16`
[1] "Idaho/ID"

$item$`19`
[1] "Iowa/IA"

$item$`22`
[1] "Louisiana/LA"

$item$`38`
[1] "North Dakota/ND"

$item$`01`
[1] "Alabama/AL"

$item$`02`
[1] "Alaska/AK"

$item$`06`
[1] "California/CA"

$item$`08`
[1] "Colorado/CO"

$item$`11`
[1] "District of Columbia/DC"

$item$`23`
[1] "Maine/ME"

$item$`34`
[1] "New Jersey/NJ"

$item$`39`
[1] "Ohio/OH"

$item$`10`
[1] "Delaware/DE"

$item$`56`
[1] "Wyoming/WY"

$item$`21`
[1] "Kentucky/KY"

$item$`24`
[1] "Maryland/MD"

$item$`44`
[1] "Rhode Island/RI"

$item$`46`
[1] "South Dakota/SD"

$item$`17`
[1] "Illinois/IL"

$item$`29`
[1] "Missouri/MO"

$item$`31`
[1] "Nebraska/NE"

$item$`36`
[1] "New York/NY"

$item$`40`
[1] "Oklahoma/OK"

$item$`41`
[1] "Oregon/OR"
```


:::

```{.r .cell-code}
data_2024$REGION$values
```

::: {.cell-output .cell-output-stdout}

```
$item
$item$`1`
[1] "Northeast"

$item$`9`
[1] "Puerto Rico"

$item$`3`
[1] "South"

$item$`4`
[1] "West"

$item$`2`
[1] "Midwest"
```


:::

```{.r .cell-code}
data_2024$DIVISION$values
```

::: {.cell-output .cell-output-stdout}

```
$item
$item$`7`
[1] "West South Central (South Region)"

$item$`2`
[1] "Middle Atlantic (Northeast region)"

$item$`4`
[1] "West North Central (Midwest region)"

$item$`6`
[1] "East South Central (South region)"

$item$`3`
[1] "East North Central (Midwest region)"

$item$`9`
[1] "Pacific (West region)"

$item$`0`
[1] "Puerto Rico"

$item$`5`
[1] "South Atlantic (South region)"

$item$`8`
[1] "Mountain (West region)"

$item$`1`
[1] "New England (Northeast region)"
```


:::

```{.r .cell-code}
state_values <- data_2024$STATE$values$item
state_values
```

::: {.cell-output .cell-output-stdout}

```
$`47`
[1] "Tennessee/TN"

$`04`
[1] "Arizona/AZ"

$`05`
[1] "Arkansas/AR"

$`49`
[1] "Utah/UT"

$`09`
[1] "Connecticut/CT"

$`50`
[1] "Vermont/VT"

$`28`
[1] "Mississippi/MS"

$`37`
[1] "North Carolina/NC"

$`42`
[1] "Pennsylvania/PA"

$`45`
[1] "South Carolina/SC"

$`51`
[1] "Virginia/VA"

$`15`
[1] "Hawaii/HI"

$`25`
[1] "Massachusetts/MA"

$`72`
[1] "Puerto Rico/PR"

$`30`
[1] "Montana/MT"

$`33`
[1] "New Hampshire/NH"

$`35`
[1] "New Mexico/NM"

$`12`
[1] "Florida/FL"

$`48`
[1] "Texas/TX"

$`53`
[1] "Washington/WA"

$`13`
[1] "Georgia/GA"

$`18`
[1] "Indiana/IN"

$`20`
[1] "Kansas/KS"

$`26`
[1] "Michigan/MI"

$`32`
[1] "Nevada/NV"

$`54`
[1] "West Virginia/WV"

$`55`
[1] "Wisconsin/WI"

$`27`
[1] "Minnesota/MN"

$`16`
[1] "Idaho/ID"

$`19`
[1] "Iowa/IA"

$`22`
[1] "Louisiana/LA"

$`38`
[1] "North Dakota/ND"

$`01`
[1] "Alabama/AL"

$`02`
[1] "Alaska/AK"

$`06`
[1] "California/CA"

$`08`
[1] "Colorado/CO"

$`11`
[1] "District of Columbia/DC"

$`23`
[1] "Maine/ME"

$`34`
[1] "New Jersey/NJ"

$`39`
[1] "Ohio/OH"

$`10`
[1] "Delaware/DE"

$`56`
[1] "Wyoming/WY"

$`21`
[1] "Kentucky/KY"

$`24`
[1] "Maryland/MD"

$`44`
[1] "Rhode Island/RI"

$`46`
[1] "South Dakota/SD"

$`17`
[1] "Illinois/IL"

$`29`
[1] "Missouri/MO"

$`31`
[1] "Nebraska/NE"

$`36`
[1] "New York/NY"

$`40`
[1] "Oklahoma/OK"

$`41`
[1] "Oregon/OR"
```


:::

```{.r .cell-code}
names(state_values)
```

::: {.cell-output .cell-output-stdout}

```
 [1] "47" "04" "05" "49" "09" "50" "28" "37" "42" "45" "51" "15" "25" "72" "30"
[16] "33" "35" "12" "48" "53" "13" "18" "20" "26" "32" "54" "55" "27" "16" "19"
[31] "22" "38" "01" "02" "06" "08" "11" "23" "34" "39" "10" "56" "21" "24" "44"
[46] "46" "17" "29" "31" "36" "40" "41"
```


:::

```{.r .cell-code}
unlist(state_values) # turn nested list into a simplified list
```

::: {.cell-output .cell-output-stdout}

```
                       47                        04                        05 
           "Tennessee/TN"              "Arizona/AZ"             "Arkansas/AR" 
                       49                        09                        50 
                "Utah/UT"          "Connecticut/CT"              "Vermont/VT" 
                       28                        37                        42 
         "Mississippi/MS"       "North Carolina/NC"         "Pennsylvania/PA" 
                       45                        51                        15 
      "South Carolina/SC"             "Virginia/VA"               "Hawaii/HI" 
                       25                        72                        30 
       "Massachusetts/MA"          "Puerto Rico/PR"              "Montana/MT" 
                       33                        35                        12 
       "New Hampshire/NH"           "New Mexico/NM"              "Florida/FL" 
                       48                        53                        13 
               "Texas/TX"           "Washington/WA"              "Georgia/GA" 
                       18                        20                        26 
             "Indiana/IN"               "Kansas/KS"             "Michigan/MI" 
                       32                        54                        55 
              "Nevada/NV"        "West Virginia/WV"            "Wisconsin/WI" 
                       27                        16                        19 
           "Minnesota/MN"                "Idaho/ID"                 "Iowa/IA" 
                       22                        38                        01 
           "Louisiana/LA"         "North Dakota/ND"              "Alabama/AL" 
                       02                        06                        08 
              "Alaska/AK"           "California/CA"             "Colorado/CO" 
                       11                        23                        34 
"District of Columbia/DC"                "Maine/ME"           "New Jersey/NJ" 
                       39                        10                        56 
                "Ohio/OH"             "Delaware/DE"              "Wyoming/WY" 
                       21                        24                        44 
            "Kentucky/KY"             "Maryland/MD"         "Rhode Island/RI" 
                       46                        17                        29 
        "South Dakota/SD"             "Illinois/IL"             "Missouri/MO" 
                       31                        36                        40 
            "Nebraska/NE"             "New York/NY"             "Oklahoma/OK" 
                       41 
              "Oregon/OR" 
```


:::

```{.r .cell-code}
get_geo <- function(year, geography) {
  
  # load variable values for selected year
  var_data <- readRDS(
    paste0(
      "data/data_",
      year,
      ".rds"
    )
  )
  
  # use ST for 2021/2022 and STATE for 2023/2024
  if (geography == "State" && year %in% c(2021, 2022)) {
    geography <- "ST"
  } else {
    geography <- toupper(geography)
  }
  
  # get selected geography & match them
  geo_data <- var_data[[geography]] 
  
  # return geography codes and names
  geo_val <- unlist(geo_data$values$item)
  
  # remove Puerto Rico using `!grepl` which means "does not contain"
  geo_val <- geo_val[!grepl("Puerto Rico", geo_val)
                     ]
  
  return(geo_val)
}

# test helper function
get_geo(2024, "State")
```

::: {.cell-output .cell-output-stdout}

```
                       47                        04                        05 
           "Tennessee/TN"              "Arizona/AZ"             "Arkansas/AR" 
                       49                        09                        50 
                "Utah/UT"          "Connecticut/CT"              "Vermont/VT" 
                       28                        37                        42 
         "Mississippi/MS"       "North Carolina/NC"         "Pennsylvania/PA" 
                       45                        51                        15 
      "South Carolina/SC"             "Virginia/VA"               "Hawaii/HI" 
                       25                        30                        33 
       "Massachusetts/MA"              "Montana/MT"        "New Hampshire/NH" 
                       35                        12                        48 
          "New Mexico/NM"              "Florida/FL"                "Texas/TX" 
                       53                        13                        18 
          "Washington/WA"              "Georgia/GA"              "Indiana/IN" 
                       20                        26                        32 
              "Kansas/KS"             "Michigan/MI"               "Nevada/NV" 
                       54                        55                        27 
       "West Virginia/WV"            "Wisconsin/WI"            "Minnesota/MN" 
                       16                        19                        22 
               "Idaho/ID"                 "Iowa/IA"            "Louisiana/LA" 
                       38                        01                        02 
        "North Dakota/ND"              "Alabama/AL"               "Alaska/AK" 
                       06                        08                        11 
          "California/CA"             "Colorado/CO" "District of Columbia/DC" 
                       23                        34                        39 
               "Maine/ME"           "New Jersey/NJ"                 "Ohio/OH" 
                       10                        56                        21 
            "Delaware/DE"              "Wyoming/WY"             "Kentucky/KY" 
                       24                        44                        46 
            "Maryland/MD"         "Rhode Island/RI"         "South Dakota/SD" 
                       17                        29                        31 
            "Illinois/IL"             "Missouri/MO"             "Nebraska/NE" 
                       36                        40                        41 
            "New York/NY"             "Oklahoma/OK"               "Oregon/OR" 
```


:::

```{.r .cell-code}
get_geo(2023, "Region")
```

::: {.cell-output .cell-output-stdout}

```
          1           3           4           2 
"Northeast"     "South"      "West"   "Midwest" 
```


:::

```{.r .cell-code}
get_geo(2022, "Division")
```

::: {.cell-output .cell-output-stdout}

```
                                    7                                     2 
  "West South Central (South Region)"  "Middle Atlantic (Northeast region)" 
                                    4                                     6 
"West North Central (Midwest region)"   "East South Central (South region)" 
                                    3                                     9 
"East North Central (Midwest region)"               "Pacific (West region)" 
                                    5                                     8 
      "South Atlantic (South region)"              "Mountain (West region)" 
                                    1 
     "New England (Northeast region)" 
```


:::
:::



::: {.cell}

```{.r .cell-code}
# make helper function that parses the time strings to return midpoint as time value

parse_time <- function(x, time_values) {
  
  # convert values to character to match metadata names
  x <- as.character(x)
  
  # return missing time values as NA
  x[x == "0"] <- NA
  
  # add leading 0's so codes match Census metadata
  x <- ifelse(
    is.na(x), 
    NA, 
    sprintf("%03d", as.numeric(x)
            )
    )
  
  # get time interval for each code
  intervals <- unname(time_values[x])
  
  # extract beginning and ending times
  start <- sub(" to .*", "", intervals)
  end <- sub(".* to ", "", intervals)
  
  # change Census AM/PM to standard AM/PM
  start <- gsub("a\\.m\\.", "AM", start)
  start <- gsub("p\\.m\\.", "PM", start)
  end <- gsub("a\\.m\\.", "AM", end)
  end <- gsub("p\\.m\\.", "PM", end)
  
  # convert times to POSIXct
  start_time <- strptime(start, format = "%I:%M %p")
  end_time <- strptime(end, format = "%I:%M %p")
  
  # calculate midpoint of each interval
  midpoint <- start_time + (end_time - start_time) / 2
  
  # return only time of day
  midpoint <- hms::as_hms(midpoint)
  
  return(midpoint)
}
```
:::



::: {.cell}

```{.r .cell-code}
# JWAP metadata
url <- paste0(
  "https://api.census.gov/data/2024/acs/acs1/pums/variables/JWAP.json"
)

response <- GET(url)

fromJSON(rawToChar(response$content))
```

::: {.cell-output .cell-output-stdout}

```
$name
[1] "JWAP"

$label
[1] "Time of arrival at work - hour and minute"

$predicateType
[1] "int"

$group
[1] "N/A"

$limit
[1] 0

$`suggested-weight`
[1] "PWGTP"

$values
$values$item
$values$item$`122`
[1] "10:20 a.m. to 10:24 a.m."

$values$item$`258`
[1] "9:40 p.m. to 9:44 p.m."

$values$item$`260`
[1] "9:50 p.m. to 9:54 p.m."

$values$item$`261`
[1] "9:55 p.m. to 9:59 p.m."

$values$item$`142`
[1] "12:00 p.m. to 12:04 p.m."

$values$item$`146`
[1] "12:20 p.m. to 12:24 p.m."

$values$item$`274`
[1] "11:00 p.m. to 11:04 p.m."

$values$item$`163`
[1] "1:45 p.m. to 1:49 p.m."

$values$item$`165`
[1] "1:55 p.m. to 1:59 p.m."

$values$item$`048`
[1] "4:10 a.m. to 4:14 a.m."

$values$item$`171`
[1] "2:25 p.m. to 2:29 p.m."

$values$item$`058`
[1] "5:00 a.m. to 5:04 a.m."

$values$item$`184`
[1] "3:30 p.m. to 3:34 p.m."

$values$item$`189`
[1] "3:55 p.m. to 3:59 p.m."

$values$item$`192`
[1] "4:10 p.m. to 4:14 p.m."

$values$item$`076`
[1] "6:30 a.m. to 6:34 a.m."

$values$item$`198`
[1] "4:40 p.m. to 4:44 p.m."

$values$item$`091`
[1] "7:45 a.m. to 7:49 a.m."

$values$item$`094`
[1] "8:00 a.m. to 8:04 a.m."

$values$item$`210`
[1] "5:40 p.m. to 5:44 p.m."

$values$item$`218`
[1] "6:20 p.m. to 6:24 p.m."

$values$item$`220`
[1] "6:30 p.m. to 6:34 p.m."

$values$item$`221`
[1] "6:35 p.m. to 6:39 p.m."

$values$item$`222`
[1] "6:40 p.m. to 6:44 p.m."

$values$item$`224`
[1] "6:50 p.m. to 6:54 p.m."

$values$item$`227`
[1] "7:05 p.m. to 7:09 p.m."

$values$item$`228`
[1] "7:10 p.m. to 7:14 p.m."

$values$item$`234`
[1] "7:40 p.m. to 7:44 p.m."

$values$item$`235`
[1] "7:45 p.m. to 7:49 p.m."

$values$item$`121`
[1] "10:15 a.m. to 10:19 a.m."

$values$item$`124`
[1] "10:30 a.m. to 10:34 a.m."

$values$item$`006`
[1] "12:25 a.m. to 12:29 a.m."

$values$item$`008`
[1] "12:40 a.m. to 12:44 a.m."

$values$item$`009`
[1] "12:45 a.m. to 12:49 a.m."

$values$item$`251`
[1] "9:05 p.m. to 9:09 p.m."

$values$item$`011`
[1] "1:00 a.m. to 1:04 a.m."

$values$item$`256`
[1] "9:30 p.m. to 9:34 p.m."

$values$item$`137`
[1] "11:35 a.m. to 11:39 a.m."

$values$item$`017`
[1] "1:30 a.m. to 1:34 a.m."

$values$item$`259`
[1] "9:45 p.m. to 9:49 p.m."

$values$item$`020`
[1] "1:45 a.m. to 1:49 a.m."

$values$item$`262`
[1] "10:00 p.m. to 10:04 p.m."

$values$item$`270`
[1] "10:40 p.m. to 10:44 p.m."

$values$item$`271`
[1] "10:45 p.m. to 10:49 p.m."

$values$item$`031`
[1] "2:45 a.m. to 2:49 a.m."

$values$item$`276`
[1] "11:10 p.m. to 11:14 p.m."

$values$item$`156`
[1] "1:10 p.m. to 1:14 p.m."

$values$item$`039`
[1] "3:25 a.m. to 3:29 a.m."

$values$item$`284`
[1] "11:50 p.m. to 11:54 p.m."

$values$item$`164`
[1] "1:50 p.m. to 1:54 p.m."

$values$item$`173`
[1] "2:35 p.m. to 2:39 p.m."

$values$item$`053`
[1] "4:35 a.m. to 4:39 a.m."

$values$item$`065`
[1] "5:35 a.m. to 5:39 a.m."

$values$item$`075`
[1] "6:25 a.m. to 6:29 a.m."

$values$item$`197`
[1] "4:35 p.m. to 4:39 p.m."

$values$item$`085`
[1] "7:15 a.m. to 7:19 a.m."

$values$item$`090`
[1] "7:40 a.m. to 7:44 a.m."

$values$item$`202`
[1] "5:00 p.m. to 5:04 p.m."

$values$item$`101`
[1] "8:35 a.m. to 8:39 a.m."

$values$item$`107`
[1] "9:05 a.m. to 9:09 a.m."

$values$item$`230`
[1] "7:20 p.m. to 7:24 p.m."

$values$item$`233`
[1] "7:35 p.m. to 7:39 p.m."

$values$item$`117`
[1] "9:55 a.m. to 9:59 a.m."

$values$item$`118`
[1] "10:00 a.m. to 10:04 a.m."

$values$item$`002`
[1] "12:05 a.m. to 12:09 a.m."

$values$item$`004`
[1] "12:15 a.m. to 12:19 a.m."

$values$item$`246`
[1] "8:40 p.m. to 8:44 p.m."

$values$item$`249`
[1] "8:55 p.m. to 8:59 p.m."

$values$item$`129`
[1] "10:55 a.m. to 10:59 a.m."

$values$item$`132`
[1] "11:10 a.m. to 11:14 a.m."

$values$item$`015`
[1] "1:20 a.m. to 1:24 a.m."

$values$item$`257`
[1] "9:35 p.m. to 9:39 p.m."

$values$item$`138`
[1] "11:40 a.m. to 11:44 a.m."

$values$item$`144`
[1] "12:10 p.m. to 12:14 p.m."

$values$item$`148`
[1] "12:30 p.m. to 12:34 p.m."

$values$item$`150`
[1] "12:40 p.m. to 12:44 p.m."

$values$item$`152`
[1] "12:50 p.m. to 12:54 p.m."

$values$item$`273`
[1] "10:55 p.m. to 10:59 p.m."

$values$item$`034`
[1] "3:00 a.m. to 3:04 a.m."

$values$item$`035`
[1] "3:05 a.m. to 3:09 a.m."

$values$item$`277`
[1] "11:15 p.m. to 11:19 p.m."

$values$item$`042`
[1] "3:40 a.m. to 3:44 a.m."

$values$item$`044`
[1] "3:50 a.m. to 3:54 a.m."

$values$item$`166`
[1] "2:00 p.m. to 2:04 p.m."

$values$item$`050`
[1] "4:20 a.m. to 4:24 a.m."

$values$item$`052`
[1] "4:30 a.m. to 4:34 a.m."

$values$item$`055`
[1] "4:45 a.m. to 4:49 a.m."

$values$item$`181`
[1] "3:15 p.m. to 3:19 p.m."

$values$item$`183`
[1] "3:25 p.m. to 3:29 p.m."

$values$item$`069`
[1] "5:55 a.m. to 5:59 a.m."

$values$item$`071`
[1] "6:05 a.m. to 6:09 a.m."

$values$item$`077`
[1] "6:35 a.m. to 6:39 a.m."

$values$item$`079`
[1] "6:45 a.m. to 6:49 a.m."

$values$item$`082`
[1] "7:00 a.m. to 7:04 a.m."

$values$item$`089`
[1] "7:35 a.m. to 7:39 a.m."

$values$item$`092`
[1] "7:50 a.m. to 7:54 a.m."

$values$item$`093`
[1] "7:55 a.m. to 7:59 a.m."

$values$item$`097`
[1] "8:15 a.m. to 8:19 a.m."

$values$item$`205`
[1] "5:15 p.m. to 5:19 p.m."

$values$item$`219`
[1] "6:25 p.m. to 6:29 p.m."

$values$item$`239`
[1] "8:05 p.m. to 8:09 p.m."

$values$item$`244`
[1] "8:30 p.m. to 8:34 p.m."

$values$item$`125`
[1] "10:35 a.m. to 10:39 a.m."

$values$item$`254`
[1] "9:20 p.m. to 9:24 p.m."

$values$item$`013`
[1] "1:10 a.m. to 1:14 a.m."

$values$item$`014`
[1] "1:15 a.m. to 1:19 a.m."

$values$item$`019`
[1] "1:40 a.m. to 1:44 a.m."

$values$item$`140`
[1] "11:50 a.m. to 11:54 a.m."

$values$item$`022`
[1] "2:00 a.m. to 2:04 a.m."

$values$item$`265`
[1] "10:15 p.m. to 10:19 p.m."

$values$item$`272`
[1] "10:50 p.m. to 10:54 p.m."

$values$item$`033`
[1] "2:55 a.m. to 2:59 a.m."

$values$item$`037`
[1] "3:15 a.m. to 3:19 a.m."

$values$item$`041`
[1] "3:35 a.m. to 3:39 a.m."

$values$item$`172`
[1] "2:30 p.m. to 2:34 p.m."

$values$item$`178`
[1] "3:00 p.m. to 3:04 p.m."

$values$item$`059`
[1] "5:05 a.m. to 5:09 a.m."

$values$item$`182`
[1] "3:20 p.m. to 3:24 p.m."

$values$item$`185`
[1] "3:35 p.m. to 3:39 p.m."

$values$item$`187`
[1] "3:45 p.m. to 3:49 p.m."

$values$item$`067`
[1] "5:45 a.m. to 5:49 a.m."

$values$item$`068`
[1] "5:50 a.m. to 5:54 a.m."

$values$item$`074`
[1] "6:20 a.m. to 6:24 a.m."

$values$item$`199`
[1] "4:45 p.m. to 4:49 p.m."

$values$item$`080`
[1] "6:50 a.m. to 6:54 a.m."

$values$item$`084`
[1] "7:10 a.m. to 7:14 a.m."

$values$item$`088`
[1] "7:30 a.m. to 7:34 a.m."

$values$item$`095`
[1] "8:05 a.m. to 8:09 a.m."

$values$item$`206`
[1] "5:20 p.m. to 5:24 p.m."

$values$item$`208`
[1] "5:30 p.m. to 5:34 p.m."

$values$item$`209`
[1] "5:35 p.m. to 5:39 p.m."

$values$item$`100`
[1] "8:30 a.m. to 8:34 a.m."

$values$item$`114`
[1] "9:40 a.m. to 9:44 a.m."

$values$item$`001`
[1] "12:00 a.m. to 12:04 a.m."

$values$item$`243`
[1] "8:25 p.m. to 8:29 p.m."

$values$item$`005`
[1] "12:20 a.m. to 12:24 a.m."

$values$item$`007`
[1] "12:30 a.m. to 12:39 a.m."

$values$item$`135`
[1] "11:25 a.m. to 11:29 a.m."

$values$item$`143`
[1] "12:05 p.m. to 12:09 p.m."

$values$item$`023`
[1] "2:05 a.m. to 2:09 a.m."

$values$item$`025`
[1] "2:15 a.m. to 2:19 a.m."

$values$item$`268`
[1] "10:30 p.m. to 10:34 p.m."

$values$item$`269`
[1] "10:35 p.m. to 10:39 p.m."

$values$item$`149`
[1] "12:35 p.m. to 12:39 p.m."

$values$item$`029`
[1] "2:35 a.m. to 2:39 a.m."

$values$item$`036`
[1] "3:10 a.m. to 3:14 a.m."

$values$item$`157`
[1] "1:15 p.m. to 1:19 p.m."

$values$item$`158`
[1] "1:20 p.m. to 1:24 p.m."

$values$item$`279`
[1] "11:25 p.m. to 11:29 p.m."

$values$item$`160`
[1] "1:30 p.m. to 1:34 p.m."

$values$item$`043`
[1] "3:45 a.m. to 3:49 a.m."

$values$item$`046`
[1] "4:00 a.m. to 4:04 a.m."

$values$item$`170`
[1] "2:20 p.m. to 2:24 p.m."

$values$item$`051`
[1] "4:25 a.m. to 4:29 a.m."

$values$item$`174`
[1] "2:40 p.m. to 2:44 p.m."

$values$item$`056`
[1] "4:50 a.m. to 4:54 a.m."

$values$item$`179`
[1] "3:05 p.m. to 3:09 p.m."

$values$item$`073`
[1] "6:15 a.m. to 6:19 a.m."

$values$item$`194`
[1] "4:20 p.m. to 4:24 p.m."

$values$item$`196`
[1] "4:30 p.m. to 4:34 p.m."

$values$item$`081`
[1] "6:55 a.m. to 6:59 a.m."

$values$item$`098`
[1] "8:20 a.m. to 8:24 a.m."

$values$item$`211`
[1] "5:45 p.m. to 5:49 p.m."

$values$item$`212`
[1] "5:50 p.m. to 5:54 p.m."

$values$item$`214`
[1] "6:00 p.m. to 6:04 p.m."

$values$item$`103`
[1] "8:45 a.m. to 8:49 a.m."

$values$item$`106`
[1] "9:00 a.m. to 9:04 a.m."

$values$item$`111`
[1] "9:25 a.m. to 9:29 a.m."

$values$item$`119`
[1] "10:05 a.m. to 10:09 a.m."

$values$item$`241`
[1] "8:15 p.m. to 8:19 p.m."

$values$item$`123`
[1] "10:25 a.m. to 10:29 a.m."

$values$item$`126`
[1] "10:40 a.m. to 10:44 a.m."

$values$item$`134`
[1] "11:20 a.m. to 11:24 a.m."

$values$item$`016`
[1] "1:25 a.m. to 1:29 a.m."

$values$item$`018`
[1] "1:35 a.m. to 1:39 a.m."

$values$item$`139`
[1] "11:45 a.m. to 11:49 a.m."

$values$item$`141`
[1] "11:55 a.m. to 11:59 a.m."

$values$item$`021`
[1] "1:50 a.m. to 1:59 a.m."

$values$item$`264`
[1] "10:10 p.m. to 10:14 p.m."

$values$item$`266`
[1] "10:20 p.m. to 10:24 p.m."

$values$item$`028`
[1] "2:30 a.m. to 2:34 a.m."

$values$item$`151`
[1] "12:45 p.m. to 12:49 p.m."

$values$item$`153`
[1] "12:55 p.m. to 12:59 p.m."

$values$item$`169`
[1] "2:15 p.m. to 2:19 p.m."

$values$item$`054`
[1] "4:40 a.m. to 4:44 a.m."

$values$item$`180`
[1] "3:10 p.m. to 3:14 p.m."

$values$item$`060`
[1] "5:10 a.m. to 5:14 a.m."

$values$item$`064`
[1] "5:30 a.m. to 5:34 a.m."

$values$item$`070`
[1] "6:00 a.m. to 6:04 a.m."

$values$item$`078`
[1] "6:40 a.m. to 6:44 a.m."

$values$item$`087`
[1] "7:25 a.m. to 7:29 a.m."

$values$item$`203`
[1] "5:05 p.m. to 5:09 p.m."

$values$item$`204`
[1] "5:10 p.m. to 5:14 p.m."

$values$item$`207`
[1] "5:25 p.m. to 5:29 p.m."

$values$item$`215`
[1] "6:05 p.m. to 6:09 p.m."

$values$item$`216`
[1] "6:10 p.m. to 6:14 p.m."

$values$item$`217`
[1] "6:15 p.m. to 6:19 p.m."

$values$item$`223`
[1] "6:45 p.m. to 6:49 p.m."

$values$item$`225`
[1] "6:55 p.m. to 6:59 p.m."

$values$item$`105`
[1] "8:55 a.m. to 8:59 a.m."

$values$item$`232`
[1] "7:30 p.m. to 7:34 p.m."

$values$item$`116`
[1] "9:50 a.m. to 9:54 a.m."

$values$item$`0`
[1] "N/A (not a worker; worker who worked from home)"

$values$item$`003`
[1] "12:10 a.m. to 12:14 a.m."

$values$item$`247`
[1] "8:45 p.m. to 8:49 p.m."

$values$item$`248`
[1] "8:50 p.m. to 8:54 p.m."

$values$item$`128`
[1] "10:50 a.m. to 10:54 a.m."

$values$item$`010`
[1] "12:50 a.m. to 12:59 a.m."

$values$item$`252`
[1] "9:10 p.m. to 9:14 p.m."

$values$item$`253`
[1] "9:15 p.m. to 9:19 p.m."

$values$item$`133`
[1] "11:15 a.m. to 11:19 a.m."

$values$item$`255`
[1] "9:25 p.m. to 9:29 p.m."

$values$item$`136`
[1] "11:30 a.m. to 11:34 a.m."

$values$item$`263`
[1] "10:05 p.m. to 10:09 p.m."

$values$item$`267`
[1] "10:25 p.m. to 10:29 p.m."

$values$item$`147`
[1] "12:25 p.m. to 12:29 p.m."

$values$item$`154`
[1] "1:00 p.m. to 1:04 p.m."

$values$item$`155`
[1] "1:05 p.m. to 1:09 p.m."

$values$item$`278`
[1] "11:20 p.m. to 11:24 p.m."

$values$item$`038`
[1] "3:20 a.m. to 3:24 a.m."

$values$item$`280`
[1] "11:30 p.m. to 11:34 p.m."

$values$item$`281`
[1] "11:35 p.m. to 11:39 p.m."

$values$item$`161`
[1] "1:35 p.m. to 1:39 p.m."

$values$item$`282`
[1] "11:40 p.m. to 11:44 p.m."

$values$item$`162`
[1] "1:40 p.m. to 1:44 p.m."

$values$item$`167`
[1] "2:05 p.m. to 2:09 p.m."

$values$item$`049`
[1] "4:15 a.m. to 4:19 a.m."

$values$item$`175`
[1] "2:45 p.m. to 2:49 p.m."

$values$item$`177`
[1] "2:55 p.m. to 2:59 p.m."

$values$item$`057`
[1] "4:55 a.m. to 4:59 a.m."

$values$item$`061`
[1] "5:15 a.m. to 5:19 a.m."

$values$item$`062`
[1] "5:20 a.m. to 5:24 a.m."

$values$item$`186`
[1] "3:40 p.m. to 3:44 p.m."

$values$item$`190`
[1] "4:00 p.m. to 4:04 p.m."

$values$item$`191`
[1] "4:05 p.m. to 4:09 p.m."

$values$item$`072`
[1] "6:10 a.m. to 6:14 a.m."

$values$item$`193`
[1] "4:15 p.m. to 4:19 p.m."

$values$item$`083`
[1] "7:05 a.m. to 7:09 a.m."

$values$item$`200`
[1] "4:50 p.m. to 4:54 p.m."

$values$item$`201`
[1] "4:55 p.m. to 4:59 p.m."

$values$item$`104`
[1] "8:50 a.m. to 8:54 a.m."

$values$item$`226`
[1] "7:00 p.m. to 7:04 p.m."

$values$item$`108`
[1] "9:10 a.m. to 9:14 a.m."

$values$item$`110`
[1] "9:20 a.m. to 9:24 a.m."

$values$item$`231`
[1] "7:25 p.m. to 7:29 p.m."

$values$item$`112`
[1] "9:30 a.m. to 9:34 a.m."

$values$item$`113`
[1] "9:35 a.m. to 9:39 a.m."

$values$item$`115`
[1] "9:45 a.m. to 9:49 a.m."

$values$item$`236`
[1] "7:50 p.m. to 7:54 p.m."

$values$item$`237`
[1] "7:55 p.m. to 7:59 p.m."

$values$item$`238`
[1] "8:00 p.m. to 8:04 p.m."

$values$item$`240`
[1] "8:10 p.m. to 8:14 p.m."

$values$item$`120`
[1] "10:10 a.m. to 10:14 a.m."

$values$item$`242`
[1] "8:20 p.m. to 8:24 p.m."

$values$item$`245`
[1] "8:35 p.m. to 8:39 p.m."

$values$item$`127`
[1] "10:45 a.m. to 10:49 a.m."

$values$item$`250`
[1] "9:00 p.m. to 9:04 p.m."

$values$item$`130`
[1] "11:00 a.m. to 11:04 a.m."

$values$item$`131`
[1] "11:05 a.m. to 11:09 a.m."

$values$item$`012`
[1] "1:05 a.m. to 1:09 a.m."

$values$item$`024`
[1] "2:10 a.m. to 2:14 a.m."

$values$item$`145`
[1] "12:15 p.m. to 12:19 p.m."

$values$item$`026`
[1] "2:20 a.m. to 2:24 a.m."

$values$item$`027`
[1] "2:25 a.m. to 2:29 a.m."

$values$item$`030`
[1] "2:40 a.m. to 2:44 a.m."

$values$item$`032`
[1] "2:50 a.m. to 2:54 a.m."

$values$item$`275`
[1] "11:05 p.m. to 11:09 p.m."

$values$item$`159`
[1] "1:25 p.m. to 1:29 p.m."

$values$item$`040`
[1] "3:30 a.m. to 3:34 a.m."

$values$item$`283`
[1] "11:45 p.m. to 11:49 p.m."

$values$item$`285`
[1] "11:55 p.m. to 11:59 p.m."

$values$item$`045`
[1] "3:55 a.m. to 3:59 a.m."

$values$item$`047`
[1] "4:05 a.m. to 4:09 a.m."

$values$item$`168`
[1] "2:10 p.m. to 2:14 p.m."

$values$item$`176`
[1] "2:50 p.m. to 2:54 p.m."

$values$item$`063`
[1] "5:25 a.m. to 5:29 a.m."

$values$item$`066`
[1] "5:40 a.m. to 5:44 a.m."

$values$item$`188`
[1] "3:50 p.m. to 3:54 p.m."

$values$item$`195`
[1] "4:25 p.m. to 4:29 p.m."

$values$item$`086`
[1] "7:20 a.m. to 7:24 a.m."

$values$item$`096`
[1] "8:10 a.m. to 8:14 a.m."

$values$item$`099`
[1] "8:25 a.m. to 8:29 a.m."

$values$item$`213`
[1] "5:55 p.m. to 5:59 p.m."

$values$item$`102`
[1] "8:40 a.m. to 8:44 a.m."

$values$item$`229`
[1] "7:15 p.m. to 7:19 p.m."

$values$item$`109`
[1] "9:15 a.m. to 9:19 a.m."
```


:::

```{.r .cell-code}
jwap_metadata <- fromJSON(rawToChar(response$content))

jwap_values <- unlist(jwap_metadata$values$item)

head(jwap_values)
```

::: {.cell-output .cell-output-stdout}

```
                       122                        258 
"10:20 a.m. to 10:24 a.m."   "9:40 p.m. to 9:44 p.m." 
                       260                        261 
  "9:50 p.m. to 9:54 p.m."   "9:55 p.m. to 9:59 p.m." 
                       142                        146 
"12:00 p.m. to 12:04 p.m." "12:20 p.m. to 12:24 p.m." 
```


:::

```{.r .cell-code}
arrive_test <- query_census(
  numeric_vars = c("JWAP", "PWGTP"),
  api_key = api_key
)
```

::: {.cell-output .cell-output-error}

```
Error in `query_census()`:
! could not find function "query_census"
```


:::

```{.r .cell-code}
head(arrive_test$JWAP)
```

::: {.cell-output .cell-output-error}

```
Error:
! object 'arrive_test' not found
```


:::

```{.r .cell-code}
jwap_time <- parse_time(
  arrive_test$JWAP,
  jwap_values
)
```

::: {.cell-output .cell-output-error}

```
Error:
! object 'arrive_test' not found
```


:::

```{.r .cell-code}
head(jwap_time)
```

::: {.cell-output .cell-output-error}

```
Error:
! object 'jwap_time' not found
```


:::
:::



::: {.cell}

```{.r .cell-code}
# JWDP metadata 

url <- paste0(
  "https://api.census.gov/data/2024/acs/acs1/pums/variables/JWDP.json"
)

response_jwdp <- GET(url)

jwdp_metadata <- fromJSON(rawToChar(response_jwdp$content))

jwdp_values <- unlist(jwdp_metadata$values$item)

head(jwdp_values)
```

::: {.cell-output .cell-output-stdout}

```
                     014                      015                      017 
"4:10 a.m. to 4:19 a.m." "4:20 a.m. to 4:29 a.m." "4:40 a.m. to 4:49 a.m." 
                     022                      035                      046 
"5:15 a.m. to 5:19 a.m." "6:20 a.m. to 6:24 a.m." "7:15 a.m. to 7:19 a.m." 
```


:::

```{.r .cell-code}
depart_test <- query_census(
  numeric_vars = c("JWDP", "PWGTP"),
  api_key = api_key
)
```

::: {.cell-output .cell-output-error}

```
Error in `query_census()`:
! could not find function "query_census"
```


:::

```{.r .cell-code}
jwdp_time <- parse_time(
  depart_test$JWDP,
  jwdp_values
)
```

::: {.cell-output .cell-output-error}

```
Error:
! object 'depart_test' not found
```


:::

```{.r .cell-code}
head(jwdp_time)
```

::: {.cell-output .cell-output-error}

```
Error:
! object 'jwdp_time' not found
```


:::
:::


### Main Function

::: {.cell}

```{.r .cell-code}
# create main parsing function 

# first set up description and defaults 

query_census <- function(
    year = 2024,
    numeric_vars = c("AGEP", "PWGTP"),
    categorical_vars = "SEX",
    geography = "State",
    specific_geography = NULL,
    api_key
) 
  {
  # check for valid year
  if (!year %in% c(2021, 2022, 2023, 2024)) {
    stop("year must be in 2021, 2022, 2023, or 2024")
  }
  
  # specify numeric variables that can be returned
  allowed_num <- c("AGEP", "GASP", "GRPIP", "JWAP", "JWDP", "JWMNP", "PWGTP")
  
  # check
  if (!all(numeric_vars %in% allowed_num)) {
    stop("Invalid numeric variable. Choose from AGEP, GASP, GRPIP, JWAP, JWDP, JWMNP, and PWGTP.")
  }
  
  # make sure PWGTP is always returned 
  if (!"PWGTP" %in% numeric_vars) {
    stop("PWGTP must be included.")
  }
  
  # make sure another numeric variable is returned 
  if (length(numeric_vars) < 2) {
    stop("numeric_vars must included PWGTP and at least one other numeric variable.")
  }
  
  # specify categorical variables that can be returned 
  allowed_cat <- c("FER", "HHL", "SCH", "SCHL", "SEX")
  
  # check
  if (!all(categorical_vars %in% allowed_cat)) {
    stop("Invalid categorical variable. Choose from FER, HHL, SCH, SCHL, and SEX.")
  }
  
  # make sure at least one categorical variable is returned 
  if (length(categorical_vars) < 1) {
    stop("At least one categorical variable must be included.")
  }
  
  # specify geography levels that can be returned 
  allowed_geo <- c("Region", "Division", "State")
  
  # make sure only one level can be returned 
  if (length(geography) != 1 || !geography %in% allowed_geo) {
    stop("geography must be either Region, Division, or State.")
  }
  
  # specify the available geography that can be subset 
  geo_values <- get_geo(year, geography)
  
  # if no specific_geography, use North Carolina as default 
  if(is.null(specific_geography)) {
    specific_geography <- "North Carolina"
  }
  
  # return error if user requests Puerto Rico
  if (grepl("Puerto Rico", specific_geography, ignore.case = TRUE)) {
    stop("Puerto Rico is not included in this function.")
  }

  # find Census code for selected geography
  if (specific_geography == "all") {
    geo_code <- NULL
  } else {
    geo_code <- names(
    geo_values[grepl(
      specific_geography,
      geo_values,
      fixed = TRUE
    )]
  )
  }
  
  # make sure valid specific geography is returned 
  if (length(geo_code) == 0 && specific_geography != "all") {
    stop("Invalid specific_geography.")
  }
  
  # create API query
  get_variables <- paste(c(numeric_vars, categorical_vars),
                         collapse = ","
                         )
  
  if (specific_geography == "all") {
    for_part <- paste0(
      tolower(geography),
      ":*"
    )
  } else {
    for_part <- paste0(
      tolower(geography),
      ":",
      geo_code
    )
  }
  
  # create API URL
  url2 <- paste0(
    "https://api.census.gov/data/",
    year,
    "/acs/acs1/pums?",
    "get=",
    get_variables,
    "&for=",
    for_part,
    "&key=",
    api_key
  )
  
  # query API
  data <- query_api(url2)
  
  # convert numeric variables
  for (var in numeric_vars) {
    if (var %in% c("JWAP", "JWDP")) {
      
      # get metadata
      time_url <- paste0(
        "https://api.census.gov/data/",
      year,
      "/acs/acs1/pums/variables/",
      var,
      ".json"
      )
      
      time_response <- GET(time_url)
      
      time_metadata <- fromJSON(
        rawToChar(time_response$content)
        )
      
      time_values <- unlist(
        time_metadata$values$item
      )
      
      # convert Census time codes to midpoint times
      data[[var]] <- parse_time(
        data[[var]],
        time_values
      )
    } else {
      
      # convert regular numeric variables to numeric
      data[[var]] <- as.numeric(data[[var]])
    }
  }
  
  # load variable metadata
  var_data <- readRDS(paste0("data/data_", year, ".rds"))
  
  # convert categorical variables to factors
  for (var in categorical_vars) {
    levels <- unlist(var_data[[var]]$values$item)
    
    data[[var]] <- factor(
      data[[var]],
      levels = names(levels),
      labels = levels
    )
  }
  
  # give the data the census class
  class(data) <- c("census", class(data))
  
  return(data)
}
```
:::


### Function Tests

::: {.cell}

```{.r .cell-code}
# test years

# valid
test_2021 <- query_census(
  year = 2021,
  api_key = api_key
  )
head(test_2021)
```

::: {.cell-output .cell-output-stdout}

```
# A tibble: 6 × 4
   AGEP PWGTP SEX    state
  <dbl> <dbl> <fct>  <chr>
1    21    78 Male   37   
2    58     9 Female 37   
3    20    32 Female 37   
4    40     6 Female 37   
5    21   119 Male   37   
6    20    65 Male   37   
```


:::

```{.r .cell-code}
test_2023 <- query_census(
  year = 2023,
  api_key = api_key
  )
head(test_2023)
```

::: {.cell-output .cell-output-stdout}

```
# A tibble: 6 × 4
   AGEP PWGTP SEX    state
  <dbl> <dbl> <fct>  <chr>
1    18    62 Female 37   
2    93    94 Female 37   
3    22    73 Male   37   
4    39    63 Male   37   
5    19    50 Male   37   
6    39    41 Male   37   
```


:::

```{.r .cell-code}
# invalid
query_census(
  year = 2002,
  api_key = api_key
  )
```

::: {.cell-output .cell-output-error}

```
Error in `query_census()`:
! year must be in 2021, 2022, 2023, or 2024
```


:::

```{.r .cell-code}
# test numeric variables

# valid
test_numeric <- query_census(
  numeric_vars = c("AGEP", "PWGTP"),
  api_key = api_key
  )
head(test_numeric)
```

::: {.cell-output .cell-output-stdout}

```
# A tibble: 6 × 4
   AGEP PWGTP SEX    state
  <dbl> <dbl> <fct>  <chr>
1    23    60 Male   37   
2    21    49 Male   37   
3    20    24 Male   37   
4    79    54 Female 37   
5    18    79 Male   37   
6    19    54 Female 37   
```


:::

```{.r .cell-code}
# invalid numeric variables
query_census(
  numeric_vars = c("BDSP", "PWGTP"),
  api_key = api_key
  )
```

::: {.cell-output .cell-output-error}

```
Error in `query_census()`:
! Invalid numeric variable. Choose from AGEP, GASP, GRPIP, JWAP, JWDP, JWMNP, and PWGTP.
```


:::

```{.r .cell-code}
query_census(
  numeric_vars = c("AGEP", "GASP"),
  api_key = api_key
  )
```

::: {.cell-output .cell-output-error}

```
Error in `query_census()`:
! PWGTP must be included.
```


:::

```{.r .cell-code}
query_census(
  numeric_vars = "PWGTP",
  api_key = api_key
  )
```

::: {.cell-output .cell-output-error}

```
Error in `query_census()`:
! numeric_vars must included PWGTP and at least one other numeric variable.
```


:::

```{.r .cell-code}
# test categorical variables

# valid
test_categorical <- query_census(
  numeric_vars = c("GASP", "PWGTP"),
  categorical_vars = c("FER", "SEX"),
  api_key = api_key
  )

head(test_categorical)
```

::: {.cell-output .cell-output-stdout}

```
# A tibble: 6 × 5
   GASP PWGTP FER                                                  SEX    state
  <dbl> <dbl> <fct>                                                <fct>  <chr>
1     3    60 N/A (less than 15 years/greater than 50 years/ male) Male   37   
2     3    49 N/A (less than 15 years/greater than 50 years/ male) Male   37   
3     3    24 N/A (less than 15 years/greater than 50 years/ male) Male   37   
4     3    54 N/A (less than 15 years/greater than 50 years/ male) Female 37   
5     3    79 N/A (less than 15 years/greater than 50 years/ male) Male   37   
6     3    54 No                                                   Female 37   
```


:::

```{.r .cell-code}
class(test_categorical$SEX)
```

::: {.cell-output .cell-output-stdout}

```
[1] "factor"
```


:::

```{.r .cell-code}
levels(test_categorical$SEX)
```

::: {.cell-output .cell-output-stdout}

```
[1] "Male"   "Female"
```


:::

```{.r .cell-code}
# invalid
query_census(
  categorical_vars = "WAOB",
  api_key = api_key
  )
```

::: {.cell-output .cell-output-error}

```
Error in `query_census()`:
! Invalid categorical variable. Choose from FER, HHL, SCH, SCHL, and SEX.
```


:::

```{.r .cell-code}
# test geography

# valid 
test_geo <- query_census(
  geography = "Region",
  specific_geography = "South",
  api_key = api_key
  )
head(test_geo)
```

::: {.cell-output .cell-output-stdout}

```
# A tibble: 6 × 4
   AGEP PWGTP SEX    region
  <dbl> <dbl> <fct>  <chr> 
1    25   103 Male   3     
2    47    38 Male   3     
3    35    55 Male   3     
4    23    60 Male   3     
5    43     8 Female 3     
6    46    39 Male   3     
```


:::

```{.r .cell-code}
test_geo2 <- query_census(
  specific_geography = "California",
  api_key = api_key
  )
head(test_geo2)
```

::: {.cell-output .cell-output-stdout}

```
# A tibble: 6 × 4
   AGEP PWGTP SEX    state
  <dbl> <dbl> <fct>  <chr>
1    45    78 Male   06   
2    45    39 Male   06   
3    21    10 Female 06   
4    28    23 Male   06   
5    19    53 Female 06   
6    56    15 Male   06   
```


:::

```{.r .cell-code}
# invalid geography
query_census(
  geography = "All",
  api_key = api_key
  )
```

::: {.cell-output .cell-output-error}

```
Error in `query_census()`:
! geography must be either Region, Division, or State.
```


:::

```{.r .cell-code}
query_census(
  geography = c("Region", "Division"),
  api_key = api_key
  )
```

::: {.cell-output .cell-output-error}

```
Error in `query_census()`:
! geography must be either Region, Division, or State.
```


:::

```{.r .cell-code}
query_census(
  specific_geography = "Puerto Rico",
  api_key = api_key
  )
```

::: {.cell-output .cell-output-error}

```
Error in `query_census()`:
! Puerto Rico is not included in this function.
```


:::

```{.r .cell-code}
query_census(
  specific_geography = "Cuba",
  api_key = api_key
  )
```

::: {.cell-output .cell-output-error}

```
Error in `query_census()`:
! Invalid specific_geography.
```


:::

```{.r .cell-code}
# test default query
test_default <- query_census(
  api_key = api_key
  )
head(test_default)
```

::: {.cell-output .cell-output-stdout}

```
# A tibble: 6 × 4
   AGEP PWGTP SEX    state
  <dbl> <dbl> <fct>  <chr>
1    23    60 Male   37   
2    21    49 Male   37   
3    20    24 Male   37   
4    79    54 Female 37   
5    18    79 Male   37   
6    19    54 Female 37   
```


:::

```{.r .cell-code}
# test time variable
test_time <- query_census(
  numeric_vars = c("JWAP", "PWGTP"),
  api_key = api_key
)
head(test_time$JWAP)
```

::: {.cell-output .cell-output-stdout}

```
07:32:00
      NA
07:37:00
      NA
      NA
      NA
```


:::

```{.r .cell-code}
class(test_time$JWAP)
```

::: {.cell-output .cell-output-stdout}

```
[1] "hms"      "difftime"
```


:::

```{.r .cell-code}
# test census class
class(test_time)
```

::: {.cell-output .cell-output-stdout}

```
[1] "census"     "tbl_df"     "tbl"        "data.frame"
```


:::

```{.r .cell-code}
# general test
gen_test <- query_census(
  numeric_vars = c("JWAP", "PWGTP"),
  api_key = api_key
)
class(gen_test$JWAP)
```

::: {.cell-output .cell-output-stdout}

```
[1] "hms"      "difftime"
```


:::

```{.r .cell-code}
class(gen_test$SEX)
```

::: {.cell-output .cell-output-stdout}

```
[1] "factor"
```


:::

```{.r .cell-code}
levels(gen_test$SEX)
```

::: {.cell-output .cell-output-stdout}

```
[1] "Male"   "Female"
```


:::

```{.r .cell-code}
head(gen_test$SEX)
```

::: {.cell-output .cell-output-stdout}

```
[1] Male   Male   Male   Female Male   Female
Levels: Male Female
```


:::
:::


yada yada yada

## Combining Years


::: {.cell}

```{.r .cell-code}
# create function to specify multiple years of survey data

query_census_years <- function(
    years = c(2021, 2022, 2023, 2024),
    numeric_vars = c("AGEP", "PWGTP"),
    categorical_vars = "SEX",
    geography = "State",
    specific_geography = NULL,
    api_key
) {
  
  # check for valid years
  if (!all(years %in% c(2021, 2022, 2023, 2024))) {
    stop("years must be in 2021, 2022, 2023, or 2024")
  }
  
  # make sure at least one year in specified 
  if (length(years) < 1) {
    stop("At least one year must be specified.")
  }

  # create a list to store each year's data
  data_list <- list()

  # query each year
  for (i in seq_along(years)) {
    data_list[[i]] <- query_census(
      year = years[i],
      numeric_vars = numeric_vars,
      categorical_vars = categorical_vars,
      geography = geography,
      specific_geography = specific_geography,
      api_key = api_key
    )
    
    # add year variable
    data_list[[i]]$year <- years[i]
  }
  
  # combine all years into one tibble
  data <- bind_rows(data_list)
  
  return(data)
}
```
:::



::: {.cell}

```{.r .cell-code}
# test function

combined_test <- query_census_years(
  years = c(2022, 2024),
  api_key = api_key
)
head(combined_test)
```

::: {.cell-output .cell-output-stdout}

```
# A tibble: 6 × 5
   AGEP PWGTP SEX    state  year
  <dbl> <dbl> <fct>  <chr> <dbl>
1    80    45 Male   37     2022
2    60     7 Male   37     2022
3    18    64 Male   37     2022
4    18    56 Male   37     2022
5    87    50 Female 37     2022
6    18    56 Male   37     2022
```


:::

```{.r .cell-code}
table(combined_test$year)
```

::: {.cell-output .cell-output-stdout}

```

  2022   2024 
109230 114270 
```


:::

```{.r .cell-code}
class(combined_test)
```

::: {.cell-output .cell-output-stdout}

```
[1] "census"     "tbl_df"     "tbl"        "data.frame"
```


:::

```{.r .cell-code}
combined_all_years <- query_census_years(
  years = c(2021, 2022, 2023, 2024),
  api_key = api_key
)
head(combined_all_years)
```

::: {.cell-output .cell-output-stdout}

```
# A tibble: 6 × 5
   AGEP PWGTP SEX    state  year
  <dbl> <dbl> <fct>  <chr> <dbl>
1    21    78 Male   37     2021
2    58     9 Female 37     2021
3    20    32 Female 37     2021
4    40     6 Female 37     2021
5    21   119 Male   37     2021
6    20    65 Male   37     2021
```


:::

```{.r .cell-code}
table(combined_all_years$year)
```

::: {.cell-output .cell-output-stdout}

```

  2021   2022   2023   2024 
104337 109230 111920 114270 
```


:::

```{.r .cell-code}
combined_test2 <- query_census_years(
  years = c(2022, 2023, 2024),
  numeric_vars = c("GASP", "PWGTP"),
  categorical_vars = c("FER", "SEX"),
  geography = "State",
  specific_geography = "California",
  api_key = api_key
)
head(combined_test2)
```

::: {.cell-output .cell-output-stdout}

```
# A tibble: 6 × 6
   GASP PWGTP FER                                              SEX   state  year
  <dbl> <dbl> <fct>                                            <fct> <chr> <dbl>
1     3    14 N/A (less than 15 years/greater than 50 years/ … Fema… 06     2022
2     3    27 N/A (less than 15 years/greater than 50 years/ … Male  06     2022
3     3    70 N/A (less than 15 years/greater than 50 years/ … Male  06     2022
4     3    22 No                                               Fema… 06     2022
5     3     8 No                                               Fema… 06     2022
6     3    49 N/A (less than 15 years/greater than 50 years/ … Male  06     2022
```


:::

```{.r .cell-code}
combined_all_states <- query_census_years(
  years = c(2023, 2024),
  specific_geography = "all",
  api_key = api_key
)
table(combined_all_states$year)
```

::: {.cell-output .cell-output-stdout}

```

   2023    2024 
3405809 3422888 
```


:::
:::


# Part 2: Writing a Generic Function for Summarizing
