# R

```
# You can load libraries like dplyr if needed
library(dplyr)

# access your data
head(client_assignments)

client_assignments <- client_assignments %>% select(-assignment_date)

df <- left_join(client_assignments, client_assignments, by=('client_id' = 'client_id'))

# recruiter_name.x > recruiter_name.y is TRUE when string x comes after string y 
df %>%
mutate(
  recruiter_a = ifelse(recruiter_name.x > recruiter_name.y, recruiter_name.y, recruiter_name.x),
  recruiter_b = ifelse(recruiter_name.x > recruiter_name.y, recruiter_name.x, recruiter_name.y),
  delete_row = ifelse(recruiter_name.x == recruiter_name.y,1,0)) %>%
filter(delete_row != 1) %>% #to pick values that aren't equal to 1
distinct(client_id, recruiter_a, recruiter_b) %>% #removing repeating rows
group_by(recruiter_a, recruiter_b) %>%
summarise(count = n()) %>%
arrange(desc(count), recruiter_a, recruiter_b)
```

# Python

```
# access datasets as pandas dataframes
import pandas as pd
import numpy as np

client_assignments.head()

df = client_assignments.drop(columns=['assignment_date'])

df_merge = pd.merge(
  df,
  df,
  how='left',
  left_on='client_id',
  right_on='client_id'
)

df_merge = df_merge.loc[ (df_merge['recruiter_name_x'] != df_merge['recruiter_name_y']) ,:]
df_merge['recruiter_a'] = np.where(df_merge['recruiter_name_x'] > df_merge['recruiter_name_y'], df_merge['recruiter_name_y'], df_merge['recruiter_name_x'])
df_merge['recruiter_b'] = np.where(df_merge['recruiter_name_x'] > df_merge['recruiter_name_y'], df_merge['recruiter_name_x'], df_merge['recruiter_name_y'])

df_merge = df_merge.drop_duplicates(subset=['client_id', 'recruiter_a', 'recruiter_b'])
df_merge.groupby(['recruiter_a','recruiter_b'])['client_id'].count().reset_index().sort_values(by=['client_id','recruiter_a', 'recruiter_b'], ascending=[False, True, True])
```

# MySQL

```

```
