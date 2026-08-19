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
# access datasets as pandas dataframes
import pandas as pd;

users.head()

users['date_convert'] = pd.to_datetime(users['activity_date'], format='%m/%d/%Y')

users = users.groupby('user_id')['date_convert'].max().reset_index(name='date')

users.loc[ (users['date'] < '2022-01-01') , ['user_id']].sort_values(by = 'user_id', ascending =True) 

```

# MySQL

```
WITH df AS(SELECT user_id,
  MAX(activity_date) AS date
FROM users
GROUP BY user_id)

SELECT user_id
FROM df 
WHERE date < '2022-01-01'
```
