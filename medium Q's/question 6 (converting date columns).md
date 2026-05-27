# question

# R

```
# You can load libraries like dplyr if needed
library(dplyr)
library(tidyr)

# access your data
head(profits)
class(profits$date)

profits$date_format <- as.Date(profits$date, format = "%m/%d/%Y")

profits %>% 
  separate_wider_delim(
    cols = date, 
    delim = "/", 
    names = c("month", "day", "year")) %>%
mutate(month = as.numeric(month)) %>%
filter(date_format < "2024-07-01",
  date_format >= "2024-01-01") %>%
group_by(month) %>%
summarise(profit = sum(profit)) %>%
filter(profit > 0) %>%
arrange(desc(profit))
```

# python

```
import pandas as pd;

profits.head()

profits['date_convert'] = pd.to_datetime(profits['date'], format='%m/%d/%Y')
profits[['month', 'day', 'year']] = profits['date'].str.split('/', expand=True)
profits['month'] = pd.to_numeric(profits['month'])
profits = profits.loc[ (profits['date_convert'] < "2024-07-01") & (profits['date_convert'] >= "2024-01-01") ,:]
profits = profits.groupby('month')['profit'].sum().reset_index()
profits.loc[(profits['profit'] > 0),:].sort_values(by='profit', ascending = False)
```

# MySQL

```

```
