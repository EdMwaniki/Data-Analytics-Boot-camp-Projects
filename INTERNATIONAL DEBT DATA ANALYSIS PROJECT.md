# Data-Analytics-Boot-camp-Projects

International Debt Analysis Using SQL & Data Visualization

Project Overview
Countries take on debt not just for necessities but to support economic development. Infrastructure spending, for instance, is a costly yet crucial investment for a country’s progress. The World Bank provides debt financing to developing countries to facilitate such initiatives.

In this project, you will analyze international debt data collected by the World Bank. The dataset includes information on the total debt (in USD) owed by developing countries across multiple debt categories from 1970 to 2015.

Your goal is to explore this dataset using SQL in PostgreSQL, extract meaningful insights, and answer key financial questions.

Project Tasks & Deliverables
Connect to the PostgreSQL database using the provided connection string and explore the dataset.

Write SQL queries to find answers to the following key questions:

What is the total amount of debt owed by all countries in the dataset?
How many distinct countries are recorded in the dataset?
What are the distinct types of debt indicators, and what do they represent?
Which country has the highest total debt, and how much does it owe?
What is the average debt across different debt indicators?
Which country has made the highest amount of principal repayments?
What is the most common debt indicator across all countries?
Identify any other key debt trends and summarize your findings.
Document your process: Clearly explain the steps you followed, your query logic, and insights from your findings.

Final Deliverable:

A GitHub repository containing:
A SQL script with all queries used.
A report (Markdown or PDF) summarizing findings with supporting data.



Solution

select * 
from dataanalytics.international_debt id;

-- 1.What is the total amount of debt owed by all countries in the dataset?
-- Answer: 3,079,735,000,000
-- The aim of the question is to find the overall average debt amount collectively owed by the countries
-- The query to find the overall average involves using the aggregate SUM function on the debt column to find the total debt
-- Finally, for better readability, divide the total debt by 1000000 to show the figure in terms of millions instead of trillions and limit it to two decimal places using the ::numeric() function.

select sum(debt) as total_debt
from dataanalytics.international_debt id;


-- 2.How many distinct countries are recorded in the dataset?
-- Answer:- 124 countries
-- The aim of the question is to identify the number of unique countries in the dataset
-- The query to find the number of unique countries involves using the DISTINCT and COUNT functions simultaneously on the country name column as shown below:

select count(distinct(country_name)) as distinct_countries
from dataanalytics.international_debt id;


-- 3.What are the distinct types of debt indicators, and what do they represent?
-- Answer: - 25 unique debt indicators
-- The question aims to identify the unique debt indicator names from the dataset and their meanings
-- The query for this involves using the DISTINCT function on the indicator name column to extract the unique values as shown below

select distinct(indicator_name)
from dataanalytics.international_debt id;

-- Principal repayments on external debt, private nonguaranteed (PNG) (AMT, current US$)
-- PPG, bonds (INT, current US$)
-- Interest payments on external debt, long-term (INT, current US$)
-- PPG, multilateral (INT, current US$)
-- PPG, commercial banks (AMT, current US$)
-- PPG, official creditors (AMT, current US$)
-- PPG, bonds (AMT, current US$)
-- Interest payments on external debt, private nonguaranteed (PNG) (INT, current US$)
-- PPG, bilateral (AMT, current US$)
-- PPG, private creditors (INT, current US$)
-- PPG, other private creditors (DIS, current US$)
-- PPG, official creditors (INT, current US$)
-- Disbursements on external debt, long-term (DIS, current US$)
-- PPG, multilateral (DIS, current US$)
-- PPG, bilateral (INT, current US$)
-- PPG, official creditors (DIS, current US$)
-- Principal repayments on external debt, long-term (AMT, current US$)
-- PPG, bilateral (DIS, current US$)
-- PPG, private creditors (AMT, current US$)
-- PPG, commercial banks (DIS, current US$)
-- PPG, other private creditors (INT, current US$)
-- PPG, multilateral (AMT, current US$)
-- PPG, commercial banks (INT, current US$)
-- PPG, private creditors (DIS, current US$)
-- PPG, other private creditors (AMT, current US$)


-- 4.Which country has the highest total debt, and how much does it owe?
-- Answer: China - 285,793,520,000
-- The question aims to retrieve the country that owes the most amount of debt from the dataset
-- The query for this should return the country name and the total debt owed by each country. 
-- This is done by using the aggregate SUM function on the debt column 
-- Then group the result by country name, 
-- Order the result by the total debt owed in a descending manner
-- Finally, limiting the result to only the top 1 record

select country_name, sum(debt) as total_debt
from dataanalytics.international_debt id 
group by country_name
order by total_debt desc
limit 1; 


-- 5.What is the average debt across different debt indicators?
-- Answer: 
-- The question aims to find out how much average debt each unique debt indicator carries
-- The query involves using the DISTINCT function on the indicator name and the aggregate SUM function on the debt column
-- The GROUP BY function is used to group the result by the distinct indicator name
-- Finally, the result is sorted by the average debt per indicator name using the ORDER BY function

select distinct(indicator_name), avg(debt)::numeric(20,2) as avg_debt
from dataanalytics.international_debt id
group by distinct(indicator_name)
order by avg_debt DESC;


-- 6.Which country has made the highest amount of principal repayments?
-- Answer: China - 96,218,620,000
-- The question aims to uncover the country that has repaid the most amount of debt owed
-- Two queries were used in this instance:- 
-- 1.the first one to find the indicator code for the debt indicator showing principal repayments on long term external debt 
-- 2.the second one to find the country with the highest principal repaymenst on long term external debt owed
-- The first query involved using the WHERE and LIKE clause to filter the indicator name and indicator code columns inorder to return only the records with "Principal repayments" in them  
-- The second query involved using the indicator code obtained from the first query to filter the country name and debt columns to return only the countries and their principal repayment amounts on long term external debt
-- The SUM, GROUP BY and ORDER BY functions were used in the second query as shown below:

select indicator_name, indicator_code 
from dataanalytics.international_debt id 
where indicator_name like 'Principal repayments%';


select country_name, sum(debt) as total_debt
from dataanalytics.international_debt id 
where indicator_code = 'DT.AMT.DLXF.CD'
group by country_name
order by total_debt desc;



-- 7.What is the most common debt indicator across all countries?
-- Answer: 
-- The question aimed to identify the debt indicator that appeared most times in the dataset
-- The query used for this should return three columns - indicator name, indicator code and count of indica
-- The COUNT function was used to aggregate the number of records in the dataset
-- The GROUP BY function was used to group the result by indicator name and indicator code
-- The ORDER BY fucntion was used to sort the result by the count of records
-- Finally, the LIMIT function was used to restrict the result to only the top 1 record 

select indicator_name, indicator_code, count(*) as indicator_count
from dataanalytics.international_debt id 
group by indicator_name, indicator_code
order by indicator_count desc
limit 1;



-- 8.Identify any other key debt trends and summarize your findings.

-- (a) What was the average amount of debt owed by all countries?

-- Identified the columns to work with - the debt column
-- Used the aggregate average function (AVG) on the debt column
-- Renamed the column to avg_debt for easier readability
-- Finally, limited the result to only two decimal points using the ::numeric(20,2) clause

select avg(debt)::numeric(20,2) as avg_debt
from dataanalytics.international_debt id;



-- (b) Which countries had the highest and least interest payments for long term debt?

-- Answer: Highest - Mexico (19,267,967,000), Lowest - Comoros (937,189.90)
-- Step 1 - write a query to identify the code that represents the indicator name that has interest repayments bu selecting the indicator_name and indicator_code columns and then filtering using the WHERE and LIKE clauses
-- Step 2 - write a query to get the country with the highest interest repayments by using the WHERE, GROUP BY, ORDER BY and LIMIT clauses
-- Step 3 - write a query to get the country with the lowest interest repayments by using the WHERE, GROUP BY, ORDER BY and LIMIT clauses

select indicator_name, indicator_code
from dataanalytics.international_debt id 
where indicator_name like 'Interest%';

select country_name, sum(debt) as total_interest_payment
from dataanalytics.international_debt id 
where indicator_code = 'DT.INT.DLXF.CD'
group by country_name
order by total_interest_payment desc
limit 1;

select country_name, sum(debt) as total_interest_repayment
from dataanalytics.international_debt id 
where indicator_code = 'DT.INT.DLXF.CD'
group by country_name
order by total_interest_repayment asc
limit 1;


KEY FINDINGS

-- 1. The most common type of debt opted for by countries was was PPG multilareal, which stands for Public and Publicly Guaranteed Multilateral debt. This is a type of low interest, long term credit facility offered by international finance institutions such as the World Bank, International Monetary Fund (IMF), African Development Bank (AfDB) etc to multiple countries.

-- 2. The debt indicator with the highest average amount of debt was the Principal repayments on external debt, long-term (AMT, current US$) while the debt indicator with the lowest average amount of debt was the PPG, other private creditors (INT, current US$). 

-- 3. Some debt indicators had zero debt allocated to them. Among these, the indicator with the highest number of zero debt records was PPG, bonds (AMT, current US$)


CONCLUSIONS:

-- 1. Most countries preferred taking the PPG multilateral debt compared to other debt types. This could be attributed to the fact that the PPG multilateral debt is generally lower interest and longer term in nature, thus being more attractive to countries as the repayment terms are more favourable. 

-- 2. The Principal repayments on external debt, long-term (AMT, current US$) indicator had the highest average debt across all countries. This shows that most countries had borrowed externally and that most of their debt repayments went towards paying back the principal amount rather than the interest accrued.

-- 3. The findings revealed that some of the debt indicators had zero debt assigned to them. This could point towards either the countries in question having already cleared that particular debt owed or having never taken the debt in the first place. Further investigation may be required in this instance using more data.


