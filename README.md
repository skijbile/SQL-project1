# Final-Project-Transforming-and-Analyzing-Data-with-SQL

## Project/Goals

	Through this project I want to solidify my base knowlegde on SQL and to challenge myself to find various methods to aquire the results to the questions using more complex clauses/statements and tools so that I can get more comfortable and understand the best ways to implement them.

## Process

	The first couple of days went into understanding the data, as we were used to working with the northwind database I thought the expectation was to clean the database to that level but it kept throwing multiple errors. Understanding the various tables and columns is still a process. There were many points of errors throught out the database with incorrect/missing and duplicate data.
	After that I switched my aproach and started working on the questions that were a part of the project and started working on cleaning data that I came across in that process. In this process I created new tables and views to make the dataset for analysis smaller for easy computing.

## Results

	In terms of data,this data base was quite big and very inconsistent, which forced me to make certain assumptions and work based on that as I had no other channel to get the right/relevant information to make changes/updates to the existing tables. This data gives us information on the buying capacity of various customers from different countries, divided across the various products. A lot of assupmtions were made in order to fulfil the analysis process and calculations were made on those assumptions and tables were joined to acquire details on all distinct products.   

## Challenges 

	The dataset was not very clean with a lot of duplicates. The biggest challenge was to understand how to go about the entire process of cleaning and the QA process and as I dugged deeper into the data it was getting more frustrating as knowing what to do theoritically was very much different from implementing it. 
	Importing of data into the database was also throwing errors as the data types used were not matching the csv files. I tried putting the right data type so it was less casting while doing the analysis and it worked for the most part . Some column names did not match the expected  data type Ex. visit_start_time, it was set to integer and upon further review I found out that the data in that colum was a repetition of the visit_id and had nothing to do with data type timestamp.
	Generating primary key for each table possed a challenge as well, as the product_sku which was available in all tables looked like to be the choice but on further reviewing I discovered that that column did not have the reliable data to create a unique identifier. 
	One product name had multiple Sku which forced me to get distinct products and assign a SKU in order to analyze the different tables and gather the most information,and it was a longer process to create duplicates of tables just so there was no error made in cleaning data as there was no going back after making any updates/alterations.
	There were a lot of tables with common column name but due to the unclean nature of the data it was getting difficult to understand the relation of those columns as the values did not match or were missing. Ex. the unitprice column in analytics and the productprice column in all_sessions has different values so again to best suit the need I made a choice of selecting the price for a particular product that was common in both the columns.
	There were other challenges like backing up the files, it was not as simple as just using the PgAdmin interface, I had to manually back it up from command prompt for some reason.

## Future Goals

	I would have liked to clean up the tables a bit more and done a better QA process on all the columns to understand the data more clearly and use certain other clauses to refine the data further and get a stronger hold to ask the right questions. 

