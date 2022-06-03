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


Data cleaning:
review data before data cleaning, find out NULL value or N/A
use [0023Orders]
go

--review the dataset

--list total number of rows
select COUNT(*)
from [0023Orders]..[hotel booking]

--list total number of columns
SELECT count(*)
FROM information_schema.columns
WHERE table_name = 'hotel booking'

SELECT *
FROM information_schema.columns
WHERE table_name = 'hotel booking'



--list all data, find out NULL value
select *
from [0023Orders]..[hotel booking]



data cleaning : Dropping "company" for being mostly null values and "arrival_date_week_number" for not being necessary.
--delete column of company with mostly null values and "arrival_date_week_number" for not being necessary.
ALTER TABLE [0023Orders]..[hotel booking]
DROP COLUMN company, arrival_date_week_number


--delete rows where there is a NULL value: (how to find NULL values: use group by.eg: group by arrival_date_month)
DELETE 
FROM [0023Orders]..[hotel booking]
WHERE arrival_date_month is NULL ;

data massaging: 
Company has sizable missing values, while country, children, and agent have missing entries that can be replaced using the mode and median.Filling missing values for country using mode and filling the missing values of children and agent with the median.

Creating an a full arrival date column.(make sure data type is uniform)


alter table [0023Orders]..[hotel booking]
ADD arrivaldate as 
(CAST(arrival_date_year as char(4))+CAST (arrival_date_month as varchar(2))+CAST(arrival_date_day_of_month as varchar(2)))


validate data
select arrivaldate
from [0023Orders]..[hotel booking]

select CAST(arrival_date_year as char(4))+CAST (arrival_date_month as varchar(2))+CAST(arrival_date_day_of_month as varchar(2))
from [0023Orders]..[hotel booking]

Adding a total guests column that includes adults and children. (make sure data type is uniform)(how to populate an aggregated data into a new column: we can use alter table add column, then update set the column; or we can direclty use alter table add column as (aggregation))


ALTER TABLE table_name
ADD totalguest int

update [0023Orders]..[hotels booking]
set totalguest = adults+children


Alternative code: 

alter table [0023Orders]..[hotel booking]
ADD totalguest as (children + adults)




Creating a dataframe to see monthly totals.

--create a dataframe monthlytotal group by month 

select arrival_date_month, SUM(totalguest) as monthlytotal
from [0023Orders]..[hotel booking]
group by arrival_date_month



Analysis （EDA: exploratory data analysis）vs DDA (descriptive data analysis)

1. City hotels have around double the amount of reservations than resort hotels:(group by hotel, reservation count)

select hotel, COUNT(*)
from [0023Orders]..[hotel booking]
group by hotel


output:
City Hotel	79330
Resort Hotel	40060


2. Both city and resort hotels reservations peak in August and the lowest in January. March through October seems to be their busiest window for the year. reservations fall in from November to January.


--group by hotel, month 
select hotel,arrival_date_month,  COUNT(*) as amount
from [0023Orders]..[hotel booking]
group by hotel, arrival_date_month
order by hotel,amount 
 
 
 
 output: 
 
City Hotel	January	3736
City Hotel	December	4132
City Hotel	November	4357
City Hotel	February	4965
City Hotel	March	       6458
City Hotel	September	7400
City Hotel	April	       7480
City Hotel	October	7605
City Hotel	June	       7894
City Hotel	July	       8088
City Hotel	May	       8232
City Hotel	August	       8983
Resort Hotel	January	2193
Resort Hotel	November	2437
Resort Hotel	December	2648
Resort Hotel	June	       3045
Resort Hotel	February	3103
Resort Hotel	September	3108
Resort Hotel	March	       3336
Resort Hotel	October	3555
Resort Hotel	May	       3559
Resort Hotel	April	       3609
Resort Hotel	July	       4573
Resort Hotel	August	       4894


3. The vast majority of reservations have 2 adults followed by 1 adult.


