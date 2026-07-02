

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

```

# MySQL

```

```
