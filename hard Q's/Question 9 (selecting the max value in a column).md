# R

```
# You can load libraries like dplyr if needed
library(dplyr)

# access your data
head(users)

users['activity_date_format'] <- as.Date(users$activity_date, format = '%m/%d/%Y')

users %>%
group_by(user_id) %>%
summarize(lastest_date = max(activity_date_format)) %>%
filter(lastest_date < '2022-01-01') %>%
arrange(user_id) %>%
select(user_id)
```

# Python

```

```

# MySQL

```

```
