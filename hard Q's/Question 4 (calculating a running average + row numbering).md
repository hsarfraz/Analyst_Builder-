# What is a running average?

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

# R

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

# Python

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

# MySQL

```

```
