# Question [link](https://www.analystbuilder.com/questions/calculator-sales-PzCXW)

# R

```
# You can load libraries like dplyr if needed
library(dplyr)

# access your data
head(calculators)

df <- calculators %>%
group_by(year) %>%
transmute(sales_yearly_total = sum(calculator_sales)) 

calcs_2000 <- df %>% filter(year == 2000)
calcs_2023 <- df %>% filter(year == 2023)

round(((calcs_2023['sales_yearly_total'] - calcs_2000['sales_yearly_total']) / calcs_2000['sales_yearly_total']) * 100, 2)
```

# Python

```
# access datasets as pandas dataframes
import pandas as pd;

calculators.head()

df = calculators.groupby('year').sum().reset_index()

sales_2000 = df.loc[ df['year'] == 2000 , ['calculator_sales']].values[0]
sales_2023 = df.loc[ df['year'] == 2023 , ['calculator_sales']].values[0]

percent_change = (((sales_2023[0] - sales_2000[0])/sales_2000[0]) * 100).round(2)
pd.DataFrame(
  {'Percent Change' : [percent_change]}
)
```

# MySQL

```
SELECT
  ROUND(
  (
  ((SUM(CASE WHEN year = 2023 THEN calculator_sales ELSE 0 END)) - 
  SUM(CASE WHEN year = 2000 THEN calculator_sales ELSE 0 END)) /
  (SUM(CASE WHEN year = 2000 THEN calculator_sales ELSE 0 END))
  ) * 100, 2) AS percent_growth
FROM calculators;
```
