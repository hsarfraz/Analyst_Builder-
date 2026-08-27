# Table of Contents

* [running average](#running-average)
* [running total](#running-total)

## running average

A running average  is a statistical technique used to analyse data by continually updating the average as new points are added. The running average is used in finance, weather forecasts, and computer vision to smooth out data and highlight long-term trends.

$$
Running Average = \frac{Running Sum}{Running Count}
$$

Example:

| Sales Date | Daily Sales |
| -------- | -------- | 
| Jan 1  | 100  | 
| Jan 2  | 200  | 
| Jan 3  | 300  | 
| Jan 4  | 400  | 

| Sales Date | Running Average |
| -------- | -------- | 
| Jan 1  | $\frac{100}{1}$ = 100  | 
| Jan 2  | $\frac{100 + 200}{2}$ = 150  | 
| Jan 3  | $(100 + 200 + 300)/3$ = 200  | 
| Jan 4  | $(100 + 200 + 300 + 400)/4$ = 250  | 

## R

```
# You can load libraries like dplyr if needed
library(dplyr)

# access your data
head(sales_records)

sales_records$sale_date_format <- as.Date(sales_records$sale_date, format='%Y-%m-%d')

sales_records %>%
group_by(store_id) %>%
arrange(store_id, sale_date_format) %>%
mutate(row_num = row_number(),
       cumulative_sum = cumsum(daily_sales),
       running_average = cumulative_sum/row_num) %>%
select(sale_date, store_id, running_average)
```

## Python

```
# access datasets as pandas dataframes
import pandas as pd;

sales_records.head()
sales_records['sale_date_format'] = pd.to_datetime(sales_records['sale_date'], format = '%Y-%m-%d')

sales_records['row_num'] = (sales_records.sort_values(by=['store_id', 'sale_date_format'], ascending = [True, True]).groupby('store_id').cumcount()+1)

sales_records['cumulative_sum'] = (sales_records.sort_values(by=['store_id', 'sale_date_format'], ascending = [True, True]).groupby('store_id')['daily_sales'].cumsum())

sales_records['running_average'] = (sales_records['cumulative_sum']/sales_records['row_num'])

sales_records.loc[:,['sale_date_format', 'store_id', 'running_average']]
```

Here is code if you aren't using groupby() since cumcount() only works with groupby()

```
investment_property = investment_property.sort_values(
    by='purchase_price',
    ascending=True
)

investment_property['row_num'] = range(1, len(investment_property) + 1)

investment_property
```

## MySQL

**Note**: PARTITION is not a part of the SFJWGHOL SQL coding order of operations. PARTITION is like group by except it doesn't collapse rows 

```
WITH df AS (SELECT *,
 ROW_NUMBER() OVER (
  PARTITION BY store_id
  ORDER BY STR_TO_DATE(sale_date, '%Y-%m-%d')
 ) AS row_num,
 SUM(daily_sales) OVER (
  PARTITION BY store_id
  ORDER BY STR_TO_DATE(sale_date, '%Y-%m-%d')
 ) AS cumulative_sum
FROM sales_records)

SELECT sale_date, 
  store_id, 
  cumulative_sum/row_num AS running_average
FROM df
```

## running total

A running total is when you add the cumulative sum of a row based on the rows behind it. It does not need a rank number since rank number is used to calculate averages.

$$
Running total = Running Sum
$$

Example:

| Sales Date | Daily Sales |
| -------- | -------- | 
| Jan 1  | 100  | 
| Jan 2  | 200  | 
| Jan 3  | 300  | 
| Jan 4  | 400  | 

| Sales Date | Running Total |
| -------- | -------- | 
| Jan 1  | 100 | 
| Jan 2  | 100 + 200 = 300  | 
| Jan 3  | 100 + 200 + 300 = 600  | 
| Jan 4  | 100 + 200 + 300 + 400 = 1000  | 



## R

```
# You can load libraries like dplyr if needed
library(dplyr)

# access your data
head(investment_property)

investment_property %>%
arrange(purchase_price) %>%
mutate(profit_or_loss = estimated_sale_price - purchase_price,
       cumulative_profit_or_loss = cumsum(profit_or_loss)
       ) %>%
select(property_id, profit_or_loss, cumulative_profit_or_loss) 
```

## Python

```
# access datasets as pandas dataframes
import pandas as pd;

investment_property.head()

investment_property = investment_property.sort_values(by=['purchase_price'], ascending=True)

investment_property['profit_loss'] = investment_property['estimated_sale_price'] - investment_property['purchase_price']

investment_property['running_profit_loss'] = investment_property['profit_loss'].cumsum()

investment_property.loc[:,['property_id', 'profit_loss', 'running_profit_loss']]
```

## MySQL and PostgresSQL

* SUM() combined with OVER() + ORDER BY means cumulative sum or running average in MySQL
* PARTITION BY acts as a group by 

```
SELECT gender, dates, points,
  SUM(points) OVER(PARTITION BY gender ORDER BY gender ASC, dates ASC) AS running_total
FROM points;
```

## MSSQL

```
SELECT property_id,
  estimated_sale_price - purchase_price AS profit_loss,
  SUM(estimated_sale_price - purchase_price) OVER (ORDER BY purchase_price ASC) AS running_profit_loss
FROM investment_property
ORDER BY purchase_price ASC;
```
