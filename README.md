# Hotel-Booking-Demand Dataset

This data set contains booking information for a city hotel and a resort hotel, and includes information such as when the booking was made, length of stay, the number of adults, children, and/or babies, and the number of available parking spaces, among other things. All personally identifying information has been removed from the data.

The data is originally from the article Hotel Booking Demand Datasets, written by Nuno Antonio, Ana Almeida, and Luis Nunes for Data in Brief, Volume 22, February 2019.


content of project 
       Introduction
       Cleaning
       Analysis
       Finding and Reccomendations
       
       
 Findings and Recommendations
 
 
City hotels have around double the amount of reservations than resort hotels.
Both city and resort hotels reservations peak in August are the lowest in January. March through October seems to be their busiest window for the year. reservations fall in from November to January.
The vast majority of reservations have 2 adults followed by 1 adult.
The more than 90% of reservation are adults. One child is slightly larger in percentage than two children.
Around 90% of reservation do not require a parking space
August 2016 saw a big spike in parking space requests. Could be a problem in the future if parking capacity is low.
the Average Daily Rate measures the average rental revenue earned for an occupied room per day. This plot shows the monthly total for APR rises from around May to October then dips down again.
My recommendations would be to target couples during the spring, summer and part of fall. Focus especially on the summer months as they bring in the best average daily rates. Resort hotels may have to increase their advertising efforts compared to city hotels. I would also make sure there are enough parking spaces during the summer months. With a surge of demand of spaces it would prudent to make sure that they have the capacity or a plan in place for any overflow.      



import dataset through SSMS 



review data before data cleaning, find out NULL value or N/A





data cleaning : Dropping "company" for being mostly null values and "arrival_date_week_number" for not being necessary.



data massaging: 
Company has sizable missing values, while country, children, and agent have missing entries that can be replaced using the mode and median.Filling missing values for country using mode and filling the missing values of children and agent with the median.
Creating an a full arrival date column.
Adding a total guests column that includes adults and children.
Creating a dataframe to see monthly totals.


Analysis 










