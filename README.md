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



--list all data, find out NULL value (group by is a good way to find NULL value)
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

--group by adults

select adults, COUNT(*) as number
from [0023Orders]..[hotel booking]
group by adults
order by number desc

output: 

adults	number
2	89680
1	23027
3	6202
0	403
4	62
26	5
5	2
27	2
20	2
10	1
50	1
6	1
40	1
55	1


4. the more than 90% of reservation are adults. One child is slightly larger in percentage than two children. (recognize NULL value again here in column of children)

--group by children

DELETE 
FROM [0023Orders]..[hotel booking]
 WHERE children is NULL

select children, COUNT(*) as number
from [0023Orders]..[hotel booking]
group by children
order by number desc


output: 

children	number
0	       110796
1	       4861
2	       3652
3	       76
10	       1


5. around 90% of reservation do not require a parking space.

--group by required parking spaces 
select required_car_parking_spaces, COUNT(*) number
from [0023Orders]..[hotel booking]
group by required_car_parking_spaces
order by number desc


output: 

required_car_parking_spaces	 number
0	                      111970
1	                      7383
2	                      28
3	                      3
8	                      2


6. August 2016 saw a big spike in parking space requests. Could be a problem in the future if parking capacity is low. （understand the business problem, know how to translate the business needs into query logics, use function of SUM or COUNT）

--group by month, read required parking spaces

select arrival_date_year, arrival_date_month,sum (required_car_parking_spaces)as number
from [0023Orders]..[hotel booking]
group by arrival_date_year, arrival_date_month
order by arrival_date_year, number desc



output: 

arrival_date_year	arrival_date_month	number
2015	October	250
2015	August	248
2015	December	231
2015	July	216
2015	September	215
2015	November	192
2016	August	451
2016	October	380
2016	July	376
2016	March	357
2016	May	334
2016	September	332
2016	April	319
2016	June	314
2016	December	271
2016	February	267
2016	November	265
2016	January	209
2017	April	314
2017	August	301
2017	June	295
2017	January	282
2017	May	280
2017	July	277
2017	March	246
2017	February	242


7. the Average Daily Rate measures the average rental revenue earned for an occupied room per day. This plot shows the monthly total for APR rises from around May to October then dips down again.

--monthly total ADR, group by month, select sum (ADR)
--change the month column data type into int from varchar

select arrival_date_year, arrival_date_month, SUM(adr) as totalADR
from [0023Orders]..[hotel booking]
group by arrival_date_year, arrival_date_month
order by arrival_date_year, datepart(mm,arrival_date_month+',1900')

output: 

arrival_date_year	arrival_date_month	totalADR
2015	July	       271588.059999999
2015	August	       411815.61
2015	September	484902.64
2015	October	391084.629999998
2015	November	141757.79
2015	December	216311.39
2016	January	145597.69
2016	February	272767.929999999
2016	March	       381430.43
2016	April	       482651.900000001
2016	May	       528075.290000002
2016	June	       566123.950000002
2016	July	       573715.300000001
2016	August	       723470.270000002
2016	September	618959.160000001
2016	October	589978.460000002
2016	November	359605.179999998
2016	December	333389.149999999
2017	January	271574.110000001
2017	February	320893.869999999
2017	March	       408746.019999999
2017	April	       630470.679999999
2017	May	       753553.539999994
2017	June	       710153.159999997
2017	July	       759959.669999992
2017	August	       808923.219999996



Findings and Recommendations


City hotels have around double the amount of reservations than resort hotels.
Both city and resort hotels reservations peak in August are the lowest in January. March through October seems to be their busiest window for the year. reservations fall in from November to January.
The vast majority of reservations have 2 adults followed by 1 adult.
The more than 90% of reservation are adults. One child is slightly larger in percentage than two children.
Around 90% of reservation do not require a parking space
August 2016 saw a big spike in parking space requests. Could be a problem in the future if parking capacity is low.
the Average Daily Rate measures the average rental revenue earned for an occupied room per day. This plot shows the monthly total for APR rises from around May to October then dips down again.
My recommendations would be to target couples during the spring, summer and part of fall. Focus especially on the summer months as they bring in the best average daily rates. Resort hotels may have to increase their advertising efforts compared to city hotels. I would also make sure there are enough parking spaces during the summer months. With a surge of demand of spaces it would prudent to make sure that they have the capacity or a plan in place for any overflow.



