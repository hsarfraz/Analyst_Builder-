# Question [link](https://www.analystbuilder.com/questions/shrink-flation-ohNJw)

# R

```
# You can load libraries like dplyr if needed
library(dplyr)

# access your data
head(products)

products %>%
mutate(size_reduction = ifelse(original_size > new_size, 1, 0),
       price_increase = ifelse(original_price <= new_price, 1, 0),
       size_change_percentage = round(((new_size - original_size)/original_size) * 100),
       price_change_percentage = round(((new_price - original_price)/original_price) * 100),
       shrinkflation_flag = ifelse(size_reduction == 1 & price_increase == 1, 'True', 'False')) %>%
select(product_name, size_change_percentage, price_change_percentage, shrinkflation_flag) %>%
arrange(product_name)
```

# Python

```
# access datasets as pandas dataframes
import pandas as pd;

products.head()

products['product_reduction'] = (products['original_size'] > products['new_size'])

products['price_increase'] = (products['original_price'] <= products['new_price'])

products['size_change_percentage'] = round(((products['new_size'] - products['original_size'])/products['original_size']) * 100, 0)

products['price_change_percentage'] = round(((products['new_price'] - products['original_price'])/products['original_price']) * 100, 0)

products['shrinkflation_flag'] = products['product_reduction'] & products['price_increase']
products['shrinkflation_flag'] = products['shrinkflation_flag'].map({True: 'True', False: 'False'})

products.loc[:,['product_name', 'size_change_percentage', 'price_change_percentage', 'shrinkflation_flag']].sort_values(by='product_name', ascending=True)
```

# MySQL

```
SELECT product_name,
  ROUND(((new_size - original_size)/original_size) * 100) AS size_change_percentage,
  ROUND(((new_price - original_price)/original_price) * 100) AS price_change_percentage,
  IF((original_size > new_size) & (original_price <= new_price), 'True', 'False') AS shrinkflation_flag 
FROM products 
ORDER BY product_name;
```
