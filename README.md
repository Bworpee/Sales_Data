# Sales_Data
## Project report on sales data from XYZ stores

### Introduction

This report aims to analyze the sales data for a non-existence XYZ store, the report should be able to answer shareholders concern about the sales made in the store. The store sales data dates from January, 2019 through to January, 2020, therefore the shareholders now in January, 2020 would want a breakdown of how to store has fared and would want to prepare for upcoming months in 2020.

### Metadata

The sales data from XYZ stores, a .csv file, contain sales from January, 2019 to January, 2020, it has a total of 11 columns and 185951 rows (headers included). A snapshot of the file is shown below for better understanding.

![image](https://github.com/user-attachments/assets/ca8df254-032d-4142-9edc-d6c0fa912a48)

The file has been attached to this repository.

_Note that this dataset was gotten from MeriSkill._

This project would be ordered according to the CISCO Data Analytics Essential Data Analysis Process, which is as follows:

- Asking the question

- Getting the data

- Investigating the data

- Preparing the data

- Analyzing the data

- Presenting the results

Tools used for Analysis: MS Excel and PostgreSQL

### ASKING THE QUESTION

Shareholders at XYZ Store are curious about the store and want to know the numbers the store makes throughout 2019, this is to help them make better data driven decision in the new year and others to come for optimizing sales strategy. Among the questions asked were business KPIs including the current total sales, total order and average cost per sales. The shareholder would also want to know the following

- Sales trend over time in 2019

- The best-selling product and product category in 2019

- Time of the day with highest traffic

- The best performing cities

- A visualization to present findings effectively

### GETTING THE DATA

As was said earlier, the data was gotten from MeriSkill.

### INVESTIGATING THE DATA

The data is generally clean, there are no missing data and no duplicate entry and the data seem relatively clean. But based on the shareholders concerns about XYZ store, more variables would be needed and some edited to be able to get insight from the data. Some variable would not be needed during the analysis. The following is observed:

1. The “Sn”, “Order ID” and “Purchase Address” column would not be needed for this analysis.

2. An “Order Year” column would be drafted out from the “Order Date” column, this would help base the analysis on the year where needed.

3. The “Month” column indicates the month of sales in numbers instead of the actual month name, this would be changed to the month name.

4. The “Hour” column is in a 24hour format and needs to be converted to a 12hour format

5. A “Product Category” column would be drafted out from the “Product” column where the product would be drafted into laptop, washing machine, batteries and so on.

6. Because the method the data was generated is unknown, the “Sales” column would be checked that it is actually the multiplication of the “Quantity Ordered” and “Price Each” column.

### PREPARING THE DATA

Before the data would be worked upon, it is highly advisable that the data be backed up in its natural form so that there is a soft ground to fall back on when needed. Also, to work on the data, it needs to be saved as a .xlsx file in MS Excel to prevent data loss while working.

In preparing the data for analysis, the observations made at the investigating process is dealt with. This is done in the newly saved .xlsx and .csv file of the data.

To follow a sequence, the data would be worked first on MS Excel and then imported to PostgreSQL to continue working on it.

#### MS Excel

Observation 1: “Sn”, “Order ID” and “Purchase Address” column not needed — All three columns were deleted.

Observation 2: Drafting out an “Order Year” column from the “Order Date” column — This was done using the “Text to Column” tool in MS Excel.

Observation 3: The month of sales is in numbers and would be converted to actual month end — This was done using the “=TEXT()” function.

Observation 4: The “Hour” column in 24hour format to be converted to 12hour format — The “=CONCAT()” and “=TEXT()” function were also used here.

The result to these adjustments is shown in the image below:

![image](https://github.com/user-attachments/assets/7802aaca-defb-49e8-bce8-67004c4bf9a2)

The .xlsx file above was also saved in .csv format so as to ease importing into PostgreSQL workbench and is shown below:

```
select *
from sales_data;
```

![image](https://github.com/user-attachments/assets/1c2b574c-65a7-4738-af61-9e8284976cf6)


Observation 5: “Product Category” column drafted out of “Product” column — First the product was categorized with the help of filter in MS Excel into the following:

- Monitor i.e 20in Monitor, 27in 4K Gaming Monitor, 27in FHD Monitor and 34in Ultrawide Monitor

- Charger Cable i.e Lightning Charging Cable and USB-C Charging Cable

- Batteries i.e AA Batteries (4-pack) and AAA Batteries (4-pack)

- Headphones i.e Apple Airpods Headphones, Bose SoundSport Headphones and Wired Headphones

- TV i.e Flatscreen TV

- Phone i.e Google Phone, iPhone and Vareebadd Phone

- Laptop i.e Macbook Pro Laptop and Thinkpad Laptop

- Dryer i.e LG Dryer

- Washing Machine i.e LG Washing Machine

The .csv file was imported to PostgreSQL in order to draft out the “Product Category” column. The result is shown above.

The newly drafted “Product Category” column would by inputted between the “product” column and the “quantity_ordered” column. The SQL case clause was used to achieve this this and is shown below.

The Query:

```
select product, case when product like '%Monitor' 
	then 'Monitor'
	when product like '%Charging Cable' 
	then 'Charging Cable'
	when product like '%Batteries%' 
	then 'Batteries'
	when product like '%Headphones' 
	then 'Headphones'
	when product like '%TV' 
	then 'TV'
	when product like '%Phone' 
	then 'Phone'
	when product like '%Laptop' 
	then 'Laptop'
	when product like '%Dryer' 
	then 'Dryer'
	when product like '%Washing Machine' 
	then 'Washing Machine'
	else 'null'
	end as "Product Catergory", quantity_ordered, price_each, order_date, order_year, month, sales, city, hour
from sales_data;
```
The Result

![image](https://github.com/user-attachments/assets/7d37b6ae-f7ca-4f74-ae1a-84e5332df987)

This was exported back into a .csv file and later added to the .xlsx file.

Observation 6: To confirm the “Sales” column is the multiplication of the “Quantity Ordered” and “Price Each” column, a new column would be temporarily created in the SQL workbench say, “total_sale” from the multiplication of the “Quantity Ordered” and “Price” column and used to confirm that it’s the same with the “Sales” column.

Query to creating the “total_sale” column:

```
select quantity_ordered, price_each, sales, quantity_ordered * price_each as total_sales
from sales_data;
```

![image](https://github.com/user-attachments/assets/3268f777-b394-4720-bb57-00fd16a31543)

Using the Where Clause to confirm:

```
select sale, sub.total_sales
from (select quantity_ordered, price_each, sales, quantity_ordered * price_each as total_sales
from sale_data) sub
where sales != sub.total_sales
```

![image](https://github.com/user-attachments/assets/f6dcf7ef-ea3e-4918-93d5-1a3efe69b550)

The result of the query above returns an empty table which shows that the “sales” and “total_sales” column are exactly the same.

With the Preparation process done, the new .xlsx file takes a new look as shown below:

![image](https://github.com/user-attachments/assets/1c626a78-8cb4-486b-8666-8dcca23e77ae)

### ANALYSING THE DATA AND PRESENTING THE RESULT

Before analyzing the data, the new .xlsx file was meticulous check for accuracy, in order to avoid any forms of error. To do this, filter and sampling at random were used.

After preparing the data for analysis, the data now in .xlsx format, pivot table was used to analyze the data based off what the shareholder concerns were and some of the results are shown below:

#### Sales Trend in 2019

![image](https://github.com/user-attachments/assets/ce66a0ff-fc6d-4b58-8baa-ba942cccded2)

#### Best-Selling product and product category in 2019

![image](https://github.com/user-attachments/assets/71fbaaa3-7c65-4699-a61a-fc21eccce3ad)

![image](https://github.com/user-attachments/assets/f4a5af9f-6a71-4bb5-8e95-4914a87b4259)

#### Time of the day with highest traffic

![image](https://github.com/user-attachments/assets/37f12fab-4ff0-416b-83fa-28bb971a1946)

#### Best performing cities

![image](https://github.com/user-attachments/assets/4853b1ce-7f84-41ee-b9fd-3c1e21e8c624)

From the analysis done the following is observed and recommended

- Batteries tops the best-selling product and product category chart, batteries should always be made available in the store and ready to be delivered upon order.

- San Francisco has the highest number of sales and order while Austin on the contrary has the lowest. More sales campaign is recommended in in cities like Austin, Portland, Seattle, Dallas, Atlanta and Boston while current campaign in San Francisco continues.

- The store has more order between around 7am to 11pm. e-channels should be made more free flowing at these times.

- More sales happens around April, May, October and December.


_Note: The Visuals has been attached in this repository_










