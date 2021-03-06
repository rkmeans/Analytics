MITx: 15.071x The Analytics Edge, Week1, Demographics and Employment in the US
========================================================
# Tarek Dib

## *Introduction*
In the wake of the Great Recession of 2009, there has been a good deal of focus on employment statistics, one of the most important metrics policymakers use to gauge the overall strength of the economy. In the United States, the government measures unemployment using the Current Population Survey (CPS), which collects demographic and employment information from a wide range of Americans each month. In this exercise, use the September 2013 version of this rich, nationally representative dataset (available online).

The observations in the dataset represent people surveyed in the September 2013 CPS who actually completed a survey.

## *Variables*
    PeopleInHousehold: The number of people in the interviewee's household.
    Region: The census region where the interviewee lives.
    State: The state where the interviewee lives.
    MetroAreaCode: A code that identifies the metropolitan area in which the interviewee lives (missing if the interviewee does not live in a metropolitan area). The mapping from codes to names of metropolitan areas is provided in the file MetroAreaCodes.csv.
    Age: The age, in years, of the interviewee. 80 represents people aged 80-84, and 85 represents people aged 85 and higher.
    Married: The marriage status of the interviewee.
    Sex: The sex of the interviewee.
    Education: The maximum level of education obtained by the interviewee.
    Race: The race of the interviewee.
    Hispanic: Whether the interviewee is of Hispanic ethnicity.
    CountryOfBirthCode: A code identifying the country of birth of the interviewee. The mapping from codes to names of countries is provided in the file CountryCodes.csv.
    Citizenship: The United States citizenship status of the interviewee.
    EmploymentStatus: The status of employment of the interviewee.
    Industry: The industry of employment of the interviewee (only available if they are employed).


## *Loading Data and Descriptive Statistics*

```r
# Set the directory at where the data is located
setwd("/home/tarek/Analytics/Week1/Rlectures/Data")
```

```
## Error: cannot change working directory
```

```r
# Read the Data
CPS <- read.csv("CPSData.csv")
```

```
## Warning: cannot open file 'CPSData.csv': No such file or directory
```

```
## Error: cannot open the connection
```

```r
MetroAreaMap <- read.csv("MetroAreaCodes.csv")
```

```
## Warning: cannot open file 'MetroAreaCodes.csv': No such file or directory
```

```
## Error: cannot open the connection
```

```r
CountryMap <- read.csv("CountryCodes.csv")
```

```
## Warning: cannot open file 'CountryCodes.csv': No such file or directory
```

```
## Error: cannot open the connection
```

```r
str(CPS)
```

```
## Error: object 'CPS' not found
```

```r
summary(CPS)
```

```
## Error: object 'CPS' not found
```

```r
# the most common industry of employment
which.max(table(CPS$Industry))
```

```
## Error: object 'CPS' not found
```

```r
# The states that the fewest and largest number of interviewees
which.min(table(CPS$State))
```

```
## Error: object 'CPS' not found
```

```r
which.max(table(CPS$State))
```

```
## Error: object 'CPS' not found
```

```r
# proportion of interviewees who are citizens of the United States
citizen = CPS[CPS$Citizenship == "Citizen, Native" | CPS$Citizenship == "Citizen, Naturalized", 
    ]
```

```
## Error: object 'CPS' not found
```

```r
nrow(citizen)/nrow(CPS)
```

```
## Error: object 'citizen' not found
```

```r
# Race and hispanic ethnicity
table(CPS$Race, CPS$Hispanic)
```

```
## Error: object 'CPS' not found
```

```r
# We can test the relationship between these four variable values and
# whether the Married variable is missing table(CPS$Region,
# is.na(CPS$Married)) table(CPS$Sex, is.na(CPS$Married)) table(CPS$Age,
# is.na(CPS$Married)) table(CPS$Citizenship, is.na(CPS$Married))

# States that had all interviewees living in a non-metropolitan area. And
# states that had all interviewees living in a metropolitan area
table(CPS$State, is.na(CPS$MetroAreaCode))  # Alaska and Wyoming have no interviewees living in a metropolitan area, and the District of Columbia, New Jersey, and Rhode Island have all interviewees living in a metro area. 
```

```
## Error: object 'CPS' not found
```

```r

# Region of the United States has the largest proportion of interviewees
# living in a non-metropolitan area?
table(CPS$Region, is.na(CPS$MetroAreaCode))
```

```
## Error: object 'CPS' not found
```

```r
# States with highest numbers of interviewees living in non-metropolitan
# areas
sort(round(tapply(is.na(CPS$MetroAreaCode), CPS$State, mean), 2))
```

```
## Error: object 'CPS' not found
```

## *Integrating Metropolitan Area Data*
To merge in the metropolitan areas, we want to connect the field MetroAreaCode from the CPS data frame with the field Code in MetroAreaMap.

```r
# Merges the two data frames on these columns, overwriting the CPS data
# frame with the result
CPS = merge(CPS, MetroAreaMap, by.x = "MetroAreaCode", by.y = "Code", all.x = TRUE)
```

```
## Error: object 'CPS' not found
```

```r
summary(CPS)
```

```
## Error: object 'CPS' not found
```

```r
# Largest number of interviewees in a metropolitan area
which.max(table(CPS$MetroArea))
```

```
## Error: object 'CPS' not found
```

```r
# Metropolitan area that has the highest proportion of interviewees of
# Hispanic ethnicity
which.max(tapply(CPS$Hispanic, CPS$MetroArea, mean))
```

```
## Error: object 'CPS' not found
```

```r
# Metropolitan areas in the United States from which at least 20% of
# interviewees are Asian.
which(tapply(CPS$Race == "Asian", CPS$MetroArea, mean) > 0.2)
```

```
## Error: object 'CPS' not found
```

```r
# Average that have no highschool diploma. Metro that has the least.
which.min(tapply(CPS$Education == "No high school diploma", CPS$MetroArea, mean, 
    na.rm = T))
```

```
## Error: object 'CPS' not found
```


# *Integrating Country of Birth Data*

```r
CPS = merge(CPS, CountryMap, by.x = "CountryOfBirthCode", by.y = "Code", all.x = TRUE)
```

```
## Error: object 'CPS' not found
```

```r
summary(CPS)
```

```
## Error: object 'CPS' not found
```

```r
# Outside North America, the most common place of birth
sort(table(CPS$Country))
```

```
## Error: object 'CPS' not found
```

```r
# proportion of the interviewees from the 'New York-Northern New
# Jersey-Long Island, NY-NJ-PA' metropolitan area who have a country of
# birth that is not the United State
t = table(CPS$MetroArea == "New York-Northern New Jersey-Long Island, NY-NJ-PA", 
    CPS$Country != "United States")
```

```
## Error: object 'CPS' not found
```

```r
t
```

```
## function (x) 
## UseMethod("t")
## <bytecode: 0xa1c8f68>
## <environment: namespace:base>
```

```r
# Convert the table into a dataframe
df = as.data.frame(t)
```

```
## Error: cannot coerce class '"function"' into a data.frame
```

```r
df$Freq[df$Var1 == TRUE][2]/(df$Freq[df$Var1 == TRUE][1] + df$Freq[df$Var1 == 
    TRUE][2])
```

```
## Error: object of type 'closure' is not subsettable
```

```r
# Which metropolitan area has the largest number (note -- not proportion)
# of interviewees with a country of birth in India, Brazil and Somalia,
# respectively?
which.max(tapply(CPS$Country == "India", CPS$MetroArea, sum, na.rm = TRUE))
```

```
## Error: object 'CPS' not found
```

```r
which.max(tapply(CPS$Country == "Brazil", CPS$MetroArea, sum, na.rm = TRUE))
```

```
## Error: object 'CPS' not found
```

```r
which.max(tapply(CPS$Country == "Somalia", CPS$MetroArea, sum, na.rm = TRUE))
```

```
## Error: object 'CPS' not found
```





