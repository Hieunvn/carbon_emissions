# carbon_emissions_analyst

## 1. Introduction
This report aims to analyze carbon emissions to examine the carbon footprint across various industries. We aim to identify sectors with the highest levels of emissions by analyzing them across countries and years, as well as to uncover trends.

![image](https://github.com/user-attachments/assets/2a899357-3545-4f20-bd0d-bb499c635e78)

### 1.1 Data model
The dataset consists of 4 tables containing information regarding carbon emissions generated during the production of goods.

![image](https://github.com/user-attachments/assets/9497a3f7-a968-4924-aa38-00e65761427d)

### 1.2 Data structure
Table "product_emissions"
```sql
SELECT * FROM product_emissions LIMIT 10;
```
Results;
|id|company_id|country_id|industry_group_id|year|product_name|weight_kg|carbon_footprint_pcf|upstream_percent_total_pcf|operations_percent_total_pcf|downstream_percent_total_pcf|
|--|----------|----------|-----------------|----|------------|---------|--------------------|--------------------------|----------------------------|----------------------------|
|10056-1-2014|82|28|2|2014|Frosted Flakes(R) Cereal|0.7485|2|57.50|30.00|12.50|
|10056-1-2015|82|28|15|2015|"Frosted Flakes, 23 oz, produced in Lancaster, PA (one carton)"|0.7485|2|57.50|30.00|12.50|
|10222-1-2013|83|28|8|2013|Office Chair|20.68|73|80.63|17.36|2.01|
|10261-1-2017|14|16|25|2017|Multifunction Printers|110.0|1488|30.65|5.51|63.84|
|10261-2-2017|14|16|25|2017|Multifunction Printers|110.0|1818|25.08|4.51|70.41|
|10261-3-2017|14|16|25|2017|Multifunction Printers|110.0|2274|20.05|3.61|76.34|
|10324-1-2016|15|16|19|2016|KURALON  fiber|1500.0|10000|N/a (product with insufficient stage-level data)|N/a (product with insufficient stage-level data)|N/a (product with insufficient stage-level data)|
|10418-1-2013|84|9|19|2013|Portland Cement|1000.0|1102|N/a (product with insufficient stage-level data)|N/a (product with insufficient stage-level data)|N/a (product with insufficient stage-level data)|
|10661-10-2014|85|28|11|2014|Regular Straight 505® Jeans – Steel (Water<Less™)|0.7665|15|N/a (product with insufficient stage-level data)|N/a (product with insufficient stage-level data)|N/a (product with insufficient stage-level data)|
|10661-10-2015|85|28|6|2015|Regular Straight 505® Jeans – Steel (Water<Less™)|0.7665|15|N/a (product with insufficient stage-level data)|N/a (product with insufficient stage-level data)|N/a (product with insufficient stage-level data)|







## 3. Data Analysis
### 3.1 Which products contribute the most to carbon emissions?
```sql
SELECT product_name,
	ROUND(AVG(carbon_footprint_pcf),2) AS 'Average PCF'
FROM product_emissions
GROUP BY product_name
ORDER BY AVG(carbon_footprint_pcf) DESC
LIMIT 10;
```
Results;
|product_name|Average PCF|
|------------|-----------|
|Wind Turbine G128 5 Megawats|3718044.00|
|Wind Turbine G132 5 Megawats|3276187.00|
|Wind Turbine G114 2 Megawats|1532608.00|
|Wind Turbine G90 2 Megawats|1251625.00|
|Land Cruiser Prado. FJ Cruiser. Dyna trucks. Toyoace.IMV def unit.|191687.00|
|Retaining wall structure with a main wall (sheet pile): 136 tonnes of steel sheet piles and 4 tonnes of tierods per 100 meter wall|167000.00|
|TCDE|99075.00|
|Mercedes-Benz GLE (GLE 500 4MATIC)|91000.00|
|Mercedes-Benz S-Class (S 500)|85000.00|
|Mercedes-Benz SL (SL 350)|72000.00|

### 3.2 What are the industry groups of these products?
```sql
SELECT product_emissions.product_name,
	   ROUND(AVG(product_emissions.carbon_footprint_pcf),2) AS 'Average PCF',
	   industry_groups.industry_group,
	   industry_groups.id
FROM product_emissions
JOIN industry_groups ON(product_emissions.industry_group_id = industry_groups.id)
GROUP BY product_name
ORDER BY AVG(carbon_footprint_pcf) DESC
LIMIT 10;
```
Results;
|product_name|Average PCF|industry_group|id|
|------------|-----------|--------------|--|
|Wind Turbine G128 5 Megawats|3718044.00|Electrical Equipment and Machinery|13|
|Wind Turbine G132 5 Megawats|3276187.00|Electrical Equipment and Machinery|13|
|Wind Turbine G114 2 Megawats|1532608.00|Electrical Equipment and Machinery|13|
|Wind Turbine G90 2 Megawats|1251625.00|Electrical Equipment and Machinery|13|
|Land Cruiser Prado. FJ Cruiser. Dyna trucks. Toyoace.IMV def unit.|191687.00|Automobiles & Components|7|
|Retaining wall structure with a main wall (sheet pile): 136 tonnes of steel sheet piles and 4 tonnes of tierods per 100 meter wall|167000.00|Materials|19|
|TCDE|99075.00|Materials|19|
|Mercedes-Benz GLE (GLE 500 4MATIC)|91000.00|Automobiles & Components|7|
|Mercedes-Benz S-Class (S 500)|85000.00|Automobiles & Components|7|
|Mercedes-Benz SL (SL 350)|72000.00|Automobiles & Components|7|

### 3.3 What are the industries with the highest contribution to carbon emissions?



