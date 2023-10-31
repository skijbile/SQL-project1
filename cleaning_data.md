What issues will you address by cleaning the data?

	This data set had multiple duplicates in almost all of the tables, unfortunately I was unable to clean the duplicates as there were multiple points of data that although seemed distinct did not make sense in the overall observations.
	Ex. In the all sessions table the same product_name had different product pricing and there was no clear way to know which of the various rows would be the right choice.
	
	Cleaning the data was done on a very basic level in this project just to have certain information in particular formats to make it more readable and easy to understand when perfoming analysis. I did not make any changes to the existing tables but only ran select statements and made observations about unecessary data.
	As a part of starting_with_questions I created views for some of the questions which i feel are also a part of the cleaning process so that i am able to refelct back on a small set of relevant data which i can keep refereing to rather than an enormous amounts of data.
	
	When creating tables a lot of the colums were made using Character varying data type as it threw errors when importing data. hence some of the colums had to be updated later with the right data type.
Ex. date in the analytics table was an integer and then converted to the date format.

	The product categories in the all_sessions table had various different categories all in the same row, hence I created a diffrent table to split those categories to get a generic category across products in order to evaluate . Certain products that do not have any categories were renamed under (not set/ misc) just to be a bit more clear that they were not present in the data set and hence are categorized under miscelleneous.



Queries:
Below, provide the SQL queries you used to clean your data.

1) To split the product category column so that it was easier to show the relation between a product and category for analysis 
		SOURCE : https://www.postgresqltutorial.com/postgresql-string-functions/postgresql-split_part/

	CREATE  TABLE product_categories 
		( 	product_name character varying,
			category1 character varying,
 			category2 character varying
		)
--only taking the first 2 categories into consideration	
	INSERT INTO product_categories
		( SELECT DISTINCT productname AS productname, 
 						SPLIT_PART(productcategory::character varying,'/',1) AS base_category,
						SPLIT_PART(productcategory::character varying,'/',2) AS category
		FROM all_sessions
		order by  productname )

2) change column data for categories to make it more readable and edit irrelevant data based on trend seen in other categories.
	UPDATE product_categories
		SET category2 ='(not set)/misc'
		WHERE category1='(not set)'

	UPDATE product_categories
		SET category1 ='Home'
		WHERE category1='escCatTitle' 

3) To make unavailable cities and country in the same style which would be easier to remember when filtering data 
	UPDATE all_sessions
		SET city ='(not set)'
		where country = '(not set)'  OR  city=  'not available in demo dataset'

4) The usual approach would be to delete rows if it has irrelevant data, but I went with creating a view instead so that the original data remains intact. Following are the queries for both approaches
		SOURCE : https://learnsql.com/cookbook/how-to-delete-a-row-in-sql/

	DELETE FROM all_sessions2 --I had made a duplicate table all_sessions2 to perform this query but dropped it later 
	WHERE totaltransactionrevenue IS NULL

--alternate option is to create a view 

	CREATE OR REPLACE VIEW REVENUE AS 
		SELECT *
		FROM all_sessions
		WHERE totaltransactionrevenue IS NOT NULL

5) To create a table to give a list of product_sku and product_name 

	DROP TABLE IF EXISTS product_details;
	CREATE TABLE product_details
	(

		SKU character varying,
		product_name character varying
	)


	INSERT INTO product_details
		(
			SELECT productsku, productname
			FROM 
				(  SELECT productsku, productname, ROW_number() OVER (partition by productname order by productsku) AS row_number
					FROm all_sessions
					full outer JOIN sales_report USING (productsku)
					WHERE productsku IS NOT NULL AND productname IS NOT NULL
				 	OR productprice IS NOT NULL OR productprice!=0
					order by productname
				)
		WHERE row_number =1	
		 )

6)  Change the column name in the view to differentiate between the 2 sku
		SOURCE : https://www.postgresqltutorial.com/postgresql-tutorial/postgresql-rename-column/

	ALTER VIEW new_sku
	RENAME COLUMN  sku TO New_productsku

7) Change data type in the all_sessions table 
	
	ALTER TABLE all_sessions
	ALTER COLUMN date TYPE date
	USING date :: date
