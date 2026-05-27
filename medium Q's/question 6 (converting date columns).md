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

```

# MySQL

```

```
