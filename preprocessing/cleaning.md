## [Overview](../index.md)

## [Data Exploration](../data_exploration/exploration.md)

# Preprocessing

## [Feature Creation](../feature_creation/features.md)

## [Model Building](../model_building/model.md)

## [Conclusion](../conclusion/conclusion.md)

### Cleaning and Preprocessing Chicago Cab Data

Since the data available on Google BigQuery is from year 2013 to 2105, we will incorporate the 2016 data into Google BigQuery table. We first downloaded the Cab data from City of Chicago website. But the column names and some of the columns were different from what was available on Google BigQuery. So we first format the 2016 and 2017 data similar to BigQuery data so that the new data can be appended to the old one.

#### Loading Libraries
```{r}
library(dplyr)
library(data.table)
```
