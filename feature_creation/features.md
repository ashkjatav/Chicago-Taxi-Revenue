## [Overview](../index.md)

## [Data Exploration](../data_exploration/exploration.md)

## [Preprocessing](../preprocessing/cleaning.md)

# Feature Creation

## [Model Building](../model_building/model.md)

## [Conclusion](../conclusion/conclusion.md)

### Feature Creation and Analysis

In this section, we will bin the categories in the `payment_types` and `companies` features. Later we will perform feature extraction on the Timestamp variable as evident by EDA, time plays an important role while predicting the total fare amount.

Let's begin by loading the dataset from the Google BigQuery output in the previous section in R.

#### Loading Libraries
```R
library(dplyr)
library(data.table)
library(anytime)
library(lubridate)
```

#### Load data
```R
df= fread("hourly_data_2013-2016.csv")
head(df)
```

| hour | day | year | month | taxi_id | Number_rides | trip_start_timestamp    | trip_end_timestamp      | trip_seconds | trip_miles | trip_total | payment_types | companies | pickup_latitude | pickup_longitude | pickup_location                      | dropoff_latitude | dropoff_longitude | dropoff_location                     |
|------|-----|------|-------|---------|--------------|-------------------------|-------------------------|--------------|------------|------------|---------------|-----------|-----------------|------------------|--------------------------------------|------------------|-------------------|--------------------------------------|
| 0    | 1   | 2013 | 1     | 2c35ec  | 3            | 2013-01-01 00:45:00 UTC | 2013-01-01 00:45:00 UTC | 1380         | 6          | 27.15      | Cash          |           | 41.92268628     | -87.64220631     | POINT (-87.6422063127 41.9305785697) | 41.92904694      | -87.65131088      | POINT (-87.6513108767 41.9290469366) |
| 1    | 1   | 2013 | 1     | 89dea8  | 4            | 2013-01-01 01:15:00 UTC | 2013-01-01 01:30:00 UTC | 1980         | 8.7        | 40.6       | Cash          |           | 41.89250778     | -87.62621491     | POINT (-87.6262105324 41.8991556134) | 41.89915561      | -87.63171737      | POINT (-87.6262149064 41.8925077809) |
| 1    | 1   | 2013 | 1     | 6b2939  | 4            | 2013-01-01 01:00:00 UTC | 2013-01-01 01:15:00 UTC | 2760         | 7.7        | 40.6       | Cash          |           | 41.94906053     | -87.6616429      | POINT (-87.6612652184 41.936159071)  | 41.97256254      | -87.67208099      | POINT (-87.6612652184 41.936159071)  |
| 2    | 1   | 2013 | 1     | 4bf516  | 3            | 2013-01-01 02:00:00 UTC | 2013-01-01 02:45:00 UTC | 3600         | 8.3        | 39.75      | Cash          |           | 41.93615907     | -87.66126522     | POINT (-87.6612652184 41.936159071)  | 41.90749193      | -87.63576009      | POINT (-87.6357600901 41.9074919303) |
| 2    | 1   | 2013 | 1     | a23c6cf | 3            | 2013-01-01 02:15:00 UTC | 2013-01-01 02:30:00 UTC | 2280         | 12.5       | 39.75      | Cash          |           | 41.90749193     | -87.63576009     | POINT (-87.6357600901 41.9074919303) | 41.92268628      | -87.64948873      | POINT (-87.6494887289 41.9226862843) |
| 3    | 1   | 2013 | 1     | 33164e  | 3            | 2013-01-01 03:15:00 UTC | 2013-01-01 03:30:00 UTC | 2160         | 11.4       | 33.15      | Cash          |           | 41.90120699     | -87.6430228      | POINT (-87.6430228044 41.9345397158) | 41.9000696       | -87.69419737      | POINT (-87.6941973701 41.9005603836) |

#### Remove unwanted characters from the 
