# Table of Contents

* [row counts of groups](#row-counts-of-groups)
* [removing duplicate rows then doing row counts](#removing-duplicate-rows-then-doing-row-counts)


## row counts of groups

* [table of contents](#table-of-contents)

## R

```
# You can load libraries like dplyr if needed

library(dplyr)

# access your data

head(help_requests)

help_requests %>%
mutate( completed_counts = ifelse(state == 'Completed', 1 , 0),
        inprogress_counts = ifelse(state != 'Completed', 1, 0)) %>%
group_by(type) %>%
summarise(total_completed_counts = sum(completed_counts),
          total_inprogress_counts = sum(inprogress_counts),
          complete_percentage = (sum(completed_counts) / n()) * 100
)
```

## Python

```
# access datasets as pandas dataframes
import pandas as pd
import numpy as np;

help_requests.head()

help_requests['completed_types'] = np.where(help_requests['state'] == 'Completed', 1, 0)
help_requests['non_completed_types'] = np.where(help_requests['state'] != 'Completed', 1, 0)

help_requests = help_requests.groupby('type').agg(
  total_completed_types = ('completed_types', 'sum'),
  total_non_completed_types = ('non_completed_types', 'sum'),
  total_counts_of_group_type = ('type', 'size')
).reset_index()

help_requests['complete_percent'] = (help_requests['total_completed_types'] / help_requests['total_counts_of_group_type']) * 100

help_requests.loc[:,['type', 'total_completed_types', 'total_non_completed_types', 'complete_percent']]
```

## MySQL

* `COUNT(*)` after the group_by counts all the rows in the dataframe

```
WITH df AS(SELECT *,
CASE WHEN state = 'Completed' THEN 1 ELSE 0 END AS received_mark,
CASE WHEN state != 'Completed' THEN 1 ELSE 0 END AS not_received_mark
FROM help_requests)
  
SELECT type,
  SUM(received_mark) AS total_received,
  SUM(not_received_mark) AS not_total_received,
  (SUM(received_mark) / COUNT(*)) * 100 AS received_percentage
FROM df 
GROUP BY type;
```

## PostgresSQL

* when doing division it is important to convert the denominator to a float

```
WITH df AS(SELECT *,
CASE WHEN state = 'Completed' THEN 1 ELSE 0 END AS received_mark,
CASE WHEN state != 'Completed' THEN 1 ELSE 0 END AS not_received_mark
FROM help_requests)
  
SELECT type,
  SUM(received_mark) AS total_received,
  SUM(not_received_mark) AS not_total_received,
  (SUM(received_mark) / COUNT(*)::float) * 100 AS received_percentage
FROM df 
GROUP BY type
ORDER BY type;
```

## MSSQL

* when doing division it is important to convert the denominator to a float

```
WITH df AS(SELECT *,
CASE WHEN state = 'Completed' THEN 1 ELSE 0 END AS received_mark,
CASE WHEN state != 'Completed' THEN 1 ELSE 0 END AS not_received_mark
FROM help_requests)
  
SELECT type,
  SUM(received_mark) AS total_received,
  SUM(not_received_mark) AS not_total_received,
  (SUM(received_mark) / CAST(COUNT(*) AS FLOAT)) * 100 AS received_percentage
FROM df 
GROUP BY type
ORDER BY type;
```

## removing duplicate rows then doing row counts

* [table of contents](#table-of-contents)

## R

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

## Python

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

## MySQL

```
WITH dataframe_to_merge AS (
  SELECT client_id, recruiter_name
  FROM client_assignments
),
  final_df AS (
  SELECT DISTINCT 
  df1.client_id,
  CASE WHEN df1.recruiter_name > df2.recruiter_name 
  THEN df2.recruiter_name ELSE df1.recruiter_name END AS recruiter_a,
  CASE WHEN df1.recruiter_name > df2.recruiter_name 
  THEN df1.recruiter_name ELSE df2.recruiter_name END AS recruiter_b
FROM client_assignments df1
LEFT JOIN dataframe_to_merge df2
  ON df1.client_id = df2.client_id 
WHERE (df1.recruiter_name != df2.recruiter_name)
  )
  
SELECT recruiter_a, recruiter_b, COUNT(*) AS common_clients
FROM final_df
GROUP BY recruiter_a, recruiter_B
ORDER BY common_clients DESC, recruiter_a ASC, recruiter_B ASC ;
```
