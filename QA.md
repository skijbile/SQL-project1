What are your risk areas? Identify and describe them.

	The biggest risk area was that there was a lot of  NULL values accross multiple tables making the process of analysis very difficult. Not being able to refer a resource to get a better looking dataset was quite upsetting and I made many assumptions and tried to include as much data points as possible .

	Apart from incomplete data, duplicates also possed an issue as the same product name had multiple product sku with no definative deferentiation to base its accuracy on which lead me to go with product name as distinct feature and join tables to get product sku in order to perform analysis.

	While there was data that could not be verified there was wrong data found in the city column of the all_sessions table as one city was found under different countries making it difficult to rely on both the geographical aspects together to find information about the customers and also a purchasing pattern.





QA Process:
Describe your QA process and include the SQL queries used to execute it.

1) to verify the city column as they were under the wrong countries 

	SELECT DISTINCT als1.fullvisitorid,als1.country,als1.city
	FROM all_sessions als1
	JOIN all_sessions als2 USING(city)
	WHERE als1.country != als2.country AND city !='(not set)'
	order  BY als1.country

2) to verify that all products from the all_sessions table is assigned with an SKU in the new product details table 
	SELECT DISTINCT productname
	FROM all_sessions

	SELECT COUNT(DISTINCT product_name)
	FROM product_details

Output for both is 471 rows.

3)  To check how many countries are not available in the data set to ensure that it is not a significant amount to skew the analysis 

	SELECT count(country)
	FROM all_sessions
	where country ='(not set)'

Output : 24 rows have no country set out of  15134 distinct rows.

4)  To check if the full visitor id is same and available in the tables all_sessions and analytics
-- for this QA i took reference of the approach in one of our lectures with Joseph Luiz, I performed a simpler query which is is the CTE, looking at the result its quite obvious that the full visitor id in both tables are different but i went ahead and wrote a query to give out a final answer rather than basing it on an observation.

	WITH customer_id AS (
		SELECT COUNT(*) AS count,
		'fullvisitorid' AS customer,
		COUNT(distinct fullvisitorid) AS customer_count
		FROM all_sessions
		
		UNION
	
		SELECT COUNT(*),
		'fullvisitorid' ,
		COUNT(distinct fullvisitorid) 
		FROM analytics
		),

		QA_for_customer AS (
		SELECT COUNT(DISTINCT customer_count) AS QA_result
		FROM customer_id
		)
	
	SELECT (
		CASE
		WHEN QA_result =1 THEN 'PASS'
		ELSE 'FAIL'
		END ) AS final_output
	FROM QA_for_customer

Output : Fail



