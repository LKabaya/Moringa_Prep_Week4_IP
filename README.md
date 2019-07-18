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
             auto_lib.drop(['Address','Bluecar counter','Charge Slots', 'Charging Status','Public name','Displayed                   comment','Scheduled at', 'Subscription status','Station type','Geo point'], axis=1, inplace=True)
             
             #check for accuracy: Remove outliers
              Q1= auto_lib.quantile(0.25)
              Q3= auto_lib.quantile(0.75)
              IQR= Q3-Q1
              auto_lib= auto_lib[~((auto_lib < (Q1- 1.5*IQR))|(auto_lib > (Q3 + 1.5*IQR))).any(axis=1)]
