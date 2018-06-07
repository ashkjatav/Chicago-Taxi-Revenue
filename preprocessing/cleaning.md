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

```R
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
```

#### Renaming all the variables

```R
df= df %>%
  rename(unique_key=`Trip ID`, taxi_id= `Taxi ID`,
         trip_start_timestamp=`Trip Start Timestamp`,
         trip_end_timestamp= `Trip End Timestamp`,
         trip_seconds=`Trip Seconds`, trip_miles=`Trip Miles`,
         pickup_census_tract= `Pickup Census Tract`,
         dropoff_census_tract= `Dropoff Census Tract`,
         pickup_community_area= `Pickup Community Area`,
         dropoff_community_area= `Dropoff Community Area`,
         fare=Fare, tips= Tips, tolls= Tolls, extras= Extras,
         trip_total= `Trip Total`, payment_type= `Payment Type`,
         company= Company, pickup_latitude= `Pickup Centroid Latitude`,
         pickup_longitude= `Pickup Centroid Longitude`,
         pickup_location= `Pickup Centroid Location`,
         dropoff_latitude= `Dropoff Centroid Latitude`,
         dropoff_longitude= `Dropoff Centroid Longitude`,
         dropoff_location= `Dropoff Centroid  Location`)
```
#### Converting 12 hour Timestamp into 24 hour format

```R
df[,3]=  lapply(df[,3],as.POSIXct ,format='%m/%d/%Y %I:%M:%S %p')
df[,4]=  lapply(df[,4],as.POSIXct ,format='%m/%d/%Y %I:%M:%S %p')
```
#### Removing $ string from the monetary columns
```R
df=df %>%
  mutate(fare= substring(fare,2, nchar(fare)),
         tips= substring(tips,2,nchar(tips)),
tolls= substring(tolls,2,nchar(tolls)),
extras= substring(extras,2,nchar(extras)),
trip_total= substring(trip_total,2,nchar(trip_total)))

df=df %>%
  mutate(fare= as.numeric(fare),
         tips= as.numeric(tips),
         tolls= as.numeric(tolls),
         extras= as.numeric(extras),
         trip_total=as.numeric(trip_total))
```
