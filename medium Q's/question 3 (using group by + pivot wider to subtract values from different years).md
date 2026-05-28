# Question

# R

```
# You can load libraries like dplyr if needed
library(dplyr)
library(tidyr) # Needed for pivot_wider

# access your data
head(products)

products %>%
group_by(year, company_name) %>%
summarize(n = n(), .groups = 'drop') %>%
pivot_wider(names_from = year, values_from = n, values_fill = 0) %>%
mutate(difference = `2023` - `2022`) %>%
select(company_name, difference) %>%
arrange(company_name)
```

# Python

```
# access datasets as pandas dataframes
import pandas as pd;

products.head()

df = products.groupby(['year', 'company_name'])['product_name'].count().reset_index()
df = df.pivot_table(index='company_name', columns='year', values='product_name', fill_value=0).reset_index()
df['difference'] = df[2023] - df[2022]
df.loc[:,['company_name', 'difference']].sort_values(by='company_name',ascending=True)
```

# MySQL

```
SELECT company_name,
  SUM(CASE WHEN year = 2023 THEN 1 ELSE 0 END) - SUM(CASE WHEN year = 2022 THEN 1 ELSE 0 END) AS difference
FROM products
GROUP BY company_name
ORDER BY company_name ASC;
```
