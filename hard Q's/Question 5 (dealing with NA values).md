# R

```
# You can load libraries like dplyr if needed
library(dplyr)

# access your data
head(missing_values)

missing_values %>%
mutate(average_excluding_nulls = mean(sale_amount, na.rm=TRUE),
       sale_amount_zero = ifelse(is.na(sale_amount), 0, sale_amount),
       sale_amount_min = ifelse(is.na(sale_amount), min(sale_amount, na.rm=TRUE), sale_amount),
       average_including_nulls = mean(sale_amount_zero),
       average_including_min = mean(sale_amount_min)                          
       ) %>%
select(average_excluding_nulls, average_including_nulls, average_including_min) %>%
head(1)
```

# Python

```
# access datasets as pandas dataframes
import pandas as pd
import numpy as np

missing_values.head()

missing_values['average_excluding_nulls'] = missing_values['sale_amount'].mean()

missing_values['sale_amount_zero'] = np.where(missing_values['sale_amount'].isna(), 0 , missing_values['sale_amount'])

missing_values['sale_amount_min'] = np.where(missing_values['sale_amount'].isna(), min(missing_values['sale_amount']), missing_values['sale_amount'])

missing_values['average_including_nulls'] = missing_values['sale_amount_zero'].mean()

missing_values['average_excluding_min'] = missing_values['sale_amount_min'].mean()

missing_values.loc[:,['average_excluding_nulls', 'average_including_nulls', 'average_excluding_min']].head(1)
```

# MySQL

```
WITH df AS(SELECT *,
  CASE WHEN (sale_amount is NULL) THEN 0 ELSE sale_amount END AS sale_amount_zero,
  CASE WHEN (sale_amount IS NULL) THEN 
  (SELECT MIN(sale_amount) FROM missing_values)
  ELSE sale_amount END AS sale_amount_min
FROM missing_values)

SELECT
  AVG(sale_amount) AS average_excluding_nulls,
  AVG(sale_amount_zero) AS average_including_nulls,
  AVG(sale_amount_min) AS average_including_min
FROM df;
```

