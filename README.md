## Moringa_Prep_Week4_IP
       In this week's independent project, I am working as a data scientist for an electric car-sharing service company. 
       I have been tasked to process stations data to understand electric car usage over time by solving 
       several research question;
        1. Identify the most popular hour of the day for picking up a shared electric car (Bluecar) in the city of 
           Paris over the month of April 2018.
        2. What is the most popular hour for returning cars?
        3. What station is the most popular?
              Overall?
              At the most popular picking hour?
        4. What postal code is the most popular for picking up Bluecars? 
              Does the most popular station  belong to that postal code?
                Overall?
                At the most popular picking hour?
        5. Do the results change if you consider Utilib and Utilib 1.4 instead of Bluecars? (that could be asked earlier to 
           push students to write modular code that can be used to query different things)
           
## Importing the libraries needed:
              import pandas as pd
              import pandas_profiling
              import numpy as np
              
## descriptive statistics:
       profile=pandas_profiling.ProfileReport(auto_lib)
       profile_to.html('autolib.html')
       
## data cleaning:
       #Check for completeness: missing values
              auto_lib.isnull().sum()
              
       #drop duplicated rows:
              auto_lib[auto_lib.duplicated(subset=None, keep= 'first')]

       #Validity: Drop unnecessary columns not needed to anser our questions:
             auto_lib.drop(['Address','Bluecar counter','Charge Slots', 'Charging Status','Public name','Displayed                          comment','Scheduled at', 'Subscription status','Station type','Geo point'], axis=1, inplace=True)
             
        #check for accuracy: Remove outliers
              Q1= auto_lib.quantile(0.25)
              Q3= auto_lib.quantile(0.75)
              IQR= Q3-Q1
              auto_lib= auto_lib[~((auto_lib < (Q1- 1.5*IQR))|(auto_lib > (Q3 + 1.5*IQR))).any(axis=1)]
              
## SAVING THE CLEAN DATASET AS CSV:
       auto_lib.to_csv('auto_cars.csv')
       
# loading our clean dataset:
       auto_car= pd.read_csv('/content/auto_cars.csv', index_col = 0)
       
# Answering our questions:
# Q1. What was the most popular hour of the day for picking up a shared electric car (Bluecar) in the city of Paris over the month of April 2018?

       #select city as Paris:
       paris= auto_car.copy()
       paris.drop(paris[paris.City != 'Paris'].index, inplace=True) 
       
        #sort the data by station and hour:
       paris.sort_values(['ID','day','hour','minute'], ascending=True, inplace=True)
       paris.head(20)
       
       #Picking up= negative change in cars at station 
       #create column with rate of change of cars per minute 
              paris['change']= paris['Cars'].diff()
              
       #Determine hour with the highest rate of negative change in cars
       paris1= paris[paris.change < 0]
       paris1.groupby(['hour'])['change'].sum().sort_values(ascending=True)
       
       Most popular hour is hour 19, with total pickups of 5534 cars in the dataset period 
# Q2  What is the most popular hour for returning cars?

       #sort the dataset(all cities) by station and hour 
              auto_car.sort_values(['ID','day','hour','minute'], ascending=True, inplace=True)
              auto_car.head()
       
       #create column with rate of change of cars per minute 
              auto_car['change']= auto_car['Cars'].diff()
              
       #Returns result in increase of cars at the station therefore Determine hour with the highest rate of positive change in         cars
              auto_car= auto_car[auto_car.change > 0]
              returns = auto_car.groupby(['hour'])['change'].sum().sort_values(ascending=False)
              returns
              
       Hour 20 has the highest rate of returns at 8565 
       
# Q3:What station is the most popular?
# 3(a) Overall?
       #station with most activity whether positive or negative.
       station= auto_car[auto_car.change!=0]
       station.head()
       station.groupby(['ID'])['change'].count().sort_values(ascending=False)
       
       Station with the most activity is paris-portedauteuil-parking which has a frequency of 395
# 3(b) At the most popular picking hour?
       #determine most popular picking hour overall:

              auto_car= auto_car[auto_car.change < 0]
              pick= auto_car.groupby(['hour'])['change'].sum().sort_values(ascending=True)
              pick    
       Hour 19 is most popular hour overall
       
       #most popular station at hour 19:
              station= auto_car[(auto_car.change!=0) & (auto_car.hour ==19)]
              station.head()
              station.groupby(['ID'])['change'].count().sort_values(ascending=False)
              
       station with highest rate of change at hour 19 is boulognebillancourt-jeanjaures-245 with frequency of 32
 # 4 What postal code is the most popular for picking up Bluecars? 

       code = auto_car[auto_car.change!=0] 
       code.groupby(['Postal code'])['change'].count().sort_values(ascending=False)
       
     The most popular Postal code is 75015 with a frequency of 7366
 # 5 Does the most popular station belong to that postal code?
#  5(a) Overall?

       auto_car.groupby(['Postal code'])['ID'].sort_values(ascending=False)

# 5(b) At the most popular picking hour?

       code = auto_car[(auto_car.change!=0) & (auto_car.hour ==19)]
       code.groupby(['Postal code'])['change'].count().sort_values(ascending=False)
