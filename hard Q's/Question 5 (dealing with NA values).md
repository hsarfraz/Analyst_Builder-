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

```

# MySQL

```

```

