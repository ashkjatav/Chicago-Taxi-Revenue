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
**hour**|**day**|**year**|**month**|**taxi\_id**|**Number\_rides**|**trip\_start\_timestamp**|**trip\_end\_timestamp**|**trip\_seconds**|**trip\_miles**|**trip\_total**|**payment\_types**|**companies**|**pickup\_latitude**|**pickup\_longitude**|**pickup\_location**|**dropoff\_latitude**|**dropoff\_longitude**|**dropoff\_location**| 
:-----:|:-----:|:-----:|:-----:|:-----:|:-----:|:-----:|:-----:|:-----:|:-----:|:-----:|:-----:|:-----:|:-----:|:-----:|:-----:|:-----:|:-----:|:-----:|:-----:
0|1|2013|1|2c35ec|3|2013-01-01 00:45:00 UTC|2013-01-01 00:45:00 UTC|1380|6|27.15|Cash| |41.92268628|-87.64220631|POINT (-87.6422063127 41.9305785697)|41.92904694|-87.65131088|POINT (-87.6513108767 41.9290469366)| 
1|1|2013|1|89dea8|4|2013-01-01 01:15:00 UTC|2013-01-01 01:30:00 UTC|1980|8.7|40.6|Cash| |41.89250778|-87.62621491|POINT (-87.6262105324 41.8991556134)|41.89915561|-87.63171737|POINT (-87.6262149064 41.8925077809)| 
1|1|2013|1|6b2939|4|2013-01-01 01:00:00 UTC|2013-01-01 01:15:00 UTC|2760|7.7|40.6|Cash| |41.94906053|-87.6616429|POINT (-87.6612652184 41.936159071)|41.97256254|-87.67208099|POINT (-87.6612652184 41.936159071)| 
2|1|2013|1|4bf516|3|2013-01-01 02:00:00 UTC|2013-01-01 02:45:00 UTC|3600|8.3|39.75|Cash| |41.93615907|-87.66126522|POINT (-87.6612652184 41.936159071)|41.90749193|-87.63576009|POINT (-87.6357600901 41.9074919303)| 
2|1|2013|1|a23c6cf|3|2013-01-01 02:15:00 UTC|2013-01-01 02:30:00 UTC|2280|12.5|39.75|Cash| |41.90749193|-87.63576009|POINT (-87.6357600901 41.9074919303)|41.92268628|-87.64948873|POINT (-87.6494887289 41.9226862843)| 
3|1|2013|1|33164e|3|2013-01-01 03:15:00 UTC|2013-01-01 03:30:00 UTC|2160|11.4|33.15|Cash| |41.90120699|-87.6430228|POINT (-87.6430228044 41.9345397158)|41.9000696|-87.69419737|POINT (-87.6941973701 41.9005603836)| 

Remove unwanted characters from the Timestamp. R does not automatically parse Timestamp. We need to convert it into Timestamp from String.

```R
df=df %>%
  mutate(trip_start_timestamp = substring(trip_start_timestamp,1, nchar(trip_start_timestamp)-4),
         trip_end_timestamp= substring(trip_end_timestamp, 1,nchar(trip_end_timestamp)-4))


df$trip_start_timestamp=  as.POSIXct(df$trip_end_timestamp,format="%Y-%m-%d %H:%M:%S")
df$trip_end_timestamp=  as.POSIXct(df$trip_end_timestamp,format="%Y-%m-%d %H:%M:%S")
df$time_identifier_day= date(df$trip_start_timestamp)
```
Selecting only features that seem important for predictive modeling

```R
df1= df %>%
  select( -taxi_id,
          -pickup_location,
          -dropoff_location)
```

When we looked at the mode of payment, we noticed that most of the riders prefer to pay by Cash and Credit with less than 5% of the total riders paying through Mobile Wallet or somthing similar. So we combined all the classes other than `Cash` and `Credit Card` into `other`.

```R
df1=df1 %>%
  mutate(payment_types= ifelse(payment_types=="Cash", "Cash", 
                               ifelse(payment_types=="Credit Card","Card","Other")))%>%
  mutate(payment_types= as.factor(payment_types))
```

Similar step was done in the `Companies` feature. There seems to be erroneous entries in the company types as the classes had unwanted strings and punctuation. This was causing same companies to appear as different classes. So, we first removed all the punctuation and numbers from the classes and then looked at the total number of rides booked through each company. There were around 100 companies, and only 6 of them only had more than 2000 number of rides over the course of 4 years. Remember!, we have taken median hourly ride. So, it makes around 35000 datapoints. Most of the companies total number of rides were less than 100. We clubbed all the company types other than the major 6 into `other` class.

```R
df1= df1 %>% 
  mutate(companies= ifelse(companies=='Taxi Affiliation Services',"Taxi Affiliation Services",
                           ifelse(companies=="Northwest Management LLC","Northwest Management LLC",
                                  ifelse(companies=="KOAM Taxi Association","KOAM Taxi Association",
                                         ifelse(companies== "Dispatch Taxi Affiliation","Dispatch Taxi Affiliation",
                                                ifelse(companies== "Choice Taxi Association", "Choice Taxi Association","other"))))))

df1$companies[is.na(df1$companies)]= "other"
df1$companies= as.factor(df1$companies)

```
