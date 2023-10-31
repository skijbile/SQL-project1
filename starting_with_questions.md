Answer the following questions and provide the SQL queries used to find the answer.

    
**Question 1: Which cities and countries have the highest level of transaction revenues on the site?**


SQL Queries:

SELECT country, city, (SUM (totaltransactionrevenue)/1000000)|| ' '||'million' AS revenue 
FROM all_sessions
WHERE totaltransactionrevenue IS NOT NULL
GROUP BY country, city
order by SUM(totaltransactionrevenue) DESC





Answer: overall United states has the highest level of transaction revenues which come from different parts of the country. due to the incomplete nature of the data this result gives us only a few cities from the United States where revenue generated is high but. The highest amount of revenue does not specify the cities but gives us the result of United States being the biggest contributor
This data is also generated from a subset of data (81 outputs) which is available as revenue under views where the total transaction revenue is available. 




**Question 2: What is the average number of products ordered from visitors in each city and country?**


SQL Queries:

 WITH visitor_count as(
		SELECT country ,COUNT(distinct fullvisitorid) AS visitors_in_country
		FROM all_sessions 
		GROUP by  country
		ORDER BY country
		),
		
 	product_count as(
		SELECT country ,COUNT( productname) AS counts
		FROM all_sessions
		GROUP by  country
		ORDER BY country
		)

-- to calculate the average number of products ordered by each customer in a particular country
SELECT pc.country, ROUND(avg(counts/visitors_in_country),2)  AS average_count
FROM product_count pc
JOIN visitor_count vc USING(country)
group by pc.country
order by average_count DESC


Answer: due to the incomplete nature of the data set with ciites and improper allocations of cities under countries(eg New York is shown under Canada and United States) the above solution is derived on the basis of country only to give an estimate idea of the average products purchased in a particular country by a visitor.
In the QA process it was verified that most of the countries had no repeat customers based on the fullvisitorid.





**Question 3: Is there any pattern in the types (product categories) of products ordered from visitors in each city and country?**


SQL Queries:

 

SELECT country, category, count
FROM 

(
	SELECT als.country AS country,
 		pc.category2 AS category, 
 		COUNT (pc.category2) AS count,
 		RANK() OVER (partition by country ORDER BY COUNT (pc.category2) DESC) AS category_ranking
FROM product_categories pc
JOIN all_sessions als
ON pc.product_name = als.productname
WHERE country != '(not set)' 
GROUP BY  category2,als.country
order by country,  category_ranking DESC
)

WHERE  category_ranking =1



Answer: As mentioned previously, the data for cities is unreliable hence the distribution of categories is shown only over countries excluding the data points where country is not set which is only 24 in total. Hence excluding it from our observation does not make a drastic change, however there are categories that are not defined(blank) but for most countries it is only a small count of products purchased that is not associated with a category. 
following the count column you will be able to see which category is the most favoured in the countries that are present in our data set.





**Question 4: What is the top-selling product from each city/country? Can we find any pattern worthy of noting in the products sold?**


SQL Queries:

SELECT country, productname
FROM 
		(SELECT country, productname, count(productname) ,
		 		DENSE_RANK() OVER(partition by country order by count(productname) DESC ) AS most_bought_product
		FROM all_sessions
		group by country, productname
		order by country
		) 
WHERE most_bought_product=1
GROUP BY country,productname
order by count(productname)






Answer: A lot of the countries have multiple top-selling product. However there is no clear way to note a patterns as a product is defined under multiple categories but shopping by Brand is seen as an influencer after going through the output of the query as most product names are defined with big brands of google, youtube,android etc.  





**Question 5: Can we summarize the impact of revenue generated from each city/country?**

SQL Queries:

DROP VIEW IF EXISTS total_revenue;

CREATE VIEW total_revenue AS ( 

	SELECT DISTINCT *, RANK() OVER (PARTITION BY productname order by totalorders DESC) AS ranking
	FROM 
			(SELECT als.fullvisitorid,als.productname,als.country ,
			 		ROUND(((als.productprice*1.0)/1000000),2) AS productprice,
			 		ROUND ( ((a.unitprice*1.0)/1000000),2) AS unitprice,
			 		sr.totalordered AS totalorders
				FROM all_sessions als
			 	JOIN analytics a USING(fullvisitorid)
				FULL OUTER JOIN sales_report sr
				ON sr.name =als.productname
				WHERE 
			 	sr.totalordered IN
			 			(CASE
						 WHEN sr.totalordered IS NULL THEN 0
						 ELSE sr.totalordered
						 END) AND
					 als.productprice =
			 			(CASE 
			 			WHEN als.productprice=a.unitprice THEN als.productprice
			 			WHEN als.productprice =0 THEN a.unitprice
						 WHEN als.productprice IS NULL THEN 0
			 			END )
			 			
			 	order by als.productname
 			)
	
	
	)
	

-- Option 1 to reflect the revenue generated from each city and country
SELECT als.country,als.city,SUM(tr.productprice*tr.totalorders)  AS revenue_in_millions
FROM total_revenue tr
JOIN all_sessions als 
ON als.fullvisitorid= tr.fullvisitorid
WHERE ranking =1
GROUP BY als.country,als.city
ORDER BY als.country,revenue_in_millions DESC

--Option 2 to reflect the overall revenue across the various countries.

SELECT als.country,SUM(tr.productprice*tr.totalorders)  AS revenue_in_millions
FROM total_revenue tr
JOIN all_sessions als 
ON als.fullvisitorid= tr.fullvisitorid
WHERE ranking =1
GROUP BY als.country
ORDER BY revenue_in_millions DESC


Answer: This revenue genertated in  the above query is  based on the assumption that the sales_report table has the quantity ordered from the visitors of each country for each product. 

OPTION 1
You can see that there is revenue generated from countries that are unavailable in the data set which is around 350 million and that is something that is needed to make a more accurate analysis of the revenue impact. Also there are a lot of cities that are not defined with brings us back to relying on countries.However that being said there are certain cities that have not generated any revenue.

OPTION 2 
This allows to refelct on the revenue based on countries and while Unites States is the highest revenue generating country, there are a few countries that have not been not generated any revenue.






