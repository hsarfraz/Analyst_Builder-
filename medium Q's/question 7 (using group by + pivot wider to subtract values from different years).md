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

```

# MySQL

```

```
