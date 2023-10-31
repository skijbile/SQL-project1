Question 1:  Determine how many customers did not enter their geographical data

SQL Queries:

WITH CTE_customerdata AS 

		(SELECT country,city,COUNT( DISTINCT fullvisitorid) AS customer_count
		FROM new_sku
		WHERE city ='(not set)'
		GROUP BY country,city
		)

SELECT SUM(customer_count)
FROM CTE_customerdata



Answer: 8189 customers did not enter their city information from the 14223 distinct customers.



Question 2:  Determine which countries have purchased products across a minimum of 10 categories. Is there any country/city where visitors have purchased products in all categories

SQL Queries:

	SELECT als.country,als.city, COUNT(DISTINCT pc.category2)
	FROM all_sessions als
	JOIN product_categories pc
	ON als.productname = pc.product_name
	WHERE als.country!='(not set)'
	GROUP BY als.country, als.city
	HAVING COUNT(DISTINCT pc.category2) >=10 AND COUNT(DISTINCT pc.category2)<=19
	ORDER BY als.country

-- to get total number of categories
-- 	SELECT DISTINCT ( COUNT category2)
--	FROM product_categories 


Answer: The total output is 143 countries with United States being the only country having products purchased across all the categories with 3 cities that can defined under it. 



Question 3: What percentage of source is brings customers to the website.

SQL Queries:

	WITH customer_traffic AS (
		SELECT COUNT(DISTINCT fullvisitorid) AS amount, channelgrouping
		FROM all_sessions
		GROUP BY channelgrouping 
		)

	SELECT channelgrouping, 100*amount / (SELECT sum(amount) FROM customer_traffic) AS percentage
	FROM customer_traffic
	ORDER BY percentage DESC

Answer: More than 50% of customer traffic comes from organic search which i feel is a good start and a very small group of customers come from paid searches which in the overall picture is quite economical in a marketing sense



Question 4: Which source brings in the most revenue

SQL Queries:

--OPTION 1 : When we do not have the required information to calculate revenue for all the customers  

	WITH customer_traffic AS (
		SELECT COUNT(DISTINCT fullvisitorid) AS amount, channelgrouping, SUM(totaltransactionrevenue) AS revenue
		FROM all_sessions
		GROUP BY channelgrouping 
	)

	SELECT revenue/1000000  AS revenue_in_million,channelgrouping, 
			ROUND((100*amount / (SELECT sum(amount) FROM customer_traffic)),2) AS percentage
	FROM  customer_traffic
	ORDER BY  revenue DESC

OPTION 2 : When we only use the subset where total transaction revenue is not NULL

	WITH customer_traffic AS (
		SELECT COUNT(DISTINCT fullvisitorid) AS amount, channelgrouping, SUM(totaltransactionrevenue) AS revenue
		FROM all_sessions
		WHERE totaltransactionrevenue IS NOT NULL
		GROUP BY channelgrouping 
		)

	SELECT revenue/1000000  AS revenue_in_million,channelgrouping, 
			ROUND((100*amount / (SELECT sum(amount) FROM customer_traffic)),2) AS percentage
	FROM customer_traffic
	ORDER BY revenue DESC


Answer: From the above query you can see that most of the revenue comes from referral sites. When comparing this to question 3 where we observed that organic search is the biggest source for customer traffic but clearly it only generated half the revenue in comparision to referral sites.


Question 5: 

SQL Queries:

Answer:
