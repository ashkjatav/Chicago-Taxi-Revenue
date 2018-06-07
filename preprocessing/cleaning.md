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
#### Reading 2016 data-set

```{r}
#Reading dataset and defining metadata
df= fread("H:/Competition/Chicago_taxi_trips.csv",
          colClasses = c("Trip ID"="character","Taxi ID"= "character",
         "Trip Start Timestamp"= "Date", "Trip End Timestamp"="Date", 
        "Trip Seconds"= "integer","Trip Miles"= "integer", 
        "Pickup Census Tract"= "character", "Dropoff Census Tract"="character", 
        "Pickup Community Area"= "integer","Pickup O'Hare Community Area"="integer", "Dropoff Community Area"= "integer",
        "Fare"="character","Tips"="character", "Tolls"= "character","Extras"=  "character","Trip Total"= "character",
      "Payment Type"=  "factor","Company"= "factor",
    "Pickup Centroid Latitude"= "integer","Pickup Centroid Longitude"= "integer", 
    "Pickup Centroid Location"= "character","Dropoff Centroid Latitude"= "integer",
       "Dropoff Centroid Longitude"= "integer","Dropoff Centroid  Location" ="character"))
