# R

```
# You can load libraries like dplyr if needed
library(dplyr)

# access your data
head(facebook)
library(stringr)

facebook %>%
mutate(christmas = ifelse(str_detect(dates, '12/25'), 1, 0)) %>%
filter(christmas == 1) %>%
group_by(actions) %>%
summarise(actions_count = n()) %>%
mutate(ranks = min_rank(desc(actions_count))) %>%
arrange(ranks, actions)
```

# Python

```
# access datasets as pandas dataframes
import pandas as pd
import numpy as np;

facebook.head()

facebook['christmas'] = np.where(facebook['dates'].str.contains('12/25'), 1, 0)

facebook = facebook.loc[facebook['christmas'] == 1,:]
facebook = facebook.groupby('actions')['actions'].count().reset_index(name='count')
facebook['ranks'] = facebook['count'].rank(method='min', ascending=False)
facebook.loc[:,:].sort_values(by=['ranks', 'actions'], ascending = [True, True])
```

# MYSQL

```
SELECT actions,
  COUNT(actions) AS counts,
  RANK() OVER (ORDER BY COUNT(actions) desc) AS ranks
FROM facebook
WHERE (STR_TO_DATE(dates, '%Y-%m-%d') = '2023-12-25')
GROUP BY actions
ORDER BY ranks ASC, actions ASC;
```
