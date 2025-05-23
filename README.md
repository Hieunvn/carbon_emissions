# Carbon_emissions_analyst

## 1. Introduction
This report aims to analyze carbon emissions to examine the carbon footprint across various industries. We aim to identify sectors with the highest levels of emissions by analyzing them across countries and years, as well as to uncover trends.

### 1.1 Data model
The dataset consists of 4 tables containing information regarding carbon emissions generated during the production of goods.

![image](https://github.com/user-attachments/assets/9497a3f7-a968-4924-aa38-00e65761427d)

### 1.2 Data structure
Table "product_emissions"
```sql
SELECT * FROM product_emissions LIMIT 10;
```
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

Table "companies"
```sql
SELECT * FROM companies LIMIT 5;
```
|id|company_name|
|--|------------|
|1|"Autodesk, Inc."|
|2|"Casio Computer Co., Ltd."|
|3|"Cisco Systems, Inc."|
|4|"CNX Coal Resources, LP"|
|5|"Coca-Cola Enterprises, Inc."|

Table "countries"
```sql
SELECT * FROM countries LIMIT 5;
```
|id|country_name|
|--|------------|
|1|Australia|
|2|Belgium|
|3|Brazil|
|4|Canada|
|5|Chile|

Table "industry_groups"
```sql
SELECT * FROM industry_groups LIMIT 5;
```
|id|industry_group|
|--|--------------|
|1|"Consumer Durables, Household and Personal Products"|
|2|"Food, Beverage & Tobacco"|
|3|"Forest and Paper Products - Forestry, Timber, Pulp and Paper, Rubber"|
|4|"Mining - Iron, Aluminum, Other Metals"|
|5|"Pharmaceuticals, Biotechnology & Life Sciences"|

## 2. Data explore
Data dulpicate
```sql
SELECT *,
	COUNT(*) AS duplicate_count
FROM product_emissions
GROUP BY
	id,
	company_id,
	country_id,
	industry_group_id,
	year,
	product_name,
	weight_kg,
	carbon_footprint_pcf,
	upstream_percent_total_pcf ,
	operations_percent_total_pcf ,
	downstream_percent_total_pcf 
HAVING duplicate_count > 1
LIMIT 10;
```
|id|company_id|country_id|industry_group_id|year|product_name|weight_kg|carbon_footprint_pcf|upstream_percent_total_pcf|operations_percent_total_pcf|downstream_percent_total_pcf|duplicate_count|
|--|----------|----------|-----------------|----|------------|---------|--------------------|--------------------------|----------------------------|----------------------------|---------------|
|10056-1-2014|82|28|2|2014|Frosted Flakes(R) Cereal|0.7485|2|57.50|30.00|12.50|2|
|10056-1-2015|82|28|15|2015|"Frosted Flakes, 23 oz, produced in Lancaster, PA (one carton)"|0.7485|2|57.50|30.00|12.50|2|
|10222-1-2013|83|28|8|2013|Office Chair|20.68|73|80.63|17.36|2.01|2|
|10261-1-2017|14|16|25|2017|Multifunction Printers|110.0|1488|30.65|5.51|63.84|2|
|10261-2-2017|14|16|25|2017|Multifunction Printers|110.0|1818|25.08|4.51|70.41|2|
|10261-3-2017|14|16|25|2017|Multifunction Printers|110.0|2274|20.05|3.61|76.34|2|
|10324-1-2016|15|16|19|2016|KURALON  fiber|1500.0|10000|N/a (product with insufficient stage-level data)|N/a (product with insufficient stage-level data)|N/a (product with insufficient stage-level data)|2|
|10418-1-2013|84|9|19|2013|Portland Cement|1000.0|1102|N/a (product with insufficient stage-level data)|N/a (product with insufficient stage-level data)|N/a (product with insufficient stage-level data)|2|
|10661-1-2014|85|28|11|2014|501® Original Jeans – Dark Stonewash|0.997|16|N/a (product with insufficient stage-level data)|N/a (product with insufficient stage-level data)|N/a (product with insufficient stage-level data)|2|
|10661-1-2015|85|28|6|2015|501® Original Jeans – Dark Stonewash|0.997|16|N/a (product with insufficient stage-level data)|N/a (product with insufficient stage-level data)|N/a (product with insufficient stage-level data)|2|

Duplicate result
```sql
SELECT COUNT(product_name ) AS Total_number_of_pro,
	COUNT(DISTINCT product_name ) AS Number_of_unique_pro
FROM product_emissions;
```
|Total_number_of_pro|Number_of_unique_pro|
|-------------------|--------------------|
|1037|661|

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

Result => The Wind Turbines contribute the most to carbon emissions.

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

Result => The products that contribute the most to carbon emissions are from 'Electrical Equipment and Machinery' industry_group.

### 3.3 What are the industries with the highest contribution to carbon emissions?
```sql
SELECT 
	ROUND(sum(product_emissions.carbon_footprint_pcf),2) AS Total_PCF,
	product_emissions.product_name,
	product_emissions.industry_group_id,
	industry_groups.industry_group
FROM product_emissions
JOIN industry_groups ON(industry_groups.id = product_emissions.industry_group_id )
GROUP BY industry_group 
ORDER BY Total_PCF DESC
LIMIT 5;
```
|Total_PCF|product_name|industry_group_id|industry_group|
|---------|------------|-----------------|--------------|
|9801558.00|ACTI9 IID K 2P 40A 30MA AC-TYPE RESIDUAL CURRENT CIRCUIT BREAKER|13|Electrical Equipment and Machinery|
|2582264.00|VW Polo V 1.6 TDI BlueMotion Technology|7|Automobiles & Components|
|577595.00|KURALON  fiber|19|Materials|
|363776.00|Multifunction Printers|25|Technology Hardware & Equipment|
|258712.00|Office Chair|8|Capital Goods|

![image](https://github.com/user-attachments/assets/4e0bc57c-e763-4f19-841d-414b8d5453c1)

Result => The industries with the highest contribution to carbon emissions are Electrical Equipment and Machinery.

### 3.4 What are the companies with the highest contribution to carbon emissions?
```sql
SELECT 
	round(sum(product_emissions.carbon_footprint_pcf),2) AS Total_pcf,
	companies.company_name
FROM product_emissions
JOIN companies ON(companies.id = product_emissions.company_id )
GROUP BY company_name 
ORDER BY Total_pcf DESC
LIMIT 5;
```
|Total_pcf|company_name|
|---------|------------|
|9778464.00|"Gamesa Corporación Tecnológica, S.A."|
|1594300.00|Daimler AG|
|655960.00|Volkswagen AG|
|212016.00|"Mitsubishi Gas Chemical Company, Inc."|
|191687.00|"Hino Motors, Ltd."|

![image](https://github.com/user-attachments/assets/5e49c22c-e72f-4304-a2bd-29958d32d50d)

Result => The companies with the highest contribution to carbon emissions are Gamese Corporacion Tecnologica, S.A.

### 3.5 What are the countries with the highest contribution to carbon emissions?
```sql
SELECT 
	round(sum(product_emissions.carbon_footprint_pcf),2) AS Total_pcf,
	countries.country_name
FROM product_emissions
JOIN countries ON(countries.id = product_emissions.company_id )
GROUP BY country_name 
ORDER BY Total_pcf DESC
LIMIT 5;
```
|Total_pcf|country_name|
|---------|------------|
|9778464.00|Germany|
|212016.00|Lithuania|
|191687.00|Greece|
|132012.00|Japan|
|105600.00|Colombia|

![image](https://github.com/user-attachments/assets/e0b0f4fa-3ebe-4e4d-acca-6933cbc47641)

Result => The countries with the highest contribution to carbon emissions are Germany.

### 3.6 What is the trend of carbon footprints (PCFs) over the years?
```sql
SELECT 
	year, 
	ROUND(SUM(carbon_footprint_pcf),2) AS Total_pcf
FROM product_emissions
GROUP BY year
ORDER BY year;
```
|year|Total_pcf|
|----|---------|
|2013|503857.00|
|2014|624226.00|
|2015|10840415.00|
|2016|1640182.00|
|2017|340271.00|

![image](https://github.com/user-attachments/assets/2301e1da-d82a-4b43-b7d9-b6879456583e)

Result => This data does not show a trend in carbon footprints over the year. However, I can see that the total carbon footprint had the highest increase in 2015 and the lowest in 2017.

### 3.7 Which industry groups has demonstrated the most notable decrease in carbon footprints (PCFs) over time?
```sql
SELECT
	ig.industry_group AS 'Industry Group',
	ROUND(SUM(CASE WHEN pe.year = 2013 THEN pe.carbon_footprint_pcf ELSE 0 END), 2) AS '2013 Emission',
	ROUND(SUM(CASE WHEN pe.year = 2014 THEN pe.carbon_footprint_pcf ELSE 0 END), 2) AS '2014 Emission',
	ROUND(SUM(CASE WHEN pe.year = 2015 THEN pe.carbon_footprint_pcf ELSE 0 END), 2) AS '2015 Emission',
	ROUND(SUM(CASE WHEN pe.year = 2016 THEN pe.carbon_footprint_pcf ELSE 0 END), 2) AS '2016 Emission',
	ROUND(SUM(CASE WHEN pe.year = 2017 THEN pe.carbon_footprint_pcf ELSE 0 END), 2) AS '2017 Emission'
FROM product_emissions AS pe
LEFT JOIN industry_groups AS ig ON ig.id = pe.industry_group_id
GROUP BY ig.industry_group
ORDER BY 
	'2013 Emission'
	'2014 Emission'
	'2015 Emission'
	'2016 Emission'
	'2017 Emission';
```
|Industry Group|2013 Emission|2014 Emission|2015 Emission|2016 Emission|2017 Emission|
|--------------|-------------|-------------|-------------|-------------|-------------|
|"Food, Beverage & Tobacco"|4995.00|2685.00|0.00|100289.00|3162.00|
|Food & Beverage Processing|0.00|0.00|141.00|0.00|0.00|
|Capital Goods|60190.00|93699.00|3505.00|6369.00|94949.00|
|Technology Hardware & Equipment|61100.00|167361.00|106157.00|1566.00|27592.00|
|Materials|200513.00|75678.00|0.00|88267.00|213137.00|
|Consumer Durables & Apparel|2867.00|3280.00|0.00|1162.00|0.00|
|"Textiles, Apparel, Footwear and Luxury Goods"|0.00|0.00|387.00|0.00|0.00|
|Software & Services|6.00|146.00|22856.00|22846.00|690.00|
|Chemicals|0.00|0.00|62369.00|0.00|0.00|
|Semiconductors & Semiconductor Equipment|0.00|50.00|0.00|4.00|0.00|
|Commercial & Professional Services|1157.00|477.00|0.00|2890.00|741.00|
|Retailing|0.00|19.00|11.00|0.00|0.00|
|Utilities|122.00|0.00|0.00|122.00|0.00|
|Gas Utilities|0.00|0.00|122.00|0.00|0.00|
|Telecommunication Services|52.00|183.00|183.00|0.00|0.00|
|Electrical Equipment and Machinery|0.00|0.00|9801558.00|0.00|0.00|
|Containers & Packaging|0.00|0.00|2988.00|0.00|0.00|
|"Mining - Iron, Aluminum, Other Metals"|0.00|0.00|8181.00|0.00|0.00|
|Media|9645.00|9645.00|1919.00|1808.00|0.00|
|Automobiles & Components|130189.00|230015.00|817227.00|1404833.00|0.00|
|"Pharmaceuticals, Biotechnology & Life Sciences"|32271.00|40215.00|0.00|0.00|0.00|
|Tires|0.00|0.00|2022.00|0.00|0.00|
|Trading Companies & Distributors and Commercial Services & Supplies|0.00|0.00|239.00|0.00|0.00|
|"Forest and Paper Products - Forestry, Timber, Pulp and Paper, Rubber"|0.00|0.00|8909.00|0.00|0.00|
|"Consumer Durables, Household and Personal Products"|0.00|0.00|931.00|0.00|0.00|
|Energy|750.00|0.00|0.00|10024.00|0.00|
|Food & Staples Retailing|0.00|773.00|706.00|2.00|0.00|
|Household & Personal Products|0.00|0.00|0.00|0.00|0.00|
|Tobacco|0.00|0.00|1.00|0.00|0.00|
|Semiconductors & Semiconductors Equipment|0.00|0.00|3.00|0.00|0.00|


![image](https://github.com/user-attachments/assets/df6dc8ac-ca50-44dc-844a-6014e60be919)

Result => The data shows irregular changes, with no consistent decrease.

Finished!






