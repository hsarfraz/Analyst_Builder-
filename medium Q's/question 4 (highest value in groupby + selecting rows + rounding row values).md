# Question [link](https://www.analystbuilder.com/questions/biggest-country-debts-FVIGT)

The world is taking on debt like there is no tomorrow, but which country is taking on the most?

Write a query to find the top 3 countries with the largest national debt for the most recent year.

The output should have the columns Country and National_Debt (round to the nearest whole number), and should be ordered by National_Debt in descending order.

# R

```
# You can load libraries like dplyr if needed
library(dplyr)

# access your data
head(global_debts)

global_debts %>%
group_by(country) %>%
filter(year == max(year)) %>%
mutate(national_debt = round(national_debt)) %>%
arrange(desc(national_debt)) %>%
select(country, national_debt) %>%
head(3)
```

# Python

```
# access datasets as pandas dataframes
import pandas as pd;

global_debts.head()

# ['year'] says to get the max year only
# .idxmax() gets the index of each country with the highest year
max_year = global_debts.groupby('country')['year'].idxmax() 

global_debts.loc[max_year, ['country', 'national_debt']].round({'national_debt':0}).sort_values(by='national_debt', ascending=False).head(3)
```

# MySQL

```
SELECT country,
  ROUND(national_debt) AS national_debt
FROM global_debts as main
WHERE year = (
  SELECT MAX(year) 
  FROM global_debts as sub 
  WHERE main.country = sub.country)
ORDER BY national_debt DESC
LIMIT 3;
```

# MSSQL

```
SELECT TOP 3 country, 
  ROUND(CAST(national_debt AS Numeric),0) as national_debt
FROM global_debts AS main
WHERE year = (
  SELECT MAX(year) 
  FROM global_debts AS sub
  WHERE main.country = sub.country)
ORDER BY national_debt DESC;
```
