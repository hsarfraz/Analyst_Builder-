

# R

```
# access your data
head(shop_custs)

#creating a new column which divides each row number iteration by 4
shop_custs['group_category'] = ((shop_custs$week - 1) / 4)

#since each row produced a decimal we can round the decimals up to a whole number and create row groups with the same group_id value
shop_custs['group_category'] = floor(shop_custs['group_category'])

df <- shop_custs %>%
group_by(group_category) %>%
summarise(total_customers = sum(customers))

df['four_week_total_avg'] = mean(df$total_customers)
df %>% select(four_week_total_avg) %>% head(1)
```

# Python

```
# access datasets as pandas dataframes
import pandas as pd

shop_custs.head()

shop_custs['groups'] = (shop_custs['week'] - 1) // 4

df = shop_custs.groupby('groups')['customers'].sum().reset_index()
df['average'] = df['customers'].mean()
df.loc[:,['average']].head(1)
```

# MySQL

```
WITH df AS(SELECT 
  SUM(customers) AS total
FROM shop_custs
GROUP BY FLOOR((week - 1) / 4))

SELECT AVG(total) AS average
FROM df
```
