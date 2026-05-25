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

```

# MySQL

```

```
