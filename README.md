# Taxi_Cab_Prediction_Analysis

# Business Problem/Problem Statement

For a given location in New York City, our goal is to predict the fare of  taxi ride given number of pickups in that given location as well as the pickup timestamp, and the passenger count.

## Purpose:
In last few years, it has been observed that number of operating hire vehicles and number of trips in app-based vehicle increased drastically. However, taxi trips have fallen in same proportion in which number of vehicles increased.

The purpose of doing this exercise is two-fold:

1. To facilitate the cab organisation data centric outcome so that they can provide the optimize overall taxi fare by incorporating the flexibility in some of the fixed cost like waiting time in pickup, drop off and traffic jam along with peak hours.
2. To increase the Net promoting Score (NPS) by winning the confidence of customers in competitive market which resulted in improving taxi trips.
## Roadmap:
The quality of results depends on the data quality and features used for modelling. Given below are the steps, I followed while building the model

1. Business Understanding
2. Data Integration and massaging
3. Exploratory Analysis
4. Building Model
5. Training and validation of Model
6. Result and Interpretation

## Business Understanding

This step involves understanding of business rules and terminology very well. It is the first and most important step of building a model. Without having proper knowledge        about business, one cannot make a proper model.


## Data Integration and massaging

This step involves data reading, data preparing by attempting missing value/outlier treatment, creation of derived variable, dropping unwanted columns, converting the categorical variables to machine-understandable format, and finally splitting of training data into train and validation sets.

1. Loading Dataset: The dataset consists of 50,000 rows and 8 Columns.

Table-1: List of variable with description available in Raw Dataset

  |       Columns                 |                              Descriptions                                                                           |
  |-------------------------------|---------------------------------------------------------------------------------------------------------------------|
  |      unique_id                |                                       Unique identifier for each respondent                                         |
  |      date_time_of_pickup	    |                                        The time when the ride started                                               |
  |      longitude_of_pickup      |                                       Longitude of the taxi ride pickup point                                       |
  |      latitude_of_pickup       |                                        Latitude of the taxi ride pickup point                                       |
  |      longitude__of_dropoff	  |                                        Longitude of the taxi ride dropoff point                                     |
  |      latitude_of_dropoff      |                                         Latitude of the taxi ride dropoff                                           |
  |      no_of_passenger	        |                                     count of the passengers during the ride                                         |
  |      amount                   |                                  (target variable) dollar amount of the cost of the taxi ride                       |
  
 
2. Data Preparation:

- Missing Value Treatment: There was no null value present in data set.
- Outlier Treatment:

  - Amount: We capped the minimum value at 3 because the amount/fare can't be negative. The least it can be is 3. The maximum value I capped according to the 99th percentile.
  - No. of Passenger: As in case of passengers it can't be more than 4 people excluding the driver in one ride.
  - All the other capping were done according to the 99th percentile of the dataset.

Table-2: Statistical Information of Raw Dataset

  |       Columns                 |   Count      |    mean      |      STD        |    Min      |       Q1     |    Q2       |    Q3      |     MAX       |
  |-------------------------------|--------------|--------------|-----------------|-------------|--------------|-------------|------------|-------------- |
  |      longitude_of_pickup      |   50000.00	 |   -72.51     |    10.39	      |   -75.42	  |    -73.99	   |  -73.99	   |  -73.99	  |   40.78       |    
  |      latitude_of_pickup       |   50000.00   |    39.93     |     6.22        |   -74.01	  |    40.73     |   40.75     |  40.77	    |   401.08      |                   
  |      longitude__of_dropoff	  |   50000.00	 |    -72.50	  |     10.41       |   -72.50	  |    10.41     |  -84.65	   |  -73.99	  |  40.85        |                     
  |      latitude_of_dropoff      |   50000.00	 |     39.93	  |     6.01        |    -74.01	  |   40.73      |   40.75     |  40.75     |     43.42     |               
  |      no_of_passenger	        |   50000.00	 |     1.67	    |     1.29        |    0.00     |     1.00     |     1.00    |     2.00   |    6.00       |                     
  |      amount                   |   50000.00	 |     11.36	  |    9.69	        |     -5.00   |    6.00      |    8.50     |     12.50  |    200.00     |                           
  
  
   - Feature Engineering:
    
      - Distance Travel Calculation: There are three methods to calculate the distance.

        1. Euclidean Distance: Euclidean Distance represents the shortest distance between two points.
        
        
         ```javascript
          
           d = ((p1 - q1)^2 + (p2 - q2)^2)^1/2
           
         ```
         Where, pi, qi = data points
         
        2. Manhattan Distance: Manhattan Distance is the sum of absolute differences between points across all the dimensions.
        
        ```javascript
          
           d = |p1 - q1| + |p2 - q2|
           
         ```
         
         3. Minkowski Distance: It is the generalized form of Euclidean and Manhattan Distance.
         
         ```javascript
          
           D = Summation( |pi - qi|^p )^1/p
           
         ```
         
       - We derived both Euclidean and Manhattan Distance formula to calculate the distance between pickup and drop-off locations.
       - From variable "date_time_of_pickup" I extracted pickup_dayofweek,  pickup_hour, total_distance,pickup_month,Is_it_Weekend,Is_Starting_month,Is_Mid_month,Is_Ending_month 
         Morning, Afternoon, Evening, LateNIght, Is_Leap for checking the effect from various perspective.
         
  - Discarding Redundant Variables: Given below variables have been dropped which are not relevant or have been used in deriving new variables before model building.
  
  Table-3: List of Discarded Variables
   
  |       Columns                 |                              Descriptions                                                                           |
  |-------------------------------|---------------------------------------------------------------------------------------------------------------------|
  |      unique_id                |                                       Unique identifier for each respondent                                         |
  |      date_time_of_pickup	    |                                        The time when the ride started                                               |
  |      longitude_of_pickup      |                                       Longitude of the taxi ride pickup point                                       |
  |      latitude_of_pickup       |                                        Latitude of the taxi ride pickup point                                       |
  |      longitude__of_dropoff	  |                                        Longitude of the taxi ride dropoff point                                     |
  |      latitude_of_dropoff      |                                         Latitude of the taxi ride dropoff                                           |
  |      pickup_day_no   	        |                                         Weekday in numbers                                                          |
  
  
 - Dummy Variable Creation: Machine Learning Algorithms cannot work on object type datatypes so for that reason if we have any categorical variable preent in the given dataset,
   I have created dummy of time of day and pickup_day. Also, we created other variables such as Is_weekend, Is_starting_of_month etc for better analysis.
   
 Table-4: Final list of variables used in Model
 
  |       Columns            |                     |              | 
  |--------------------------|---------------------|--------------|
  |    no_of_passenger       |   Is_Starting_month |   LateNight  |  
  |     amount               |    Is_Mid_month     |   Is_Leap    |    
  |     total_distance	     |   Is_Ending_month	 | Is_it_Weekend|                       
  |      pickup_hour         |     Morning	       |    Evening   |                   
  |      	year               |     Afternoon	     |              |                          
 

- Splitting the data into train and test: Split the data into train and test datasets and feed it into our model. Here we have split the data into 80% train and 20% test. The     training and testing dataset consist of 39155 rows and 9789 rows correspondingly with 23 Columns in both datasets. Below is the list of final variables in training and testing   dataset.


## Exploratory Analysis

### Graphical Analysis

![image](https://user-images.githubusercontent.com/79011767/139195035-41a60941-f148-43b4-bfc8-bd7961dd0cdc.png)

I checked the distribution of the amount/fare and as it can be seen that the distribution is highly positive skewed. In addition, the density of fare in the range of 0 to 20 is significantly higher. This could indicate that individuals prefer not to use taxi services for long distances because long distances imply a high fare.


![image](https://user-images.githubusercontent.com/79011767/139392226-74ca88f1-83be-496f-b16b-4fab6558fe8c.png)

In comparison to the other times, the fare is significantly higher in the afternoon and at night. This increase could be related to the unusual hours, as most cab firms impose a night premium so that drivers don't cancel rides and lose out on the odd hour incentives.

We had now divided the entire 24-hour period into four pieces. Namely:
  - Morning: 6 a.m. to 12 p.m.
  - Afternoon: 12 p.m. to 16 p.m.
  - Evening: 16 p.m. to 22 p.m.
  - Night: 22 p.m. to 24 p.m.

![image](https://user-images.githubusercontent.com/79011767/139392691-8cc68ae5-c205-446a-ace2-8040ef852094.png)

The suitable trend may be seen when looking at the time of day vs. the number of passengers on different days. The majority of passengers travelling at evening and night do so on weekends, particularly on Friday, Saturday and Sunday, while for other times of day, particularly in the morning, less people use taxi services on Saturday and Sunday.





































# Model Building:

1. Applying the model: Applied RandomForest Regression.
2. Predicting y: Predict the y_train using x_train through the model and same goes for y_test.
3. Calculating MSE, RMSE and MAE

# Testing and validation of Model

The validation of model helps in understanding how the model fit using training data and works on any unseen data. This facilitate to gauge whether the model is overfitting or underfitting. Overfitting is the term used when training error is low, but the testing error is high. This is a common problem with complex models because they tend to memorize the underlying data and hence perform poorly on unseen data.

# Result and Interpretation

R2 is a measure of how well a model predicts the target variables. The error is calculated using the RMSE and MAE methods. As it can be seen, our model has good accuracy and a low bias/variance trade-off. We can conclude from this analysis that our model is performing well.


Table-5: Statistics of Predicted variable (Taxi fare)

  |       Statistic          |   Training Dataset  |  Testing Dataset  | 
  |--------------------------|---------------------|-------------------|
  |                          |       Amount        |     Amount        |  
  |     count                |    Is_Mid_month     |   Is_Leap        |    
  |     mean	               |   Is_Ending_month	 | Is_it_Weekend   |                       
  |      std                 |     Morning	       |    Evening      |                   
  |      	min                |     Afternoon	     |                | 
  |       Q1                 |                   |                 |
  |       Q2                 |                    |                |
  |       Q3                 |                   |                   |
  |      Max                 |                |                 |
  
  
  Table-6: Model Statistics of Training and Testing dataset
  
  |   Model Parameter	|    Training Dataset	  |  Testing Dataset	|
  |-------------------|-----------------------|-------------------|
  |  R square(R2)	    |                   |                     |
  |  MAE              |                |                        |
  | RMSE              |              |                         |



  
